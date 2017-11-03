---
title: "Ligar em segurança a recursos de back-end, a partir de um ambiente de serviço de aplicações"
description: "Saiba mais sobre como ligar de forma segura a recursos de back-end de um ambiente de serviço de aplicações."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: a43d88d64710b95dd753c19f02582f22accac8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Ligar em segurança a recursos de back-end, a partir de um ambiente de serviço de aplicações
## <a name="overview"></a>Descrição geral
Uma vez que um ambiente de serviço de aplicações é sempre criado no **ou** uma rede virtual do Azure Resource Manager, **ou** um modelo de implementação clássica [rede virtual] [ virtualnetwork], ligações de saída a partir de um ambiente de serviço de aplicação para outros recursos de back-end possam circular exclusivamente através da rede virtual.  Com uma alteração recente efetuada em Junho de 2016, ASEs também podem ser implementados em redes virtuais que utilizam os intervalos de endereços público ou espaços de endereços RFC1918 (ou seja, endereços privados).  

Por exemplo, poderá ser um SQL Server em execução num cluster de máquinas virtuais com a porta 1433 bloqueados.  O ponto final pode ser ACLd para permitir apenas o acesso a partir de outros recursos na mesma rede virtual.  

Outro exemplo, pontos finais confidenciais poderá ser executado no local e a ser ligados ao Azure através de um [Site a Site] [ SiteToSite] ou [Azure ExpressRoute] [ ExpressRoute] ligações.  Como resultado, apenas os recursos na redes virtuais ligadas ao Site a Site ou ExpressRoute túneis conseguirá aceder aos pontos finais de no local.

Para todos estes cenários, as aplicações em execução no ambiente de serviço de aplicações será capazes de ligar de forma segura os vários servidores e recursos.  Tráfego de saída de aplicações em execução num ambiente de serviço de aplicações para os pontos finais privados na mesma rede virtual (ou estiver ligado à mesma rede virtual), será apenas fluxo através da rede virtual.  Tráfego de saída para pontos finais privados não irá fluir através da Internet pública.

Uma advertência aplica-se ao tráfego de saída de um ambiente de serviço de aplicações para os pontos finais dentro de uma rede virtual.  Ambientes do App Service não é possível aceder a pontos finais de máquinas virtuais localizadas no **mesmo** sub-rede que o ambiente de serviço de aplicações.  Isto normalmente não deve ser um problema, desde que os ambientes de serviço de aplicações são implementados para uma sub-rede reservada para utilização exclusiva por apenas o ambiente de serviço de aplicações.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de saída e os requisitos de DNS
Para um ambiente de serviço de aplicações funcionar corretamente, requer acesso a vários pontos finais de saída. Uma lista completa dos pontos finais externos utilizados pelo ASE está na secção "Necessárias conectividade de rede" o [configuração de rede para o ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artigo.

Ambientes do App Service necessitam de uma infraestrutura de DNS válida configurada para a rede virtual.  Se por qualquer motivo, a configuração de DNS é alterada depois de criar um ambiente de serviço de aplicações, os programadores de podem forçar um ambiente de serviço de aplicações para recolher a nova configuração de DNS.  Acionar um reinício de ambiente graduais utilizando o ícone de "Restart" localizado na parte superior do painel de gestão do ambiente de serviço de aplicações no portal do fará com que o ambiente recolher a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet ser configurado antes de tempo antes de criar um ambiente de serviço de aplicações.  Se a configuração de DNS de uma rede virtual é alterada durante a criação de um ambiente de serviço de aplicações, que resultará na falha de processo de criação do ambiente de serviço de aplicações.  Num vein semelhante, se um servidor DNS personalizado existe na outra extremidade de um gateway de VPN e o servidor DNS está inacessível ou não está disponível, o processo de criação do ambiente de serviço de aplicações também falharão.

## <a name="connecting-to-a-sql-server"></a>Ligar a um servidor SQL
Uma configuração comum do SQL Server tem um ponto final à escuta na porta 1433:

![Ponto final do SQL Server][SqlServerEndpoint]

Existem duas abordagens para restringir o tráfego para este ponto final:

* [Listas de controlo de acesso de rede] [ NetworkAccessControlLists] (ACLs de rede)
* [Grupos de segurança de rede][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Restringir o acesso com uma ACL de rede
A porta 1433 pode ser protegida utilizando uma lista de controlo de acesso de rede.  O exemplo abaixo whitelists cliente endereços originadas a partir de dentro de uma rede virtual e bloqueia o acesso a todos os outros clientes.

![Exemplo de lista de controlo de acesso de rede][NetworkAccessControlListExample]

Todas as aplicações em execução no ambiente de serviço de aplicações na mesma rede virtual como o SQL Server, poderão ligar para a instância do SQL Server utilizando o **VNet interno** endereço IP para a máquina virtual do SQL Server.  

A cadeia de ligação de exemplo abaixo referencia o SQL Server utilizando o respetivo endereço IP privado.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Apesar da máquina virtual possui um ponto de final público bem, as tentativas de ligação com o endereço IP público serão rejeitadas devido a ACL de rede. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringir o acesso a um grupo de segurança de rede
É uma abordagem alternativa para proteger o acesso a um grupo de segurança de rede.  Grupos de segurança de rede podem ser aplicados a máquinas virtuais individuais, ou a uma sub-rede que contém máquinas virtuais.

Primeiro um grupo de segurança de rede tem de ser criada:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir o acesso a apenas VNet interno o tráfego é muito simple com um grupo de segurança de rede.  As regras predefinidas de um grupo de segurança de rede apenas permitem o acesso a partir de outros clientes de rede na mesma rede virtual.

Como resultado bloquear acesso ao SQL Server é tão simple como aplicar um grupo de segurança de rede com o respetivos regras predefinidas para as máquinas de virtuais a executar o SQL Server ou a sub-rede que contém as máquinas virtuais.

O exemplo abaixo aplica-se um grupo de segurança de rede para a sub-rede do contentor:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

O resultado final é um conjunto de regras de segurança que bloquear o acesso externo, ao permitir o acesso interno da VNet:

![Regras de segurança de rede predefinida][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introdução
Para começar com ambientes do App Service, consulte o artigo [introdução ao ambiente de serviço de aplicações][IntroToAppServiceEnvironment]

Para obter mais detalhes em torno de controlar o tráfego de entrada para o ambiente de serviço de aplicações, consulte [controlar o tráfego de entrada para um ambiente de serviço de aplicações][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
