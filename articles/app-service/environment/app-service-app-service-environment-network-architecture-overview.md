---
title: "Descrição Geral da Arquitetura de Rede de Ambientes de Serviço de Aplicações"
description: "Descrição geral da arquitetura de topologia de rede ofApp ambientes de serviço."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: 3362a55524da42914681db06b8d2c0da8df773d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Descrição Geral da Arquitetura de Rede de Ambientes de Serviço de Aplicações
## <a name="introduction"></a>Introdução
Ambientes do App Service sempre são criados dentro de uma sub-rede de um [rede virtual] [ virtualnetwork] -aplicações em execução num ambiente de serviço de aplicações podem comunicar com pontos finais privados localizados na mesmo topologia de rede virtual.  Uma vez que os clientes podem bloquear partes da respetiva infraestrutura de rede virtual, é importante compreender os tipos de fluxos de comunicação de rede que ocorre com um ambiente de serviço de aplicações.

## <a name="general-network-flow"></a>Fluxo de gerais de rede
Quando a aplicação serviço de ambiente (ASE) utiliza um endereço IP virtual público (VIP) para aplicações, todo o tráfego de entrada chegar a esse esse VIP público.  Isto inclui o tráfego HTTP e HTTPS para aplicações, bem como outro tráfego para operações de gestão do Azure, funcionalidades de depuração remota e FTP.  Para uma lista completa das portas específicas (obrigatórios e opcionais) que estão disponíveis nos público VIP, consulte o artigo em [controlar o tráfego de entrada] [ controllinginboundtraffic] para um ambiente de serviço de aplicações. 

Ambientes do App Service também suportam aplicações em execução que estão vinculadas apenas para um endereço interno da rede virtual, também referido como um endereço do ILB (Balanceador de carga interno).  Num ILB tráfego ASE, HTTP e HTTPS ativado para aplicações, bem como as chamadas remotas de depuração, chegam no endereço ILB.  Para configurações mais comuns do ILB ASE, o tráfego FTP/FTPS será também chegam ao endereço do ILB.  No entanto a operações de gestão do Azure ainda irão fluir para o VIP público de um ILB de portas 454/455 ativada ASE.

O diagrama abaixo mostra uma descrição geral dos fluxos de rede de entrada e saída de vários para um ambiente de serviço de aplicações em que as aplicações estão vinculadas a um endereço IP virtual público:

![Fluxos de rede gerais][GeneralNetworkFlows]

Um ambiente de serviço de aplicações podem comunicar com uma variedade de pontos finais de cliente privada.  Por exemplo, aplicações em execução no ambiente de serviço de aplicações podem ligar a servidores de base de dados em execução no IaaS máquinas de virtuais na mesma topologia de rede virtual.

> [!IMPORTANT]
> "Outros computação recursos" observar o diagrama de rede, são implementados numa sub-rede diferente do ambiente de serviço de aplicações. A implementar recursos na mesma sub-rede com o ASE irá bloquear a conectividade do ASE a esses recursos (exceto específico intra-ASE encaminhamento). Implemente uma sub-rede diferente em vez disso, (na mesma VNET). O ambiente de serviço de aplicações, em seguida, será capaz de ligar. É necessária nenhuma configuração adicional.
> 
> 

Ambientes de serviço de aplicações comunicam também com base de dados Sql e Storage do Azure recursos necessários para gerir e funcionamento do ambiente de serviço de aplicações.  Alguns dos recursos de armazenamento e SQL Server com que comunica um ambiente de serviço de aplicações estão localizados na mesma região que o ambiente de serviço de aplicações, enquanto outros estão localizados em regiões do Azure remotas.  Como resultado, conectividade à Internet de saída é sempre necessária para um ambiente de serviço de aplicações funcionar corretamente. 

Uma vez que um ambiente de serviço de aplicação é implementado numa sub-rede, grupos de segurança de rede podem ser utilizados para controlar o tráfego de entrada para a sub-rede.  Para obter detalhes sobre como controlar o tráfego de entrada para um ambiente de serviço de aplicações, consulte o seguinte [artigo][controllinginboundtraffic].

Para obter detalhes sobre como permitir que a ligação à Internet de saída de um ambiente de serviço de aplicações, consulte o seguinte artigo sobre como trabalhar com [Express Route][ExpressRoute].  A mesma abordagem descrita no artigo aplica-se ao trabalhar com conetividade Site a Site e utilizar imposição do túnel.

## <a name="outbound-network-addresses"></a>Endereços de rede de saída
Quando um ambiente de serviço de aplicações efetua chamadas de saída, um endereço IP está sempre associado com as chamadas de saída.  O endereço IP específico que é utilizado depende se o ponto final que está a ser chamado está localizado dentro da topologia da rede virtual, ou no exterior da topologia da rede virtual.

Se o ponto final que está a ser chamado **fora** da topologia de rede virtual, em seguida, o endereço de saída (também conhecido como o endereço NAT saído) que é utilizado é o VIP público de ambiente de serviço de aplicações.  Este endereço pode ser encontrado na interface do portal de utilizador para o ambiente de serviço de aplicações no painel de propriedades.

![Endereço IP de saída][OutboundIPAddress]

Este endereço também pode ser determinado para ASEs que têm apenas um VIP público ao criar uma aplicação no ambiente de serviço de aplicações e, em seguida, efetuar uma *nslookup* no endereço da aplicação. O endereço IP resultante é tanto o VIP público, bem como endereço NAT de saída do ambiente de serviço de aplicações.

Se o ponto final que está a ser chamado **dentro** da topologia de rede virtual, o endereço de saída da aplicação chamar será o endereço IP do recurso de computação individuais executar a aplicação.  No entanto não é um mapeamento de endereços IP internos de rede virtual para aplicações persistente.  As aplicações podem mover-se em recursos de computação diferentes e o conjunto de recursos num ambiente de serviço de aplicações podem alterar devido a operações de dimensionamento de computação disponíveis.

No entanto, uma vez que um ambiente de serviço de aplicações sempre está localizado numa sub-rede, são garantido que o endereço IP de um recurso de computação com uma aplicação será sempre se situam dentro do intervalo de CIDR da sub-rede.  Como resultado, quando ACLs detalhadas ou grupos de segurança de rede são utilizados para proteger o acesso a outros pontos finais dentro da rede virtual, o intervalo de sub-rede que contém o ambiente de serviço de aplicações tem de ser concedido acesso.

O diagrama seguinte mostra estes conceitos mais detalhadamente:

![Endereços de rede de saída][OutboundNetworkAddresses]

No diagrama acima:

* Uma vez que o VIP público de ambiente de serviço de aplicações 192.23.1.2, que é o endereço IP de saída utilizado ao efetuar chamadas de pontos finais "Internet".
* O intervalo CIDR da sub-rede para o ambiente de serviço de aplicações que contêm é 10.0.1.0/26.  Outros pontos finais dentro da mesma infraestrutura de rede virtual Verão chamadas a partir de aplicações como sendo proveniente de algures dentro deste intervalo de endereços.

## <a name="calls-between-app-service-environments"></a>Chamadas entre ambientes do App Service
Um cenário mais complexo pode ocorrer se implementar vários ambientes do App Service na mesma rede virtual e, efetuar chamadas de saída de um ambiente de serviço de aplicação para outro ambiente de serviço de aplicações.  Estes tipos de se estender o ambiente de serviço de aplicações chamadas também serão tratadas como chamadas de "Internet".

O diagrama seguinte mostra um exemplo de uma arquitetura em camadas com aplicações num ambiente de serviço de aplicações (por exemplo "Porta da frente" aplicações web) a chamada de aplicações num segundo ambiente de serviço de aplicações (por exemplo, internas aplicações de API de back-end não se destina a ser acessível a partir da Internet). 

![Chamadas entre ambientes do App Service][CallsBetweenAppServiceEnvironments] 

No exemplo acima, o ambiente de serviço de aplicações "ASE um" tem um endereço IP saído 192.23.1.2.  Se uma aplicação em execução no ambiente de serviço de aplicações faz com que uma chamada de saída para uma aplicação em execução num ambiente de serviço de aplicações segundo ("ASE dois"), localizada na mesma rede virtual, a chamada de saída serão tratadas como uma chamada de "Internet".  Como resultado o tráfego de rede que estão a chegar a segunda ambiente de serviço de aplicações mostrará como sendo proveniente de 192.23.1.2 (ou seja, não o sub-rede intervalo de endereços de ambiente de serviço de aplicações primeiro).

Apesar de chamadas entre diferentes ambientes do App Service são tratadas como chamadas de "Internet", quando ambos os ambientes de serviço de aplicações estão localizados na mesma região do Azure, o tráfego de rede irá permanecer na rede Azure regional e fisicamente não irá fluir através da Internet pública.  Como resultado pode utilizar um grupo de segurança de rede na sub-rede da segunda ambiente de serviço de aplicações para permitir apenas entradas chamadas a partir do ambiente de serviço de aplicações primeiro (cujo endereço IP de saída é 192.23.1.2), que garante uma comunicação segura entre os ambientes do App Service.

## <a name="additional-links-and-information"></a>Informações e hiperligações adicionais
Detalhes sobre as portas utilizadas pelo ambientes do App Service de entrada e utilizar grupos de segurança de rede para controlar o tráfego de entrada está disponível [aqui][controllinginboundtraffic].

Detalhes sobre a utilização do utilizador definida rotas para conceder acesso de Internet de saída para ambientes do App Service está disponível neste [artigo][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

