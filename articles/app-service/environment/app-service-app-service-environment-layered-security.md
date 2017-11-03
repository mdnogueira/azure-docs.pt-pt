---
title: "Arquitetura de segurança em camadas com ambientes do App Service"
description: "Implementar uma arquitetura de segurança em camadas com ambientes do App Service."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.openlocfilehash: 6c1f62b5e10a625911feea17ae6835e027709790
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementar uma arquitetura de segurança em camadas com ambientes do App Service
## <a name="overview"></a>Descrição geral
Uma vez que os ambientes do App Service fornece um ambiente de tempo de execução isolada implementado para uma rede virtual, os programadores podem criar uma arquitetura de segurança por camadas fornecer diferentes níveis de acesso de rede para cada camada físico da aplicação.

É um desejo comuns para ocultar API back-ends de geral acesso à Internet e permitir apenas APIs chamado através de aplicações web a montante.  [Grupos de segurança (NSGs) de rede] [ NetworkSecurityGroups] podem ser utilizados em sub-redes que contém os ambientes do App Service para restringir o acesso público ao aplicações API.

O diagrama abaixo mostra uma arquitetura de exemplo com uma aplicação de end WebAPI com base implementada num ambiente de serviço de aplicações.  Três instâncias de aplicações web separado, implementadas em três separado ambientes do App Service, efetuar chamadas de back-end para a mesma aplicação de end WebAPI.

![Arquitetura conceptual][ConceptualArchitecture] 

Sinais de plus verdes indica que o grupo de segurança de rede na sub-rede que contém "apiase" permite chamadas de entrada a partir de aplicações web a montante, como bem chamadas a partir do próprio.  No entanto o mesmo grupo de segurança de rede explicitamente nega o acesso ao tráfego de entrada geral a partir da Internet. 

O resto deste tópico explica os passos necessários para configurar o grupo de segurança de rede na sub-rede que contém "apiase".

## <a name="determining-the-network-behavior"></a>Determinar o comportamento de rede
Para saber quais as regras de segurança de rede são necessárias, terá de determinar quais os clientes de rede serão permitidos a alcançar o ambiente de serviço de aplicações que contém a aplicação API e os clientes que serão bloqueados.

Uma vez que [(NSGs) de grupos de segurança de rede] [ NetworkSecurityGroups] são aplicados a sub-redes e ambientes de serviço de aplicações são implementados em sub-redes, as regras contidas num NSG são aplicadas para **todos os** aplicações em execução no ambiente de serviço de aplicações.  Utilizar a arquitetura de exemplo para este artigo, depois de um grupo de segurança de rede é aplicado à sub-rede que contém "apiase", todas as aplicações em execução no "apiase" ambiente de serviço de aplicação serão protegidas através do mesmo conjunto de regras de segurança. 

* **Determinar o endereço IP de saída dos chamadores montante:** o que é o endereço ou endereços IP dos chamadores montante?  Estes endereços terá de ser explicitamente permissão de acesso no NSG.  Uma vez que as chamadas entre ambientes do App Service são consideradas chamadas "Internet", isto significa que o endereço IP saído atribuído a cada uma a três montante ambientes do App Service tem permissão de acesso no NSG para sub-rede "apiase".   Para obter mais detalhes sobre como determinar o endereço IP de saída para aplicações em execução num ambiente de serviço de aplicações, consulte o [arquitetura de rede] [ NetworkArchitecture] artigo de descrição geral.
* **A aplicação de API de back-end terá de chamar si próprio?**  Um ponto, por vezes, ignorado e subtis é o cenário em que a aplicação de back-end tem de chamar si próprio.  Se uma aplicação de API de back-end num ambiente de serviço de aplicações tem de chamar si próprio, este também é tratado como uma chamada de "Internet".  A arquitetura de exemplo Isto requer que permite o acesso do endereço IP saído do "apiase" ambiente de serviço de aplicações, bem como.

## <a name="setting-up-the-network-security-group"></a>Configurar o grupo de segurança de rede
Depois do conjunto de endereços IP saídos são conhecidas, o passo seguinte é construir um grupo de segurança de rede.  Grupos de segurança de rede podem ser criados para o Gestor de recursos baseadas em redes virtuais, bem como as redes virtuais clássicas.  Os exemplos abaixo mostram a criar e configurar um NSG uma rede virtual clássica através do Powershell.

Para a arquitetura de exemplo, os ambientes estão localizados no Sul Central-na, pelo que é criado um NSG vazio nessa região:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Primeiro explícita permitir é adicionada uma regra para a infraestrutura de gestão do Azure, conforme indicado no artigo no [tráfego de entrada] [ InboundTraffic] para ambientes do App Service.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Em seguida, duas regras são adicionadas ao permite chamadas de HTTP e HTTPS a partir do primeiro a montante aplicação ambiente de serviço ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Rinse e repita para a segunda e terceira montante ambientes do App Service ("fe2ase" e "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Por último, conceda acesso ao endereço IP de saída do ambiente de serviço de aplicações a API de back-end, para que possa chamar novamente para si próprio.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Não existem outras regras de segurança de rede tem de ser configurados porque cada NSG tem um conjunto de regras predefinidas que bloquear o acesso de entrada da Internet por predefinição.

A lista completa das regras no grupo de segurança de rede são mostrados abaixo.  Tenha em atenção a como a último regra, o que é realçada, blocos de acesso de entrada de todos os chamadores além das que tenham sido explicitamente concedidos acesso.

![Configuração do NSG][NSGConfiguration] 

É o último passo para aplicar o NSG para sub-rede que contém a "apiase" ambiente de serviço de aplicações.  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Com o NSG aplicado à sub-rede, apenas os três montante ambientes do App Service e o ambiente de serviço de aplicações que contém o back-end de API, estão autorizados a chamada para o ambiente de "apiase".

## <a name="additional-links-and-information"></a>Informações e hiperligações adicionais
Informações sobre [grupos de segurança de rede](../../virtual-network/virtual-networks-nsg.md). 

Noções sobre [saídos endereços IP] [ NetworkArchitecture] e ambientes do App Service.

[Portas de rede] [ InboundTraffic] utilizado pelo ambientes do App Service.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
