---
title: Adicionar, alterar ou eliminar uma sub-rede de rede virtual do Azure | Microsoft Docs
description: Saiba como adicionar, alterar ou eliminar uma sub-rede de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.openlocfilehash: 85ba6ef3e51c339a77eb9b4198c4f87e2a64cf09
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou eliminar uma sub-rede de rede virtual

Saiba como adicionar, alterar ou eliminar uma sub-rede de rede virtual. 

Se não estiver familiarizado com redes virtuais, antes de adicionar, alterar ou eliminar uma sub-rede, recomendamos que leia [descrição geral da rede Virtual do Azure](virtual-networks-overview.md) e [criar, alterar ou eliminar uma rede virtual](virtual-network-manage-network.md). Todos os recursos do Azure implementados numa rede virtual são implementados para uma sub-rede dentro de uma rede virtual. Normalmente, são criadas várias sub-redes dentro de uma rede virtual para:
- **Filtrar o tráfego entre sub-redes**. Pode aplicar a grupos de segurança de rede para sub-redes para filtrar o tráfego de rede de entrada e saída para todos os recursos (como máquinas virtuais) que estão na rede virtual. Para obter mais informações sobre como criar um grupo de segurança de rede, consulte [criar grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
- **Controlar o encaminhamento entre sub-redes**. Para que o tráfego é encaminhado automaticamente entre sub-redes, o Azure cria as rotas predefinidas. Pode substituir as rotas predefinidas do Azure, criar rotas definidas pelo utilizador. Para saber mais sobre as rotas definidas pelo utilizador, consulte o artigo [criar rotas definidas pelo utilizador](virtual-network-create-udr-arm-ps.md). 

Este artigo explica como adicionar, alterar e eliminar uma sub-rede para redes virtuais que foram criadas utilizando o modelo de implementação Azure Resource Manager.
 
## <a name="before"></a>Antes de começar

Antes de iniciar as tarefas que são descritas neste artigo, conclua os seguintes pré-requisitos:

- Se estiver a trabalhar com redes virtuais, recomendamos que reveja o exercício no [criar a sua primeira rede virtual do Azure](virtual-network-get-started-vnet-subnet.md). Neste exercício pode ajudar a tornar-se mais familiar com redes virtuais.
- Para saber mais sobre os limites de redes virtuais, reveja [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Inicie sessão no portal do Azure, a ferramenta de linha de comandos do Azure (CLI do Azure) ou o Azure PowerShell, utilizando a sua conta do Azure. Se não tiver uma conta do Azure, inscreva-se um [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se planeia utilizar comandos do PowerShell para concluir as tarefas descritas neste artigo, tem primeiro [instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que tem a versão mais recente dos cmdlets Azure PowerShell instaladas. Para obter ajuda para comandos do PowerShell nos exemplos, introduza `get-help <command> -full`.
- Se planeia utilizar comandos da CLI do Azure para concluir as tarefas descritas neste artigo, terá:
    - [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que tem a versão mais recente da CLI do Azure instalados.
    - Utilize a Shell de nuvem do Azure. Em vez de instalar a CLI e as respetivas dependências, pode utilizar a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Para utilizar a Shell de nuvem, clique na Shell de nuvem (**> _**) na parte superior do portal do Azure. 

  Para obter ajuda com os comandos da CLI do Azure, introduza `az <command> --help`.

## <a name="create-subnet"></a>Adicionar uma sub-rede

Para adicionar uma sub-rede:

1. Iniciar sessão para o [portal](https://portal.azure.com) com uma conta que está a atribuir permissões para a função de contribuinte de rede (no mínimo) para a sua subscrição. Para saber mais sobre a atribuição de funções e permissões a contas, consulte o artigo [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa de portal, introduza **redes virtuais**. Nos resultados da pesquisa, clique em **redes virtuais**.
3. No **redes virtuais** painel, clique na rede virtual que pretende adicionar uma sub-rede para.
4. No painel de rede virtual, clique em **sub-redes**.
5. Clique em **+ sub-rede**.
6. No **adicionar sub-rede** painel, introduza valores para os seguintes parâmetros:
    - **Nome**: O nome tem de ser exclusivo dentro da rede virtual.
    - **Intervalo de endereços**: O intervalo deve ser exclusivo num espaço de endereços da rede virtual. O intervalo não pode sobrepor-se com outros intervalos de endereços de sub-rede na rede virtual. O espaço de endereços tem de ser especificado utilizando a notação de Classless entre domínios encaminhamento CIDR (). Por exemplo, numa rede virtual com endereço espaço 10.0.0.0/16, poderá definir um espaço de endereços da sub-rede do 10.0.0.0/24. O intervalo mais pequeno, que pode especificar é /29, que fornece oito endereços IP da sub-rede. O primeiro e último endereço em cada sub-rede para a conformidade de protocolo de reservas de Azure. Três endereços adicionais estão reservados para utilização do serviço do Azure. Como resultado, que define uma sub-rede com/29 resultados de intervalo três utilizáveis endereços de IP na sub-rede de endereços. Se planear ligar uma rede virtual para um gateway de VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereço específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Pode alterar o intervalo de endereços depois da sub-rede é adicionada, sob condições específicas. Para saber como alterar um intervalo de endereços da sub-rede, consulte [alterar definições da sub-rede](#change-subnet) neste artigo.
    - **Grupo de segurança de rede**: Opcionalmente, pode associar um grupo de segurança de rede existente com a sub-rede para controlar o tráfego de rede de entrada e saída de filtragem para a sub-rede. O grupo de segurança de rede tem de existir na mesma subscrição e localização da rede virtual. Também deve ser criada utilizando o modelo de implementação Resource Manager. Para obter mais informações sobre como criar um grupo de segurança de rede, consulte [grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
    - **Tabela de rotas**: Opcionalmente, pode associar uma tabela de rota existente com a sub-rede para controlar o encaminhamento de tráfego de rede a outras redes. A tabela de rota tem de existir na mesma subscrição e localização da rede virtual. Também deve ser criada utilizando o modelo de implementação Resource Manager. Para obter mais informações sobre como criar tabelas de rota, consulte [rotas definidas pelo utilizador](virtual-network-create-udr-arm-ps.md).
    - **Os utilizadores**: pode controlar o acesso à sub-rede através da utilização de funções incorporadas ou as suas próprias funções personalizadas. Para saber mais sobre a atribuição de funções e os utilizadores acedam a sub-rede, consulte o artigo [utilizar atribuição de função para gerir o acesso aos seus recursos do Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Para adicionar a sub-rede da rede virtual que selecionou, clique em **OK**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI do Azure|[criar a sub-rede da vnet AZ rede](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Novo AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json), [AzureRmVirtualNetworkSubnetConfig adicionar](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Alterar definições da sub-rede

Pode alterar os grupos de segurança de rede, as tabelas de rotas e acesso de utilizador para uma sub-rede através da gestão de recursos que estão numa sub-rede. Para saber mais sobre estas definições no [adicionar uma sub-rede](#create-subnet), consulte o passo 6. Se pretender alterar o espaço de endereços de uma sub-rede, primeiro tem de eliminar quaisquer recursos que estão na sub-rede. Os passos que efetuar para eliminar um recurso variam consoante o recurso. Para saber como eliminar os recursos que estão em sub-redes, leia a documentação para cada tipo de recurso que pretende eliminar. Para alterar o intervalo de endereços para uma sub-rede:

1. Iniciar sessão para o [portal](https://portal.azure.com) com uma conta que está a atribuir permissões para a função de contribuinte de rede (no mínimo) para a sua subscrição. Para saber mais sobre a atribuição de funções e permissões a contas, consulte o artigo [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa de portal, introduza **redes virtuais**. Nos resultados da pesquisa, clique em **redes virtuais**.
3. No **redes virtuais** painel, clique na rede virtual para o qual pretende alterar um intervalo de endereços de sub-rede.
4. Clique na sub-rede para o qual pretende alterar o intervalo de endereços.
5. No painel da sub-rede, no **intervalo de endereços** box, introduza o intervalo de endereços novo. O intervalo deve ser exclusivo num espaço de endereços da rede virtual. O intervalo não pode sobrepor-se com outros intervalos de endereços de sub-rede na rede virtual. O espaço de endereços tem de ser especificado utilizando a notação CIDR. Por exemplo, numa rede virtual com endereço espaço 10.0.0.0/16, poderá definir um espaço de endereços da sub-rede do 10.0.0.0/24. O intervalo mais pequeno, que pode especificar é /29, que fornece oito endereços IP da sub-rede. O primeiro e último endereço em cada sub-rede para a conformidade de protocolo de reservas de Azure. Três endereços adicionais estão reservados para utilização do serviço do Azure. Como resultado, uma sub-rede/29 com o intervalo de endereços tem três endereços IP utilizáveis. Se planear ligar uma rede virtual para um gateway de VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereço específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Pode alterar o intervalo de endereços depois de criar a sub-rede, sob condições específicas. Para saber como alterar um intervalo de endereços da sub-rede, consulte [alterar definições da sub-rede](#change-subnet) neste artigo.
6. Clique em **Guardar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI do Azure|[atualização de sub-rede da vnet de rede de AZ](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Conjunto AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Eliminar uma sub-rede

Pode eliminar uma sub-rede apenas se existem não existem recursos na sub-rede. Se existirem recursos na sub-rede, tem de eliminar os recursos que estão na sub-rede antes de poder eliminar a sub-rede. Os passos que efetuar para eliminar um recurso variam consoante o recurso. Para saber como eliminar os recursos que estão em sub-redes, leia a documentação para cada tipo de recurso que pretende eliminar. Para eliminar uma sub-rede:

1. Iniciar sessão para o [portal](https://portal.azure.com) com uma conta que está a atribuir permissões para a função de contribuinte de rede (no mínimo) para a sua subscrição. Para saber mais sobre a atribuição de funções e permissões a contas, consulte o artigo [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa de portal, introduza **redes virtuais**. Nos resultados da pesquisa, clique em **redes virtuais**.
3. No **redes virtuais** painel, clique na rede virtual que pretende eliminar uma sub-rede do.
4. Em virtual de rede painel, em **definições**, clique em **sub-redes**.
5. Na lista de sub-redes que aparece no painel de sub-redes, clique com botão direito a sub-rede que pretende eliminar, clique em **eliminar**e, em seguida, clique em **Sim** para eliminar a sub-rede.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI do Azure|[AZ rede vnet elimine](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remover AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Passos seguintes

Para criar uma máquina virtual numa sub-rede, consulte [criar uma rede virtual e implementar as VMs na sub-rede](virtual-network-get-started-vnet-subnet.md#create-vms).
