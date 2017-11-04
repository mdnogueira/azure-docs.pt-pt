---
title: Criar, alterar ou eliminar um peering de rede virtual do Azure | Microsoft Docs
description: Saiba como criar, alterar ou eliminar um peering de rede virtual.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 9fcfca3de6204581936a2bacfd86e84fd373190a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Criar, alterar ou eliminar um peering de rede virtual

Saiba como criar, alterar ou eliminar um peering de rede virtual. Peering de rede virtual permite-lhe ligar redes virtuais através da rede principal do Azure. Uma vez executado o peering, as redes virtuais ainda são geridas como recursos separados. Se não estiver familiarizado com o peering de rede virtual, recomendamos a leitura a [descrição geral de peering da rede Virtual](virtual-network-peering-overview.md) e concluir o [criar um tutorial de peering de rede virtual](virtual-network-create-peering.md), antes de concluir o tarefas neste artigo.

O peering de redes virtuais na mesma região encontra-se em disponibilidade geral. O peering de redes virtuais em diferentes regiões encontra-se atualmente em pré-visualização nas regiões E.U.A. Centro-Oeste, Canadá Central e E.U.A. Oeste 2. Pode [registar a sua subscrição na pré-visualização.](virtual-network-create-peering.md)

> [!WARNING]
> Os peerings de redes virtuais criados neste cenário podem não ter o mesmo nível de disponibilidade e fiabilidade do que em cenários de versão de disponibilidade geral. Os peerings de redes virtuais podem ter capacidades restringidas e não estar disponíveis em todas as regiões do Azure. Para obter as notificações mais atualizadas sobre a disponibilidade e o estado desta funcionalidade, veja a página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).
>

## <a name="before-you-begin"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir os passos em qualquer secção deste artigo:

- Reveja o [Azure limita](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artigo para saber mais sobre os limites para o peering.
- Inicie sessão no portal do Azure, interface de linha de comandos (CLI) do Azure ou do Azure PowerShell com uma conta do Azure. Se ainda não tiver uma conta do Azure, inscreva-se um [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se utilizar comandos do PowerShell para concluir tarefas neste artigo, [instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que dispõe da versão mais recente dos cmdlets Azure PowerShell instaladas. Para obter ajuda para comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se utilizar comandos de interface de linha de comandos (CLI) do Azure para concluir tarefas neste artigo, [instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que dispõe da versão mais recente da CLI do Azure instalados. Para obter ajuda para comandos da CLI, escreva `az <command> --help`. Em vez de instalar a CLI e respetivos pré-requisitos, pode utilizar a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. A Shell de nuvem tem a CLI do AZURE pré-instalado e configurado para utilizar com a sua conta. Para utilizar a Shell de nuvem, clique na Shell de nuvem **> _** na parte superior da parte a [portal](https://portal.azure.com). 

## <a name="create-a-peering"></a>Criar um peering

>[!NOTE]
>Antes de criar um peering, certifique-se tiver familiarized por si com o [requisitos e restrições](#requirements-and-constraints) e [as permissões necessárias](#permissions).
>

1. Inicie sessão no [portal](https://portal.azure.com) com uma conta que é atribuída o necessário [funções ou permissões](#permissions).
2. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *redes virtuais*. Quando **redes virtuais** é apresentado nos resultados da pesquisa, clique no mesmo. Não selecione **redes virtuais (clássicas)** se aparece na lista, como não é possível criar um peering de uma rede virtual implementada através do modelo de implementação clássica.
3. No **redes virtuais** painel apresentado, clique na rede virtual que pretende criar um peering para.
4. No painel que aparece para a rede virtual que selecionou, clique em **Peerings** no **definições** secção.
5. Clique em **+ adicionar**. 
6. <a name="add-peering"></a>No **adicionar peering** painel, introduza ou selecione os valores para as seguintes definições:
    - **Nome:** o nome para o peering tem de ser exclusivo dentro da rede virtual.
    - **Modelo de implementação de rede virtual:** selecione a rede virtual que pretende peering com de modelo de implementação de que foi implementada através de.
    - **Sei o ID de recurso:** se tiver acesso de leitura para a rede virtual que pretende peering com, deixe esta caixa de verificação desmarcada. Se não tiver acesso de leitura para a rede virtual ou uma subscrição que pretende peering com, esta caixa de verificação. Introduza o ID de recurso completo da rede virtual que pretende peering com no **ID de recurso** caixa que antes eram quando tiver selecionado a caixa. Tem de ser o ID de recurso que introduziu para uma rede virtual que existe na mesmo do Azure [região](https://azure.microsoft.com/regions) como esta rede virtual. Se pretender selecionar uma rede virtual numa região diferente, [registar a sua subscrição para a pré-visualização.](virtual-network-create-peering.md) O ID de recurso completo semelhante /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}<Id>/providers/Microsoft.Network/virtualNetworks/ /resourceGroups/ < nome de grupo de recursos > <--nome de rede virtual >. Pode obter o ID de recurso para uma rede virtual visualizando as propriedades de uma rede virtual. Para saber como visualizar as propriedades de uma rede virtual, consulte o artigo [gerir redes virtuais](virtual-network-manage-network.md#view-vnet).
    - **Subscrição:** selecionar o [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) da rede virtual que pretende peering com. Uma ou mais subscrições são listadas, dependendo de quantos subscrições a conta tem acesso de leitura para. Se tiver selecionado o **ID de recurso** caixa de verificação, esta definição não está disponível. Pode elemento redes virtuais em diferentes subscrições, desde que ambas as redes virtuais criadas através do Resource Manager. A capacidade de elemento entre subscrições criadas através de modelos de implementação diferentes está na versão de pré-visualização. Registar-se para a pré-visualização antes de criar um peering entre redes virtuais implementadas através de modelos de implementação diferentes que existam em diferentes subscrições. Saiba mais sobre como registar-se para a pré-visualização e [elemento redes virtuais criadas através de modelos de implementação diferentes em diferentes subscrições](create-peering-different-deployment-models-subscriptions.md).
    - **Rede virtual:** selecione a rede virtual que pretende peering com. Pode selecionar uma rede virtual criadas através de um modelo de implementação do Azure. Se pretender selecionar uma rede virtual numa região diferente, [registar a sua subscrição para a pré-visualização.](virtual-network-create-peering.md) Tem de ter acesso de leitura para a rede virtual para que seja visível na lista. Se uma rede virtual é listada, mas a cinzento, poderá ser porque o espaço de endereços da rede virtual sobrepõe-se o espaço de endereços para esta rede virtual. Se a sobreposição de espaços de endereços de rede virtual, não pode ser emparelhados. Se tiver selecionado o **ID de recurso** caixa de verificação, esta definição não está disponível.
    - **Permitir o acesso de rede virtual:** selecione **ativado** (predefinição), se pretender ativar a comunicação entre as duas redes virtuais. Ativar a comunicação entre redes virtuais permite que os recursos ligados a uma rede virtual para comunicar entre si com o mesmo largura de banda e latência, como se ligados à mesma rede virtual. Todas as comunicações entre os recursos as duas redes virtuais é através da rede privada do Azure. O **VirtualNetwork** etiqueta predefinida para grupos de segurança de rede abrange a rede virtual e a rede virtual em modo de peering. Para obter mais informações sobre etiquetas de predefinição de grupo de segurança de rede, leia o [descrição geral de grupos de segurança de rede](virtual-networks-nsg.md#default-tags) artigo.  Selecione **desativado** se não quiser que o tráfego que irão transitar para a rede virtual em modo de peering. Poderá selecionar **desativado** se tiver uma rede virtual com a outra rede virtual em modo de peering, mas ocasionalmente pretende desativar o fluxo de tráfego entre as duas redes virtuais. Pode considerar a ativação/desativação é mais conveniente de eliminar e voltar a criar peerings. Quando esta definição estiver desativada, o tráfego não fluir entre as redes virtuais em modo de peering.
    - **Permitir tráfego reencaminhado:** marcar esta caixa para permitir tráfego reencaminhado para a rede virtual em modo de peering (tráfego não com origem na rede virtual em modo de peering) fluxo para esta rede virtual. Encaminhamento de tráfego é comum quando implementou uma aplicação virtual de rede na rede virtual que está a peering com e criar rotas definidas pelo utilizador para encaminhar o tráfego através da aplicação virtual de rede. Se deixar esta caixa desmarcada (predefinição), não é possível fluxo de tráfego reencaminhado da rede virtual em modo de peering para esta rede virtual. Ao ativar esta capacidade permite o tráfego reencaminhado através do peering, não cria as rotas definidas pelo utilizador ou dispositivos de rede virtual. Rotas definidas pelo utilizador e os dispositivos de rede virtual são criados em separado. Saiba mais sobre [rotas definidas pelo utilizador](virtual-networks-udr-overview.md).
    - **Permitir que o trânsito do gateway:** esta caixa de verificação se tiver um gateway de rede virtual ligado a esta rede virtual e pretende permitir o tráfego de rede virtual em modo de peering para fluir pelo gateway. Por exemplo, esta rede virtual pode ser ligada a uma rede no local através de um gateway de rede virtual. Ao selecionar esta caixa permite que o tráfego de rede virtual em modo de peering para fluir pelo gateway ligado a esta rede virtual. Se selecionar esta caixa, a rede virtual em modo de peering não pode ter um gateway configurado. A rede virtual em modo de peering tem de ter o **gateway remoto utilize** caixa de verificação selecionado quando configurar o peering da outra rede virtual para esta rede virtual. Se deixar esta caixa desmarcada (predefinição), o tráfego dos fluxos ainda em modo de peering de rede virtual para esta rede virtual, mas não é possível fluxo através de um gateway de rede virtual ligado a esta rede virtual. Saiba mais sobre [gateways de rede virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti). 
    
        Não é possível ativar esta opção se estiver a peering uma rede virtual (Resource Manager) com uma rede virtual (clássica). Apesar do tráfego flua entre as duas redes virtuais, o tráfego (clássica) de rede virtual não é possível fluir através de um gateway de rede ligado à rede virtual (Resource Manager).

    - **Utilizar gateways remotos:** marcar esta caixa para permitir tráfego desta rede virtual para o fluxo através de um gateway de rede virtual ligado à rede virtual está a ser peering com. Por exemplo, a rede virtual que está a ser peering com tem um gateway de VPN ligado que permite a comunicação a uma rede no local.  Ao selecionar esta caixa permite tráfego desta rede virtual para o fluxo através do gateway VPN ligado à rede virtual em modo de peering. Se selecionar esta caixa, a rede virtual em modo de peering tem de ter um gateway de rede virtual ligado à mesma e tem de ter o **permitir trânsito do gateway** caixa de verificação marcada. Se deixar esta caixa desmarcada (predefinição), o tráfego de rede virtual em modo de peering ainda possam circular para esta rede virtual, mas não pode fluxo através de um gateway de rede virtual ligado a virtual nesta rede. 
    
        Não é possível ativar esta opção se estiver a peering uma rede virtual (Resource Manager) com uma rede virtual (clássica). Apesar do tráfego flua entre as duas redes virtuais, o tráfego de rede (Resource Manager) virtual não é possível fluir através de um gateway de rede ligado à rede virtual (clássica).
7. Clique em de **OK** botão para adicionar a sub-rede à rede virtual que selecionou.

### <a name="commands"></a>Comandos

|Ferramenta|Comando|
|---|---|
|CLI|[Criar AZ rede vnet peering](/cli/azure/network/vnet/peering#create?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Adicionar-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="scenarios"></a>Cenários

É criado um peering de rede virtual entre redes virtuais criadas com o mesmo ou com diferentes modelos de implementação que existam na mesma ou em diferentes subscrições. Conclua um tutorial passo a passo para um dos seguintes cenários:
 
|Modelo de implementação do Azure  | Subscrição  |
|---------|---------|
|Ambas com Resource Manager |[Mesma](virtual-network-create-peering.md)|
| |[Diferente](create-peering-different-subscriptions.md)|
|Uma com Resource Manager, outra com clássica     |[Mesma](create-peering-different-deployment-models.md)|
| |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

## <a name="view-or-change-peering-settings"></a>Ver ou alterar as definições de peering

1. Inicie sessão no [portal](https://portal.azure.com) com uma conta que é atribuída o necessário [funções ou permissões](#permissions).
2. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *redes virtuais*. Quando **redes virtuais** é apresentado nos resultados da pesquisa, clique no mesmo.
3. No **redes virtuais** painel apresentado, clique na rede virtual que pretende criar um peering para.
4. No painel que aparece para a rede virtual que selecionou, clique em **Peerings** no **definições** secção.
5. Clique em peering que pretende ver ou alterar as definições.
6. Altere a definição adequada. Leia sobre as opções para cada definição na [passo 6](#add-peering) de criar uma peering secção deste artigo. 

    >[!NOTE]
    >Antes de criar um peering, certifique-se tiver familiarized por si com o [requisitos e restrições](#requirements-and-constraints) e [as permissões necessárias](#permissions).
    >

7. Clique em **Guardar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[lista do AZ rede vnet peering](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para peerings de lista para uma rede virtual, [az rede Mostrar peering vnet](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para mostrar as definições para um peering específica, e [az de atualização de vnet peering rede](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#update) para alterar definições de peering.|
|PowerShell|[Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter as definições de peering de vista e [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) para alterar as definições.|

## <a name="delete-a-peering"></a>Eliminar um peering
Quando um peering é eliminado, o tráfego de rede virtual já não flui de para a rede virtual em modo de peering. Quando as redes virtuais implementadas através do Gestor de recursos em modo de peering, a cada rede virtual tem um peering à rede virtual. Embora a eliminar o peering de uma rede virtual desativa a comunicação entre as redes virtuais, não eliminar o peering da outra rede virtual. O estado de peering para o peering que existe na rede virtual é **desligado**. Não é possível recriar o peering até voltar a criar o peering na primeira rede virtual e o estado de peering para ambas as redes virtuais que as alterações *ligado*. 

Se pretender que as redes virtuais para comunicar por vezes, mas nem sempre, em vez de eliminar um peering, pode definir o **permitir o acesso de rede virtual** definição **desativado** em vez disso. Para saber como, leia o passo 6 do [criar um peering](#create-peering) secção deste artigo. Pode encontrar desativar e ativar o acesso de rede fácil eliminar e recriar peerings.

1. Inicie sessão no [portal](https://portal.azure.com) com uma conta que é atribuída o necessário [funções ou permissões](#permissions).
2. Na caixa que contém o texto *procurar recursos* na parte superior do portal do Azure, escreva *redes virtuais*. Quando **redes virtuais** é apresentado nos resultados da pesquisa, clique no mesmo.
3. No **redes virtuais** painel apresentado, clique na rede virtual que pretende eliminar um peering do.
4. No painel que aparece para a rede virtual que selecionou, clique em **Peerings** em **definições**.
5. Na lista de peerings que aparece no painel do peerings, clique com botão direito do peering que pretende eliminar, clique em **eliminar**, em seguida, **Sim** para eliminar o peering da primeira rede virtual.
6. Conclua os passos anteriores para eliminar o peering da outra rede virtual no peering de.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI|[eliminação do AZ rede vnet peering](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="requirements-and-constraints"></a>Os requisitos e limitações 

- As redes virtuais que o elemento tem de ter espaços de endereços IP sem sobreposição.
- Não é possível adicionar os espaços de endereços para ou eliminar espaços de endereços da rede virtual depois de uma rede virtual é executado o peering com outra rede virtual. Para adicionar ou remover espaços de endereços, eliminar o peering, adicionar ou remover os espaços de endereços, em seguida, recrie o peering. Para espaços de endereços para adicionar ou remover espaços de endereços das redes virtuais, leia o [criar, alterar ou eliminar de redes virtuais](virtual-network-manage-network.md#add-address-spaces) artigo.
- Pode elemento duas redes virtuais implementadas através do Gestor de recursos ou uma rede virtual implementado através do Resource Manager com uma rede virtual implementada através do modelo de implementação clássica. Não é possível elemento duas redes virtuais criadas através do modelo de implementação clássica. Se não estiver familiarizado com os modelos de implementação do Azure, leia o [modelos de implementação do Azure compreender](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo. Pode utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar duas redes virtuais criadas através do modelo de implementação clássica.
- Ao configurar o peering de duas redes virtuais criadas através do Resource Manager, tem de ser configurado um peering para cada rede virtual no peering. 
    - *Foi iniciada:* quando cria o peering de modo a segunda rede virtual da primeira rede virtual, o estado de peering é *iniciado*. 
    - *Ligado:* quando cria o peering da segunda rede virtual para a primeira rede virtual, o respetivo estado peering é *ligado*. Se visualizar o estado de peering para a primeira rede virtual, consulte o estado mudou de *iniciado* para *ligado*. O peering não é estabelecido êxito até que o estado de peering para ambos os peerings de rede virtual é *ligado*.
- Quando o peering de uma rede virtual criadas através do Resource Manager com uma rede virtual criadas através do modelo de implementação clássica, apenas configurar um peering da rede virtual implementado através do Resource Manager. Não é possível configurar o peering para uma rede virtual (clássica) ou entre duas redes virtuais implementadas através do modelo de implementação clássica. Quando cria o peering da rede virtual (Resource Manager) para a rede virtual (clássica), o estado de peering é *atualização*, em seguida, em breve alterações *ligado*.
- Um peering é estabelecido entre duas redes virtuais. Não são transitivas Peerings. Se criar peerings entre:
    - VirtualNetwork1 & VirtualNetwork2
    - VirtualNetwork2 & VirtualNetwork3

  Não há nenhum peering entre VirtualNetwork1 e VirtualNetwork3 através de VirtualNetwork2. Se pretender criar uma rede virtual peering entre VirtualNetwork1 e VirtualNetwork3, terá de criar um peering entre VirtualNetwork1 e VirtualNetwork3.
- Não é possível resolver os nomes de redes virtuais em modo de peering com a resolução do nome do Azure de predefinição. Para resolver os nomes de outras redes virtuais, tem de utilizar um servidor DNS personalizado. Para saber como configurar o próprio servidor DNS, leia o [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) artigo.
- Recursos em ambas as redes virtuais no peering de podem comunicar entre si com o mesmo largura de banda e latência como se estivessem na mesma rede virtual. Cada tamanho da máquina virtual tem no entanto a sua própria largura de banda de rede máxima. Para saber mais sobre a largura de banda de rede máxima para os diferentes tamanhos de máquinas virtuais, leia os artigos sobre os tamanhos de máquinas virtuais do [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou do [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Pode elemento redes virtuais implementadas através do Gestor de recursos que estão em subscrições idêntica ou diferentes.
- Pode elemento redes virtuais implementadas através de modelos de implementação diferentes que estão em subscrições idêntica ou diferentes (pré-visualização). 
- As subscrições que ambas as redes virtuais estão no tem de estar associadas ao mesmo inquilino do Azure Active Directory. Se ainda não tiver um inquilino do AD, pode rapidamente [criar um](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch). Pode utilizar um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para ligar duas redes virtuais que existam em diferentes subscrições associadas aos inquilinos diferentes do Active Directory.
- Uma rede virtual pode estar em modo de peering para outra rede virtual e também de estar ligada a outra rede virtual com um gateway de rede virtual do Azure. Quando estão ligados a redes virtuais através de peering e um gateway, o tráfego entre as redes virtuais flui através da configuração do peering, em vez do gateway.
- Existe uma cobrança nominal para o tráfego de entrada e de saída que utilize um peering de rede virtual. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/virtual-network).


## <a name="permissions"></a>Permissões

As contas que utilizar para criar um peering de rede virtual tem de ter a função necessária ou permissões. Por exemplo, se foram peering duas redes virtuais com o nome myVnetA e myVnetB, deve ser atribuída à conta a seguinte função mínima ou as permissões para cada rede virtual:
    
|Rede virtual|Modelo de implementação|Função|Permissões|
|---|---|---|---|
|myVnetA|Resource Manager|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássica|[Contribuidor de Rede Clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnetB|Resource Manager|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássica|[Contribuidor de Rede Clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções incorporadas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e atribuir permissões específicas para [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (apenas para o Resource Manager).

## <a name="next-steps"></a>Passos seguintes

Saiba como criar uma [topologia de rede hub and spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) 
