---
title: "Como controlar o tráfego de entrada para um ambiente de serviço de aplicações"
description: "Saiba mais sobre como configurar regras de segurança de rede para controlar o tráfego de entrada para um ambiente de serviço de aplicações."
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: 
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.openlocfilehash: ed72bf3202d6cb2d2161bc0df693d3e6a1fc58ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Como controlar o tráfego de entrada para um ambiente de serviço de aplicações
## <a name="overview"></a>Descrição geral
Um ambiente de serviço de aplicações podem ser criado na **ou** uma rede virtual do Azure Resource Manager, **ou** um modelo de implementação clássica [rede virtual] [ virtualnetwork].  Uma nova rede virtual e uma nova sub-rede podem ser definidos da hora de que criação de um ambiente de serviço de aplicações.  Em alternativa, um ambiente de serviço de aplicações podem ser criado numa rede virtual já existente e sub-rede já existente.  Com uma alteração efetuada em Junho de 2016, ASEs também podem ser implementados em redes virtuais que utilizam os intervalos de endereços público ou espaços de endereços RFC1918 (ou seja, endereços privados).  Para obter mais detalhes sobre a criação de um ambiente de serviço de aplicações, consulte [como criar um ambiente de serviço de aplicações][HowToCreateAnAppServiceEnvironment].

Um ambiente de serviço de aplicações tem sempre de ser criado numa sub-rede porque uma sub-rede fornece um limite de rede que pode ser utilizado para bloquear o tráfego de entrada atrás montante dispositivos e serviços de forma a que o tráfego HTTP e HTTPS é apenas aceite específicos a montante Endereços IP.

Tráfego de rede de entrada e saída de uma sub-rede é controlado através de um [grupo de segurança de rede][NetworkSecurityGroups]. Controlar o tráfego de entrada requer a criação de regras de segurança de rede num grupo de segurança de rede e, em seguida, atribuir a segurança de rede de grupo a sub-rede que contém o ambiente de serviço de aplicações.

Depois de um grupo de segurança de rede é atribuído a uma sub-rede, tráfego de entrada para aplicações no ambiente de serviço de aplicações é permitido/bloqueado com base no permitir e negar regras definidas no grupo de segurança de rede.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Portas de rede utilizadas num ambiente de serviço de aplicação de entrada
Antes de bloquear o tráfego de rede de entrada com um grupo de segurança de rede, é importante saber o conjunto de portas de rede necessários e opcionais utilizado por um ambiente de serviço de aplicações.  Fechar acidentalmente desativar o tráfego para algumas portas pode resultar na perda das funcionalidades num ambiente de serviço de aplicações.

Segue-se uma lista de portas utilizadas por um ambiente de serviço de aplicações. Todas as portas são **TCP**, exceto claramente indicação em contrário:

* 454: **necessário porta** utilizado pela infraestrutura do Azure para gerir e manter a ambientes de serviço de aplicações através de SSL.  Bloqueia o tráfego para esta porta.  Esta porta sempre está vinculada ao VIP público de ASE.
* 455: **necessário porta** utilizado pela infraestrutura do Azure para gerir e manter a ambientes de serviço de aplicações através de SSL.  Bloqueia o tráfego para esta porta.  Esta porta sempre está vinculada ao VIP público de ASE.
* 80: predefinição a porta para o tráfego HTTP de entrada para aplicações em execução no App Service planos num ambiente de serviço de aplicações.  Num ASE ativado o ILB, está vinculada esta porta para o endereço do ILB do ASE.
* 443: predefinição a porta para o tráfego de entrada SSL para aplicações em execução no App Service planos num ambiente de serviço de aplicações.  Num ASE ativado o ILB, está vinculada esta porta para o endereço do ILB do ASE.
* 21: canal de controlo para FTP.  Esta porta pode ser bloqueada com segurança FTP não está a ser utilizado.  Num ASE ILB-ativada, esta porta pode ser vinculada para o endereço do ILB ASE.
* 990: canal de controlo para FTPS.  Esta porta pode ser bloqueada com segurança FTPS não está a ser utilizado.  Num ASE ILB-ativada, esta porta pode ser vinculada para o endereço do ILB ASE.
* 10001 10020: canais de dados para FTP.  Tal como acontece com o canal de controlo, estas portas podem em segurança bloqueadas se FTP não está a ser utilizado.  Num ASE ILB-ativada, esta porta pode estar vinculada ao endereço ILB o ASE.
* 4016: utilizado para depuração remota com o Visual Studio 2012.  Esta porta pode ser bloqueada com segurança a funcionalidade não está a ser utilizado.  Num ASE ativado o ILB, está vinculada esta porta para o endereço do ILB do ASE.
* 4018: utilizado para depuração remota com o Visual Studio 2013.  Esta porta pode ser bloqueada com segurança a funcionalidade não está a ser utilizado.  Num ASE ativado o ILB, está vinculada esta porta para o endereço do ILB do ASE.
* 4020: utilizado para depuração remota com o Visual Studio 2015.  Esta porta pode ser bloqueada com segurança a funcionalidade não está a ser utilizado.  Num ASE ativado o ILB, está vinculada esta porta para o endereço do ILB do ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de saída e os requisitos de DNS
Para um ambiente de serviço de aplicações funcionar corretamente, também requer acesso a vários pontos finais de saída. Uma lista completa dos pontos finais externos utilizados pelo ASE está na secção "Necessárias conectividade de rede" o [configuração de rede para o ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artigo.

Ambientes do App Service necessitam de uma infraestrutura de DNS válida configurada para a rede virtual.  Se por qualquer motivo, a configuração de DNS é alterada depois de criar um ambiente de serviço de aplicações, os programadores de podem forçar um ambiente de serviço de aplicações para recolher a nova configuração de DNS.  Acionar um reinício de ambiente graduais utilizando o ícone "Restart" localizado na parte superior do painel de gestão do ambiente de serviço de aplicações no [portal do Azure] [ NewPortal] fará com que o ambiente recolher a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet ser configurado antes de tempo antes de criar um ambiente de serviço de aplicações.  Se a configuração de DNS de uma rede virtual é alterada durante a criação de um ambiente de serviço de aplicações, que resultará na falha de processo de criação do ambiente de serviço de aplicações.  Num vein semelhante, se um servidor DNS personalizado existe na outra extremidade de um gateway de VPN e o servidor DNS está inacessível ou não está disponível, o processo de criação do ambiente de serviço de aplicações também falharão.

## <a name="creating-a-network-security-group"></a>Criar um grupo de segurança de rede
Para obter detalhes completos na forma como o trabalho de grupos de segurança de rede, consulte o seguinte [informações][NetworkSecurityGroups].  A gestão de serviço do Azure exemplo abaixo tocar em realça dos grupos de segurança de rede, com um focarem-se na configurar e aplicar um grupo de segurança de rede a uma sub-rede que contém um ambiente de serviço de aplicações.

**Nota:** grupos de segurança de rede podem ser configurados graficamente utilizando o [Portal do Azure](https://portal.azure.com) ou através do PowerShell do Azure.

Grupos de segurança de rede pela primeira vez são criados como uma entidade de autónoma associada uma subscrição. Uma vez que os grupos de segurança de rede são criados numa região do Azure, certifique-se de que o grupo de segurança de rede é criado na mesma região que o ambiente de serviço de aplicações.

O seguinte mostra como criar um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Assim que for criado um grupo de segurança de rede, uma ou mais regras de segurança de rede são adicionadas ao mesmo.  Uma vez que o conjunto de regras pode alterar ao longo do tempo, recomenda-se ao espaço de esquema numeração utilizado para prioridades da regra para facilitar a inserir regras adicionais ao longo do tempo.

O exemplo abaixo mostra uma regra que conceda explicitamente acesso às portas de gestão necessários para a infraestrutura do Azure para gerir e manter um ambiente de serviço de aplicações.  Tenha em atenção que todo o tráfego de gestão flui através de SSL e protegido por certificados de cliente, pelo que, apesar das portas são abertas são inacessíveis por qualquer entidade que não seja a infraestrutura de gestão do Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Quando a bloquear o acesso à porta 80 e 443 para um ambiente de serviço de aplicações por trás de serviços ou dispositivos a montante "Ocultar", terá de saber o endereço IP a montante.  Por exemplo, se estiver a utilizar uma firewall de aplicação web (WAF), o WAF terá os seus próprios endereços IP (ou endereços) que utiliza quando o tráfego de utilização do proxy para um ambiente de serviço de aplicações a jusante.  Terá de utilizar este endereço IP no *SourceAddressPrefix* parâmetro de uma regra de segurança de rede.

No exemplo abaixo, o tráfego de entrada de um endereço IP a montante específico explicitamente é permitido.  O endereço *1.2.3.4* é utilizado como um marcador de posição para o endereço IP de uma WAF a montante.  Altere o valor a corresponder o endereço utilizado pelo seu dispositivo a montante ou serviço.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Se pretender o suporte FTP, as seguintes regras podem ser utilizadas como um modelo para conceder acesso para a porta do controlo FTP e os dados portas de canal.  Uma vez que o FTP é um protocolo de monitorização de estado, poderá não conseguir encaminhar o tráfego FTP através de um dispositivo de firewall ou proxy HTTP/HTTPS tradicional.  Neste caso, terá de definir o *SourceAddressPrefix* para um valor diferente - por exemplo o intervalo de endereços IP de máquinas de programador ou implementação no qual FTP clientes estão a executar. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Nota:** pode alterar o intervalo de portas de canal de dados durante o período de pré-visualização.)

Se for utilizada a depuração remota com o Visual Studio, as regras seguintes demonstram como conceder acesso.  Não há uma regra separada para cada versão suportada do Visual Studio, uma vez que cada versão utiliza uma porta diferente para depuração remota.  Tal como acontece com acesso de FTP, tráfego de depuração remoto não poderá fluir corretamente através de um dispositivo de proxy ou WAF tradicional.  O *SourceAddressPrefix* em vez disso, pode ser definido como o intervalo de endereços IP de máquinas de programador com o Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Atribuição de um grupo de segurança de rede a uma sub-rede
Um grupo de segurança de rede tem uma regra de segurança predefinida que nega o acesso a todo o tráfego externo.  O resultado da combinação as regras de segurança de rede descritas acima e a regra de segurança predefinida bloquear o tráfego de entrada, é que o tráfego apenas de intervalos de endereços de origem associado um *permitir* ação será capaz de enviar tráfego para aplicações em execução num ambiente de serviço de aplicações.

Depois de um grupo de segurança de rede é preenchido com as regras de segurança, tem de ser atribuídos à sub-rede que contém o ambiente de serviço de aplicações.  O comando de atribuição referencia tanto o nome da rede virtual onde reside o ambiente de serviço de aplicações, bem como o nome da sub-rede onde foi criado o ambiente de serviço de aplicações.  

O exemplo abaixo mostra um grupo de segurança de rede que está a ser atribuído a uma sub-rede e a rede virtual:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Assim que a atribuição de grupo de segurança de rede é concluída com êxito (a atribuição é um de operações de longa execução e pode demorar alguns minutos para concluir) correspondente ao tráfego de entrada apenas *permitir* regras com êxito irão aceder apps no App Service Ambiente.

Por questões de exaustividade o exemplo seguinte mostra como remover e, por conseguinte, dis-associar o grupo de segurança de rede da sub-rede:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerações especiais para explícita de SSL de IP
Se uma aplicação está configurada com um endereço de IP-SSL explícito (aplicáveis *apenas* para ASEs que tenham um VIP público), em vez de utilizar o endereço IP predefinido do ambiente de serviço de aplicações, HTTP e HTTPS de tráfego fluxos para a sub-rede através de um obter conjunto diferente de portas que não as portas 80 e 443.

O par individuais de portas utilizadas por cada endereço de IP-SSL pode ser encontrado na interface do portal de utilizador a partir do painel UX de detalhes do ambiente de serviço de aplicações.  Selecionadas "todas as definições"--> "Endereços de IP".  O painel "Endereços de IP" mostra uma tabela de todos os endereços de IP-SSL configurados explicitamente para o ambiente de serviço de aplicação, juntamente com o par de portas especial que é utilizado para encaminhar o tráfego HTTP e HTTPS associado a cada endereço de IP-SSL.  É este par de porta tem de ser utilizado para os parâmetros de DestinationPortRange quando configurar regras de um grupo de segurança de rede.

Quando uma aplicação num ASE está configurada para utilizar SSL de IP, os clientes externos não irão ver e não precisam de preocupar com o mapeamento do par de portas especial.  O tráfego para as aplicações irão fluir normalmente para o endereço de IP-SSL configurado.  A tradução para o par de portas especial automaticamente ocorre internamente durante a fase final de encaminhamento de tráfego para a sub-rede que contém o ASE. 

## <a name="getting-started"></a>Introdução
Para começar com ambientes do App Service, consulte o artigo [introdução ao ambiente de serviço de aplicações][IntroToAppServiceEnvironment]

Para obter mais detalhes em torno aplicações ligar em segurança a recursos de back-end, a partir de um ambiente de serviço de aplicações, consulte [em segurança ligação aos recursos de back-end de um ambiente de serviço de aplicações][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

