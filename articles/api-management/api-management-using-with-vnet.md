---
title: Como utilizar a API Management do Azure com redes virtuais
description: "Saiba como configurar uma ligação a uma rede virtual na API Management do Azure e acesso serviços web através do mesmo."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: apimpm
ms.openlocfilehash: 9970452b62b31f28f8277580dd1075c306767d8b
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Como utilizar a API Management do Azure com redes virtuais
Redes virtuais do Azure (VNETs) permitem-lhe colocar qualquer um dos seus recursos do Azure numa rede routeable não internet que controla o acesso a. Estas redes, em seguida, podem ser ligadas a suas redes no local utilizando várias tecnologias VPN. Para saber mais sobre redes virtuais do Azure começar a utilizar as informações aqui: [descrição geral de rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

Gestão de API do Azure pode ser implementado dentro da rede virtual (VNET), para que possa aceder a serviços de back-end dentro da rede. O portal do programador e o gateway de API, pode ser configurado para ser acessível a partir da Internet ou apenas dentro da rede virtual.

> [!NOTE]
> Gestão de API do Azure suporta clássicas e do Azure VNets do Gestor de recursos.
>

## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância APIM. Para obter mais informações, consulte [criar uma instância de API Management do Azure](get-started-create-service-instance.md).
+ Conectividade VNET está disponível apenas as camadas para programadores e Premium. Mudar para uma destas camadas, seguindo as indicações de [atualizar e dimensionar](upgrade-and-scale.md#upgrade-and-scale) tópico.

## <a name="enable-vpn"></a>Ligação ativar VNET

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Ativar a conetividade VNET no portal do Azure

1. Navegue até à sua instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **domínios personalizados e SSL**.
3. Configure a instância de API Management para ser implementada no interior de uma rede Virtual.

    ![Menu de rede virtual de gestão de API][api-management-using-vnet-menu]
4. Selecione o tipo de acesso pretendida:
    
    * **Externo**: o portal de gestão de API do gateway e programador estejam acessíveis a partir da internet pública através de um balanceador de carga externo. O gateway pode aceder a recursos na rede virtual.
    
    ![Peering público][api-management-vnet-public]
    
    * **Interno**: portal de gateway e o programador da API Management estão acessíveis apenas a partir de dentro da rede virtual através de um balanceador de carga interno. O gateway pode aceder a recursos na rede virtual.
    
    ![Peering privado][api-management-vnet-private]`

    Agora, irá ver uma lista de todas as regiões onde o serviço de API Management está aprovisionado. Selecione uma VNET e sub-rede para cada região. A lista ser preenchida com clássica e Resource Manager redes virtuais disponíveis nas suas subscrições do Azure que estão a configuração na região que está a configurar.
    
    > [!NOTE]
    > **Ponto final de serviço** no diagrama acima inclui Gateway/Proxy, Portal do publicador, Portal do programador, GIT e o ponto final de gestão direta.
    > **Ponto final de gestão** no diagrama acima é o ponto final alojado no serviço para gerir a configuração através do portal do Azure e do Powershell.
    > Além disso, tenha em atenção que, apesar de, o diagrama mostra os endereços IP para os pontos finais várias, serviço de API Management **apenas** responde no respetivo Hostnames configurado.
    
    > [!IMPORTANT]
    > Quando implementar uma instância de API Management do Azure para uma VNET do Resource Manager, o serviço deve estar numa sub-rede dedicada que contém outros recursos de exceto instâncias de API Management do Azure. Se for efetuada uma tentativa para implementar uma instância de API Management do Azure a uma sub-rede da VNET do Resource Manager que contém outros recursos, a implementação irá falhar.
    >

    ![Selecione a VPN][api-management-setup-vpn-select]

5. Clique em **guardar** na parte superior do ecrã.

> [!NOTE]
> O endereço VIP da instância de API Management alterará sempre que a VNET está ativado ou desativado.  
> O endereço VIP também será alterado quando a API Management é movido da **externo** para **interno** ou vice-versa
>

> [!IMPORTANT]
> Se remover a API Management a partir de uma VNET ou alterar um que está implementada numa, a VNET utilizada anteriormente pode permanecer bloqueada para 4 horas. Durante este período, não é possível eliminar a VNET ou implementar um novo recurso.

## <a name="enable-vnet-powershell"></a>Ligação de VNET ativar utilizando cmdlets do PowerShell
Também pode ativar a conectividade VNET utilizando os cmdlets do PowerShell

* **Criar um serviço de API Management dentro de uma VNET**: Utilize o cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) para criar um serviço de API Management do Azure dentro de uma VNET.

* **Implementar um serviço de API Management existente no interior de uma VNET**: Utilize o cmdlet [atualização AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) para mover um serviço de API Management do Azure existente no interior de uma rede Virtual.

## <a name="connect-vnet"></a>Ligar a um serviço web alojado dentro de uma rede virtual
Depois do serviço de API Management está ligado a VNET, aceder aos serviços de back-end dentro da mesma é igual ao aceder aos serviços do público. Escreva o endereço IP local ou o nome de anfitrião (se um servidor DNS está configurado para a VNET) do seu serviço web para o **URL do serviço Web** campo ao criar uma nova API ou editar uma existente.

![Adicionar API de VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"></a>Problemas comuns de configuração de rede
Segue-se uma lista dos problemas de configurações incorretas comuns que podem ocorrer durante a implementação de serviço de API Management numa rede Virtual.

* **Configuração do servidor DNS personalizada**: gestão de API o serviço depende de vários serviços do Azure. Quando a API Management está alojado numa VNET com um servidor DNS personalizado, tem de resolver os nomes de anfitriões desses serviços do Azure. Siga [isto](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) orientações sobre a configuração DNS personalizada. Consulte a tabela de portas abaixo e outros requisitos de rede para referência.

> [!IMPORTANT]
> Recomenda-se que, se estiver a utilizar um servidor de DNS personalizado (es) para a VNET, configure que **antes** implementar um serviço de API Management para a mesma. Caso contrário, terá de atualizar o serviço de API Management sempre que alterar os servidores de DNS (s) executando o [aplicar a operação de configuração de rede](https://docs.microsoft.com/en-us/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **As portas necessárias para a API Management**: tráfego de entrada e saída para a sub-rede na qual a API Management está implementada pode ser controlado através de [grupo de segurança de rede][Network Security Group]. Se qualquer uma destas portas não estão disponíveis, a gestão de API poderá não funcionar corretamente e podem ficar inacessível. Um ou mais destas portas bloqueadas é ter outro problema de configuração incorreta comum quando utilizar a API Management com uma VNET.

Quando uma instância de serviço de API Management está alojada numa VNET, as portas na tabela seguinte são utilizadas.

| Origem / destino portas | Direção | Protocolo de transporte | Origem / destino | Objetivo (*) | Tipo de rede virtual |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |Entrada |TCP |INTERNET / VIRTUAL_NETWORK|Comunicação de cliente para gestão de API|Externo |
| * / 3443 |Entrada |TCP |INTERNET / VIRTUAL_NETWORK|Ponto final de gestão para o portal do Azure e o Powershell |Interno |
| * / 80, 443 |Saída |TCP |VIRTUAL_NETWORK / INTERNET|**Acesso a pontos finais de armazenamento do Azure** |Externo & interno |
| * / 1433 |Saída |TCP |VIRTUAL_NETWORK / INTERNET|**Acesso a pontos finais do SQL do Azure** |Externo & interno |
| * / 11000 - 11999 |Saída |TCP |VIRTUAL_NETWORK / INTERNET|**Acesso ao Azure SQL V12 mais recente** |Externo & interno |
| * / 14000 - 14999 |Saída |TCP |VIRTUAL_NETWORK / INTERNET|**Acesso ao Azure SQL V12 mais recente** |Externo & interno |
| * / 5671 |Saída |AMQP |VIRTUAL_NETWORK / INTERNET|Dependência de registo para a política de Hub de eventos e o agente de monitorização |Externo & interno |
| * / 445 |Saída |TCP |VIRTUAL_NETWORK / INTERNET|Dependência de partilha de ficheiros do Azure de GIT |Externo & interno |
| * / 6381 - 6383 |Entrada e saída |TCP |VIRTUAL_NETWORK / VIRTUAL_NETWORK|Instâncias de Cache de Redis acesso entre RoleInstances |Externo & interno |
| * / * | Entrada |TCP |AZURE_LOAD_BALANCER / VIRTUAL_NETWORK| Balanceador de carga da infraestrutura do Azure |Externo & interno |

>[!IMPORTANT]
> * As portas para os quais o *objetivo* é **negrito** são necessários para o serviço de API Management ser implementada com êxito. Bloquear as portas de outras no entanto irá causar degradação na capacidade de utilizar e monitorizar o serviço em execução.

* **Funcionalidade SSL**: para ativar a validação de gestão de API e criação de cadeias de certificados SSL serviço necessita de conectividade de rede de saída ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. Esta dependência não é necessária se qualquer certificado que carrega a API de gestão contém a cadeia completa para a raiz da AC.

* **Acesso de DNS**: acesso de saída na porta 53 é necessário para comunicação com os servidores DNS. Se existir um servidor DNS personalizado na outra extremidade de um gateway de VPN, o servidor DNS deve ser acessível a partir de sub-rede que aloja a API Management.

* **Métricas e monitorização de estado de funcionamento**: conectividade de rede de saída para Azure pontos finais de monitorização, que resolver em seguintes domínios: global.metrics.nsatc.net, shoebox2.metrics.nsatc.net, prod3.metrics.nsatc.net.

* **Configuração de rota rápida**: uma configuração de cliente comum consiste em definir as seus próprios rota predefinida (0.0.0.0/0) que força o tráfego de Internet de saída para o fluxo em vez disso, no local. Este fluxo de tráfego invariably de quebras de conectividade com a API Management do Azure porque o tráfego de saída é bloqueado no local ou NAT faria para um conjunto de endereços que deixará de funcionar com pontos finais Azure vários irreconhecível. A solução é definir um (ou mais) rotas definidas pelo utilizador ([UDRs][UDRs]) na sub-rede que contém a API Management do Azure. Um UDR define as rotas de sub-rede específicos que serão cumpridas em vez da rota predefinida.
  Se for possível, recomenda-se para utilizar a seguinte configuração:
 * A configuração do ExpressRoute anuncia 0.0.0.0/0 e por predefinição, force túneis todos os tráfego de saída no local.
 * UDR aplicado à sub-rede que contém a API Management do Azure define 0.0.0.0/0 com um tipo de próximo salto de Internet.
 O efeito combinado destes passos é que o nível de sub-rede UDR tem precedência sobre o ExpressRoute forçado túnel, que garante saído acesso à Internet a partir da API Management do Azure.

>[!WARNING]  
>Gestão de API do Azure não é suportado com configurações de ExpressRoute que **incorretamente cross-anunciar rotas a partir do caminho de peering público para o caminho de peering privado**. Configurações de ExpressRoute que tenham o peering público configurado, receberá anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se estes intervalos de endereços são incorretamente cross-anunciados no caminho de peering privado, o resultado final é que todos os pacotes de rede de saída da sub-rede a instância de API Management do Azure estão em incorretamente imposição de túnel para rede no local de um cliente infraestrutura. Este fluxo de rede de quebras de API Management do Azure. A solução para este problema está a parar as rotas entre publicidade do caminho de peering público para o caminho de peering privado.


## <a name="troubleshooting"></a>Resolução de problemas
* **Inicial configuração**: quando a implementação inicial do serviço de API Management para uma sub-rede tiver êxito, recomenda-se primeiro implementar uma máquina virtual na mesma sub-rede. Ambiente de trabalho remoto seguinte para a máquina virtual e validar se há conetividade para uma de cada recurso abaixo na sua subscrição do azure 
    * Blob de armazenamento do Azure
    * Base de Dados SQL do Azure

 > [!IMPORTANT]
 > Depois de validar a conectividade, certifique-se remover todos os recursos implementados na sub-rede, antes de implementar a gestão de API para a sub-rede.

* **As atualizações incrementais**: quando efetuar alterações à sua rede, consulte [NetworkStatus API](https://docs.microsoft.com/en-us/rest/api/apimanagement/networkstatus), para verificar que o serviço de API Management não perdeu acesso a qualquer um dos recursos críticos que que depende. O estado de conectividade deve ser atualizado a cada 15 minutos.

* **Ligações de navegação de recursos**: ao implementar numa sub-rede de vnet do Resource Manager estilo, gestão de API reserva-se a sub-rede através da criação de uma ligação de navegação de recursos. Se a sub-rede já contém um recurso de um fornecedor diferente, a implementação será **falhar**. Da mesma forma, quando mover um serviço de API Management para outra sub-rede ou eliminá-lo, vamos remover dessa ligação de navegação de recursos. 

## <a name="routing"></a> Encaminhamento
+ Será possível reservar um endereço IP público com balanceamento de carga (VIP) para fornecer acesso a todos os pontos finais de serviço.
+ Um endereço IP de um intervalo de sub-rede IP (DIP) será utilizado para aceder a recursos dentro da vnet e um endereço IP público (VIP) será utilizado para aceder a recursos fora da vnet.
+ IP público com balanceamento de carga endereço pode ser encontrado no painel de descrição geral/Essentials no portal do Azure.

## <a name="limitations"></a>Limitações
* Uma sub-rede que contém instâncias de API Management não pode conter outros tipos de recursos do Azure.
* A sub-rede e o serviço de API Management tem de ser na mesma subscrição.
* Uma sub-rede que contém instâncias de API Management não pode ser movida entre subscrições.
* Para implementações de gestão de API de multirregião configuradas no modo de rede virtual interna, os utilizadores são responsáveis por gerir a balanceamento de carga entre várias regiões, como possuem o encaminhamento.


## <a name="related-content"></a>Relacionados com o conteúdo
* [Ligar uma rede Virtual ao back-end utilizando o Gateway de Vpn](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Ligar uma rede Virtual a partir de modelos de implementação diferentes](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Como utilizar a API de Inspector para rastreio chama-se na API Management do Azure](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md
