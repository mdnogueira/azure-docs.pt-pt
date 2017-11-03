---
title: "Detalhes de configuração de rede para trabalhar com o Expressroute"
description: "Detalhes de configuração de rede para a execução ambientes do App Service num redes virtuais ligadas a um circuito ExpressRoute."
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: bb3e283e8a9327a9c66c8d8ded037cee5195ffc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Detalhes da Configuração de Rede para Ambientes de Serviço de Aplicações com o ExpressRoute
## <a name="overview"></a>Descrição geral
Os clientes podem ligar-se um [Azure ExpressRoute] [ ExpressRoute] circuito a respetiva infraestrutura de rede virtuais, assim expandir a sua rede no local para o Azure.  Um ambiente de serviço de aplicações podem ser criado numa sub-rede deste [rede virtual] [ virtualnetwork] infraestrutura.  As aplicações em execução no ambiente de serviço de aplicações, em seguida, podem estabelecer ligações seguras a recursos de back-end acessíveis apenas através da ligação do ExpressRoute.  

Um ambiente de serviço de aplicações podem ser criado na **ou** uma rede virtual do Azure Resource Manager, **ou** uma rede virtual do modelo de implementação clássica.  Com uma alteração recente efetuada em Junho de 2016, ASEs agora também podem ser implementados em redes virtuais que utilizam os intervalos de endereços público ou espaços de endereços RFC1918 (ou seja, endereços privados). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Conectividade de rede necessária
Existem requisitos de conectividade de rede para os ambientes de serviço de aplicações que não pode ser cumprido inicialmente numa rede virtual ligada ao ExpressRoute.  Ambientes do App Service necessitam de todas as seguintes opções para funcionar corretamente:

* Conectividade de rede de saída para pontos finais de armazenamento do Azure em todo o mundo em ambas as portas 80 e 443.  Isto inclui pontos finais localizados na mesma região que o ambiente de serviço de aplicações, bem como pontos finais de armazenamento localizados na **outros** regiões do Azure.  Pontos finais de armazenamento do Azure resolver em seguintes domínios DNS: *w*, *w*, *q* e *w*.  
* Conectividade de rede de saída para o serviço de ficheiros do Azure na porta 445.
* Conectividade de rede de saída para pontos finais de BD Sql localizada na mesma região que o ambiente de serviço de aplicações.  Pontos finais de BD SQL resolver em domínio seguinte: *database.windows.net*.  Isto requer acesso a portas 1433, 11000 11999 e 14000 14999 a abrir.  Para obter mais detalhes, consulte [neste artigo na utilização de porta de Sql Database V12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Conectividade de rede de saída para pontos finais plane gestão do Azure (ASM e ARM pontos finais).  Isto inclui a conectividade de saída para ambos *management.core.windows.net* e *management.azure.com*. 
* Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*.  Isto é necessário para suportar a funcionalidade SSL.
* A configuração de DNS para a rede virtual tem de ser capaz de resolver todos os pontos finais e domínios mencionados em pontos de anteriores.  Se estes pontos finais não podem ser resolvidos, as tentativas de criação do ambiente de serviço de aplicações irão falhar e ambientes do App Service existente serão marcados como danificados.
* Acesso de saída na porta 53 é necessário para comunicação com os servidores DNS.
* Se existir um servidor DNS personalizado na outra extremidade de um gateway de VPN, o servidor DNS deve ser acessível a partir da sub-rede que contém o ambiente de serviço de aplicações. 
* O caminho de rede de saída não é possível viajam através de proxies empresarias internos, nem pode ser forçada em túnel no local.  Se o fizer, altera o endereço NAT Efetivo do tráfego de rede de saída do ambiente de serviço de aplicações.  Alterar o endereço NAT de tráfego de rede de saída de um ambiente de serviço de aplicações irá causar falhas de conectividade para muitos dos pontos finais listados acima.  Isto resulta em tentativas falhadas de criação de ambiente de serviço de aplicações, bem como anteriormente bom ambientes do App Service a ser marcado como danificado.  
* Entrada de acesso de rede para as portas necessárias para ambientes do App Service tem de ser permitidos, tal como descrito neste [artigo][requiredports].

Os requisitos de DNS podem ser cumpridos ao garantir uma infraestrutura de DNS válida está configurada e mantida para a rede virtual.  Se por qualquer motivo, a configuração de DNS é alterada depois de criar um ambiente de serviço de aplicações, os programadores de podem forçar um ambiente de serviço de aplicações para recolher a nova configuração de DNS.  Acionar um reinício de ambiente graduais utilizando o ícone "Restart" localizado na parte superior do painel de gestão do ambiente de serviço de aplicações no [portal do Azure] [ NewPortal] fará com que o ambiente recolher a nova configuração de DNS.

Os requisitos de acesso de rede de entrada podem ser cumpridos ao configurar um [grupo de segurança de rede] [ NetworkSecurityGroups] na sub-rede do ambiente de serviço de aplicações para permitir o acesso necessário, tal como descrito neste [artigo][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Ativar a conectividade de rede de saída para um ambiente de serviço de aplicações
Por predefinição, um circuito ExpressRoute criado recentemente anuncia uma rota predefinida que permite a ligação à Internet de saída.  Com esta configuração de um ambiente de serviço de aplicações será possível estabelecer ligação com outros pontos finais do Azure.

No entanto, uma configuração de cliente comum é definir as seus próprios rota predefinida (0.0.0.0/0) que força o tráfego de Internet de saída para o fluxo em vez disso, no local.  Este fluxo de tráfego invariably de quebras de ambientes de serviço de aplicação porque o tráfego de saída é bloqueado no local ou NAT faria para um conjunto de endereços que deixará de funcionar com pontos finais Azure vários irreconhecível.

A solução é definir um (ou mais) rotas definidas pelo utilizador (UDRs) na sub-rede que contém o ambiente de serviço de aplicações.  Um UDR define as rotas de sub-rede específicos que serão cumpridas em vez da rota predefinida.

Se for possível, recomenda-se para utilizar a seguinte configuração:

* A configuração do ExpressRoute anuncia 0.0.0.0/0 e por predefinição, force túneis todos os tráfego de saída no local.
* UDR aplicado à sub-rede que contém o ambiente de serviço de aplicações define 0.0.0.0/0 com um tipo de próximo salto de Internet (um exemplo desta situação é farther pendente neste artigo).

O efeito combinado destes passos é que o nível de sub-rede UDR irá ter precedência sobre o ExpressRoute forçado túnel, que garante saído acesso à Internet a partir do ambiente de serviço de aplicações.

> [!IMPORTANT]
> As rotas definidas num UDR **tem** ser específico o suficiente para têm precedência sobre qualquer rotas anunciadas na configuração do ExpressRoute.  O exemplo abaixo utiliza o intervalo de endereços 0.0.0.0/0 abrangente e como tal, pode potencialmente ser acidentalmente substituído pelo anúncios de rota utilizando intervalos de endereços mais específicos.
> 
> Os ambientes de serviço de aplicações não são suportados com configurações de ExpressRoute que **cross-anunciar rotas a partir do caminho de peering público para o caminho de peering privado**.  Configurações de ExpressRoute que tenham o peering público configurado, receberá anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure.  Se estes intervalos de endereços entre anunciados no caminho de peering privado, o resultado final é que todos os pacotes de rede de saída da sub-rede do ambiente de serviço de aplicações será com imposição de túnel à infraestrutura de rede no local do cliente.  Este fluxo de rede não é suportado com ambientes do App Service.  Uma solução para este problema está a parar as rotas entre publicidade do caminho de peering público para o caminho de peering privado.
> 
> 

Informações de fundo sobre rotas definidas pelo utilizador estão disponíveis neste [descrição geral][UDROverview].  

Detalhes sobre como criar e configurar rotas definidas pelo utilizador está disponível neste [como ao guia][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Configuração de UDR de exemplo para um ambiente de serviço de aplicações
**Pré-requisitos**

1. Instalar o Azure Powershell a partir de [página de transferências do Azure] [ AzureDownloads] (com a data de Junho versão 2015 ou posterior).  Em "Ferramentas de linha de comandos" há uma ligação de "Instalação" em "Do Windows Powershell" que irá instalar os cmdlets do Powershell mais recentes.
2. Recomenda-se que é criar uma sub-rede exclusiva para utilização exclusiva por um ambiente de serviço de aplicações.  Isto garante que o UDRs aplicados à sub-rede apenas abrir o tráfego de saída para o ambiente de serviço de aplicações.
3. **Importante**: não implemente o ambiente de serviço de aplicações até **depois** forem seguidos os seguintes passos de configuração.  Isto garante que a conectividade de rede de saída está disponível antes de tentar implementar um ambiente de serviço de aplicações.

**Passo 1: Criar uma tabela de rotas com nome**

O fragmento seguinte cria uma tabela de rota chamada "DirectInternetRouteTable" na região do Azure do oeste-na:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Passo 2: Criar um ou mais rotas na tabela de rota**

Terá de adicionar um ou mais rotas para a tabela de rotas para poder ativar o acesso de Internet de saída.  

A abordagem recomendada para a configuração de acesso à Internet de saída consiste em definir uma rota de 0.0.0.0/0 conforme mostrado abaixo.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Lembre-se de que 0.0.0.0/0 é um intervalo de endereços abrangente e como tal irá ser substituído pelo mais específicos intervalos de endereços anunciados pelo ExpressRoute.  Para iterar voltar a recomendação anterior, um UDR com uma rota 0.0.0.0/0 deve ser utilizada em conjunto com uma configuração de Expressroute apenas anuncia 0.0.0.0/0 bem. 

Como alternativa, pode transferir uma lista abrangente e atualizada de intervalos CIDR em utilização pelo Azure.  O ficheiro Xml que contém todos os intervalos de endereços IP do Azure está disponível no [Microsoft Download Center][DownloadCenterAddressRanges].  

Tenha em atenção que que estes intervalos alteram ao longo do tempo, deste modo, sendo necessárias atualizações manuais periódicas para as rotas definidas pelo utilizador a manter sincronizadas.  Além disso, uma vez que existe um limite superior da predefinição de 100 rotas de um único UDR, terá de "resumir" os intervalos de endereços IP do Azure para caber dentro do limite de 100 rota, mantendo em mente que UDR definido rotas têm de ser mais específicas que as rotas anunciadas pelo ExpressRoute.  

**Passo 3: Associar a tabela de rotas para a sub-rede que contém o ambiente de serviço de aplicações**

O último passo de configuração está a associar a tabela de rotas para a sub-rede onde o ambiente de serviço de aplicações será implementado.  O comando seguinte associa "DirectInternetRouteTable" para o "ASESubnet" que eventualmente irá conter um ambiente de serviço de aplicações.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Passo 4: Passos finais**

Depois da tabela de rotas é vinculada à sub-rede, recomenda-se primeiro de teste e confirmar o efeito que se destinam.  Por exemplo, implementar uma máquina virtual para a sub-rede e confirme se:

* Tráfego de saída para o Azure e os pontos finais do Azure não mencionados anteriormente neste artigo é **não** fluir o circuito do ExpressRoute.  É muito importante verificar este comportamento, uma vez que o se o tráfego de saída da sub-rede é ainda a ser forçada em túnel no local, ambiente de serviço de aplicações criação irá falhar sempre. 
* Pesquisas de DNS para os pontos finais mencionado anteriormente todas a resolver corretamente. 

Assim que forem confirmadas as etapas acima, terá de eliminar a máquina virtual porque a sub-rede tem de ser "vazio" em que o ambiente de serviço de aplicações é criado.

Em seguida, prosseguir com a criação de um ambiente de serviço de aplicações!

## <a name="getting-started"></a>Introdução
Para começar com ambientes do App Service, consulte o artigo [introdução ao ambiente de serviço de aplicações][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
