---
title: "Escala Distribuída Geograficamente com Ambientes de Serviço de Aplicações"
description: "Saiba como escalar horizontalmente aplicações ao utilizar a georreplicação distribuição com ambientes do App Service e o Traffic Manager."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.openlocfilehash: 21f747239e565aba79a84c8e946a71e306b64968
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Escala Distribuída Geograficamente com Ambientes de Serviço de Aplicações
## <a name="overview"></a>Descrição geral
Cenários de aplicações que requerem muito grande escala podem exceder a capacidade de recursos de computação disponível para uma única implementação de uma aplicação.  Aplicações de voto, sporting eventos e os eventos de entretenimento televised são todos os exemplos de cenários que necessitam de escala extremamente elevada. Requisitos de escala elevada possam ser satisfeitos pelo horizontalmente aumentar horizontalmente a aplicações, com várias implementações de aplicação que está a ser efetuadas numa única região, bem como em regiões, para lidar com requisitos de carregamento extremos.

Os ambientes de serviço de aplicações são uma plataforma ideal para Escalamento horizontal.  Uma vez uma aplicação ambiente de serviço de configuração tiver sido selecionada que pode suportar uma taxa de pedidos conhecido, os programadores podem implementar adicionais ambientes do App Service de forma "cookie cutter" para obter uma capacidade de carregamento das horas de ponta pretendido.

Por exemplo, suponha uma aplicação em execução numa configuração de ambiente de serviço de aplicações foi testada para processar pedidos de 20K por segundo (RPS).  Se a capacidade de carga de pico pretendido for 100K RPS, em seguida, cinco (5) ambientes do App Service pode ser criados e configurados para garantir que a aplicação pode processar a carga prevista máxima.

Uma vez que os clientes, normalmente, aceder a aplicações ao utilizar um domínio personalizado (ou intuitivos), os programadores precisam de uma forma para distribuir pedidos de aplicação em todas as instâncias de ambiente de serviço de aplicações.  É uma excelente forma de realizar esta tarefa para resolver o domínio personalizado utilizando um [perfil do Traffic Manager do Azure][AzureTrafficManagerProfile].  O perfil do Gestor de tráfego pode ser configurado para apontar para todos os ambientes de serviço de aplicações individuais.  Gestor de tráfego processará automaticamente os clientes de distribuição em todos os ambientes de serviço de aplicações com base na definições no perfil do Gestor de tráfego de balanceamento de carga.  Esta abordagem funciona independentemente se todos os ambientes de serviço de aplicações são implementados numa única região do Azure ou em todo o mundo implementados em várias regiões do Azure.

Além disso, uma vez que os clientes aceder a aplicações através do domínio intuitivos, os clientes são não tem conhecimento do número de ambientes de serviço de aplicações em execução uma aplicação.  Como resultado os programadores podem rápida e facilmente adicionar e remover, ambientes do App Service, com base na carga de tráfego observado.

O diagrama conceptual abaixo ilustra uma aplicação horizontalmente ampliada em três ambientes do App Service numa única região.

![Arquitetura conceptual][ConceptualArchitecture] 

O resto deste tópico explica os passos envolvidos configurar uma topologia distribuída para a aplicação de exemplo utilizando vários ambientes do App Service.

## <a name="planning-the-topology"></a>Planear a topologia
Antes de conceção de requisitos de espaço de aplicação distribuída, ajuda a ter alguns informações de peças antecedência.

* **Domínio personalizado para a aplicação:** qual é o nome de domínio personalizado que os clientes utilizarão para aceder à aplicação?  Para a aplicação de exemplo é o nome de domínio personalizado *www.scalableasedemo.com*
* **Domínio do Traffic Manager:** um nome de domínio tem de ser selecionado quando criar um [perfil do Traffic Manager do Azure][AzureTrafficManagerProfile].  Este nome será ser combinado com o *trafficmanager.net* sufixo para registar uma entrada de domínio que é gerida pelo Gestor de tráfego.  Para a aplicação de exemplo, é escolhido o nome *demonstração dimensionável ase*.  Como resultado, o nome de domínio completo que é gerido pelo Gestor de tráfego está *demo.trafficmanager.net dimensionável ase*.
* **Estratégia de dimensionamento os requisitos de espaço de aplicação:** os requisitos de espaço da aplicação serem distribuídos pelos vários ambientes do App Service numa única região?  Várias regiões?  Uma combinação-e-correspondência de ambas as abordagens?  A decisão baseia-se no expectativas onde o tráfego de cliente será provir, bem como também pode dimensionar o resto da infraestrutura de back-end de suporte de uma aplicação.  Por exemplo, com uma aplicação sem monitorização de estado de 100%, uma aplicação pode ser extremamente escalada utilizando uma combinação de vários ambientes do App Service por região do Azure, multiplicado pelo ambientes do App Service implementado em várias regiões do Azure.  Com 15 + públicas regiões do Azure disponíveis para escolha, os clientes verdadeiramente podem criar requisitos de espaço de aplicação de hiper escala em todo o mundo.  Para a aplicação de exemplo utilizada para este artigo, três ambientes do App Service foram criados numa única região do Azure (Sul Central US).
* **Convenção de nomenclatura para os ambientes do App Service:** cada ambiente de serviço de aplicações requer um nome exclusivo.  Para além de um ou dois ambientes do App Service, será útil ter uma convenção de nomenclatura para ajudar a identificar cada ambiente de serviço de aplicações.  Para a aplicação de exemplo foi utilizada uma convenção de nomenclatura simple.  Os nomes dos ambientes de serviço de aplicações três forem *fe1ase*, *fe2ase*, e *fe3ase*.
* **Convenção de nomenclatura para as aplicações:** uma vez que serão implementadas várias instâncias da aplicação, um nome é necessária para cada instância da aplicação implementada.  Uma funcionalidade reduzida conhecido mas muito conveniente de ambientes do App Service é que o mesmo nome de aplicação pode ser utilizado em vários ambientes do App Service.  Uma vez que cada ambiente de serviço de aplicações têm um sufixo de domínio único, os programadores podem optar por utilizar novamente o mesmo nome de aplicação em cada ambiente.  Por exemplo um programador pode ter aplicações com o nome da seguinte forma: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc.  Apesar da aplicação de exemplo cada instância da aplicação também tem um nome exclusivo.  Os nomes de instância da aplicação utilizados são *webfrontend1*, *webfrontend2*, e *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Configurar o perfil do Gestor de tráfego
Assim que forem implementadas várias instâncias de uma aplicação em vários ambientes do App Service, as instâncias de aplicações individuais podem ser registadas com o Gestor de tráfego.  Para a aplicação de exemplo de um Gestor de tráfego de perfil é necessária para *demo.trafficmanager.net dimensionável ase* que pode encaminhar os clientes para qualquer das instâncias da aplicação implementada seguintes:

* **webfrontend1.fe1ase.p.azurewebsites.NET:** uma instância da aplicação de exemplo implementada no ambiente de serviço de aplicações primeiro.
* **webfrontend2.fe2ase.p.azurewebsites.NET:** uma instância da aplicação de exemplo implementada no ambiente de serviço de aplicação de segundo.
* **webfrontend3.fe3ase.p.azurewebsites.NET:** uma instância da aplicação de exemplo implementada no ambiente de serviço de aplicações terceiro.

A forma mais fácil para registar múltiplos App Service do Azure pontos finais, todos os em execução no **mesmo** região do Azure, é com o Powershell [suporte do Gestor de tráfego do Azure Resource Manager][ARMTrafficManager].  

O primeiro passo é criar um perfil do Traffic Manager do Azure.  O código abaixo mostra como o perfil foi criado para a aplicação de exemplo:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Repare como o *RelativeDnsName* parâmetro foi definido como *demonstração dimensionável ase*.  Trata-se como o nome de domínio *demo.trafficmanager.net dimensionável ase* são criados e associados a um perfil do Traffic Manager.

O *TrafficRoutingMethod* parâmetro define a política do Traffic Manager irá utilizar para determinar como distribuir a carga de cliente em todos os pontos finais disponíveis de balanceamento de carga.  Neste exemplo de *Weighted* método escolhido.  Isto irá resultar em pedidos de cliente que está a ser distribuídos por todos os pontos finais de aplicação registada com base em ponderações relativas associadas a cada ponto final. 

Com o perfil que criou, cada instância da aplicação é adicionada ao perfil como um ponto final do Azure nativo.  O código abaixo obtém uma referência para cada aplicação web de front-end e, em seguida, adiciona cada aplicação como um ponto final do Gestor de tráfego por way do *TargetResourceId* parâmetro.

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Repare como se houver uma chamada de *adicionar AzureTrafficManagerEndpointConfig* para cada instância de aplicações individuais.  O *TargetResourceId* parâmetro em cada comando do Powershell faz referência a uma das três instâncias de aplicação implementada.  O perfil do Gestor de tráfego serão distribuídos carga por todos os pontos três finais registados no perfil.

Todos os pontos finais três utilizam o mesmo valor (10) para o *ponderação* parâmetro.  Isto resulta no Gestor de tráfego pedidos de cliente propagando-se em todas as instâncias da aplicação de três relativamente uniformemente. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Apontar o domínio personalizado da aplicação no domínio do Traffic Manager
É o último passo necessário para apontar o domínio personalizado da aplicação no domínio do Traffic Manager.  Para a aplicação de exemplo significa apontar *www.scalableasedemo.com* em *demo.trafficmanager.net dimensionável ase*.  Este passo tem de ser concluído com a entidade de registo do domínio que gere o domínio personalizado.  

Utilizar as ferramentas de gestão do domínio sua entidade de registo, um CNAME regista tem de ser criados pontos que o domínio personalizado no domínio do Traffic Manager.  A imagem abaixo mostra um exemplo do aspeto esta configuração de CNAME:

![CNAME para o domínio personalizado][CNAMEforCustomDomain] 

Apesar de não abrangidas neste tópico, lembre-se de que cada instância de aplicações individuais tem de ter o domínio personalizado registado com o mesmo assim.  Caso contrário, se um pedido faz com que uma instância de aplicação e a aplicação não tem o domínio personalizado registado com a aplicação, o pedido irá falhar.  

Neste exemplo é o domínio personalizado *www.scalableasedemo.com*, e cada instância da aplicação tem o domínio personalizado com associados.

![Domínio Personalizado][CustomDomain] 

Para um resumo de registar um domínio personalizado com aplicações do App Service do Azure, consulte o seguinte artigo no [registar domínios personalizados][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Experimentar a topologia distribuída
O resultado final da configuração do Gestor de tráfego e o DNS é que os pedidos para *www.scalableasedemo.com* irá fluir através da seguinte sequência:

1. Um browser ou o dispositivo irá efetuar uma pesquisa de DNS *www.scalableasedemo.com*
2. A entrada CNAME na entidade de registo de domínio faz com que a pesquisa de DNS ser redirecionado para o Gestor de tráfego do Azure.
3. É efetuada uma pesquisa de DNS *demo.trafficmanager.net dimensionável ase* contra um dos servidores DNS de Gestor de tráfego do Azure.
4. Com base na política de balanceamento de carga (o *TrafficRoutingMethod* parâmetro utilizado anteriormente ao criar o perfil do Gestor de tráfego), Gestor de tráfego será selecione um dos pontos finais configurados e devolver o FQDN desse ponto final para o browser ou o dispositivo.
5. Uma vez que o FQDN do ponto final do Url de uma instância de aplicação em execução no ambiente de serviço de aplicações, o browser ou o dispositivo irá perguntar-um servidor de DNS do Azure da Microsoft para resolver o FQDN para um endereço IP. 
6. O browser ou o dispositivo irá enviar o pedido HTTP/S para o endereço IP.  
7. O pedido será chegam a uma das instâncias de aplicações em execução dos ambientes do App Service.

A imagem de consola abaixo mostra uma pesquisa DNS para o domínio personalizado a aplicação de exemplo com êxito a resolver para uma instância de aplicação em execução da três exemplo ambientes do App Service (neste caso, o segundo de ambientes de serviço de aplicações três):

![Pesquisa de DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Informações e hiperligações adicionais
A documentação sobre o Powershell [suporte do Gestor de tráfego do Azure Resource Manager][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
