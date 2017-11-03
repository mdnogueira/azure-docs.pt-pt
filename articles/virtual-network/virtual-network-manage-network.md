---
title: Criar, alterar ou eliminar uma Azure virtual network | Microsoft Docs
description: Saiba como criar, alterar ou eliminar uma rede virtual no Azure.
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
ms.openlocfilehash: 74aace2136136c25bc56327d38cfbab168265401
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-change-or-delete-a-virtual-network"></a>Criar, alterar ou eliminar uma rede virtual

Saiba como criar e eliminar uma rede virtual e alterar as definições, como servidores DNS e espaços de endereços IP, para uma rede virtual existente.

Uma rede virtual é uma representação da sua própria rede na nuvem. Uma rede virtual é um isolamento lógico da nuvem do Azure que está dedicado à sua subscrição do Azure. Para cada rede virtual que criou, pode:
- Escolha um espaço de endereço para atribuir. Um espaço de endereços é composta por um ou mais intervalos de endereços que são definidos utilizando a notação de Classless entre domínios encaminhamento CIDR (), como 10.0.0.0/16.
- Optar por utilizar o servidor DNS fornecidos pelo Azure ou utilize o seu próprio servidor DNS. Todos os recursos que estão ligados à rede virtual são atribuídos este servidor DNS para resolver nomes dentro da rede virtual.
- Segmentar a rede virtual em sub-redes, cada um com o seu próprio intervalo de endereços, dentro do espaço de endereços da rede virtual. Para saber como criar, alterar e eliminar as sub-redes, consulte o artigo [adicionar, alterar ou eliminar sub-redes](virtual-network-manage-subnet.md).

Este artigo explica como criar, alterar e eliminar redes virtuais utilizando o modelo de implementação Azure Resource Manager.

## <a name="before"></a>Antes de começar

Antes de iniciar as tarefas que são descritas neste artigo, conclua os seguintes pré-requisitos:

- Se estiver a trabalhar com redes virtuais, recomendamos que reveja o exercício no [criar a sua primeira rede virtual do Azure](virtual-network-get-started-vnet-subnet.md). Neste exercício pode ajudar a tornar-se mais familiar com redes virtuais.
- Para saber mais sobre os limites de redes virtuais, reveja [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Inicie sessão no portal do Azure, a ferramenta de linha de comandos do Azure (CLI do Azure) ou o Azure PowerShell, utilizando a sua conta do Azure. Se não tiver uma conta do Azure, inscreva-se um [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se planeia utilizar comandos do PowerShell para concluir as tarefas descritas neste artigo, tem primeiro [instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que tem a versão mais recente dos cmdlets Azure PowerShell instaladas. Para obter ajuda para comandos do PowerShell nos exemplos, introduza `get-help <command> -full`.
- Se planeia utilizar comandos da CLI do Azure para concluir as tarefas descritas neste artigo, tem primeiro [instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que tem a versão mais recente da CLI do Azure instalados. Para obter ajuda com os comandos da CLI do Azure, introduza `az <command> --help`.


## <a name="create-vnet"></a>Criar uma rede virtual

Para criar uma rede virtual:

1. Iniciar sessão para o [portal](https://portal.azure.com) com uma conta que está a atribuir permissões para a função de contribuinte de rede (no mínimo) para a sua subscrição. Para saber mais sobre a atribuição de funções e permissões a contas, consulte o artigo [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Clique em **novo** > **redes** > **rede Virtual**.
3. No **rede Virtual** painel, no **selecionar um modelo de implementação** caixa, deixe **Resource Manager** selecionada e, em seguida, clique em **criar**.
4. No **criar rede virtual** painel, introduza ou selecione os valores para as seguintes definições, em seguida, clique em **criar**:
    - **Nome**: O nome tem de ser exclusivo no [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) que selecionar para criar a rede virtual. Não é possível alterar o nome depois da criação da rede virtual. Pode criar várias redes virtuais ao longo do tempo. Para sugestões de atribuição de nomes, consulte [convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Seguir uma convenção de nomenclatura pode ajudar a tornar mais fácil de gerir várias redes virtuais.
    - **Espaço de endereços**: Especifique o espaço de endereços em notação CIDR. O espaço de endereços que define pode ser public ou private (RFC 1918). Se definir o espaço de endereços como público ou privado, o espaço de endereços é acessível apenas a partir de dentro da rede virtual, redes virtuais interligados e quaisquer redes no local que tiver estabelecido ligação à rede virtual. Não é possível adicionar os seguintes espaços de endereços:
        - 224.0.0.0/4 (Multicast)
        - 255.255.255.255/32 (difusão)
        - 127.0.0.0/8 (Loopback)
        - 169.254.0.0/16 (locais)
        - 168.63.129.16/32 (DNS interno)

      Embora, pode definir apenas um espaço de endereços quando criar a rede virtual, pode adicionar mais espaços de endereços depois da criação da rede virtual. Para saber como adicionar um espaço de endereço a uma rede virtual existente, consulte [adicionar ou remover um espaço de endereço](#add-address-spaces) neste artigo.

      >[!WARNING]
      >Se uma rede virtual tem espaços de endereços que se sobrepõe a outra rede virtual local ou na rede, as duas redes não podem ser ligadas. Antes de definir um espaço de endereços, considere se pretenda ligar a rede virtual com outras redes virtuais ou redes no local no futuro.
      >
      >

    - **Nome da sub-rede**: O nome de sub-rede tem de ser exclusivo dentro da rede virtual. Não é possível alterar o nome de sub-rede depois de criar a sub-rede. O portal requer que defina uma sub-rede quando cria uma rede virtual, apesar de uma rede virtual não é necessária ter quaisquer sub-redes. No portal, pode definir apenas uma sub-rede quando criar uma rede virtual. Pode adicionar mais sub-redes para a rede virtual mais tarde, depois da criação da rede virtual. Para adicionar uma sub-rede para uma rede virtual, consulte [criar uma sub-rede](virtual-network-manage-subnet.md#create-subnet) no [criar, alterar ou eliminar sub-redes](virtual-network-manage-subnet.md). Pode criar uma rede virtual que tem várias sub-redes utilizando a CLI do Azure ou o PowerShell.

      >[!TIP]
      >Por vezes, administradores criar sub-redes diferentes filtrar ou controlar o encaminhamento de tráfego entre as sub-redes. Antes de definir sub-redes, considere como poderá pretender filtrar e encaminhar o tráfego entre as sub-redes. Para saber mais sobre a filtragem de tráfego entre sub-redes, consulte [grupos de segurança de rede](virtual-networks-nsg.md). Azure automaticamente tráfego de rotas entre sub-redes, mas pode substituir as rotas predefinidas do Azure. Para saber como substituir o encaminhamento de tráfego de sub-rede predefinido do Azure, consulte [rotas definidas pelo utilizador](virtual-networks-udr-overview.md).
      >

    - **Intervalo de endereços da sub-rede**: O intervalo tem de estar no espaço de endereço que introduziu para a rede virtual. O intervalo mais pequeno, que pode especificar é /29, que fornece oito endereços IP da sub-rede. O primeiro e último endereço em cada sub-rede para a conformidade de protocolo de reservas de Azure. Três endereços adicionais estão reservados para utilização do serviço do Azure. Como resultado, uma rede virtual com um intervalo de endereços de sub-rede de /29 tem três apenas endereços IP utilizáveis. Se planear ligar uma rede virtual para um gateway de VPN, tem de criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereço específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Pode alterar o intervalo de endereços depois de criar a sub-rede, sob condições específicas. Para saber como alterar um intervalo de endereços da sub-rede, consulte [alterar definições da sub-rede](#change-subnet) no [adicionar, alterar ou eliminar sub-redes](virtual-network-manage-subnet.md).
    - **Subscrição**: selecione uma [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Não é possível utilizar a mesma rede virtual em mais do que uma subscrição do Azure. No entanto, pode ligar uma rede virtual numa subscrição a redes virtuais no outras subscrições. Para ligar redes virtuais em diferentes subscrições, utilize o peering de rede virtual ou de Gateway de VPN do Azure. Qualquer recurso do Azure que ligam à rede virtual tem de ser na mesma subscrição que a rede virtual.
    - **Grupo de recursos**: selecione um existente [grupo de recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) ou crie um novo. Um recurso do Azure que ligam à rede virtual pode estar no mesmo grupo de recursos de rede virtual ou num grupo de recursos diferente.
    - **Localização**: selecione um Azure [localização](https://azure.microsoft.com/regions/), também conhecido como uma região. Uma rede virtual pode ter apenas uma localização do Azure. No entanto, pode ligar uma rede virtual numa localização a uma rede virtual noutra localização, utilizando um gateway de VPN. Qualquer recurso do Azure que ligam à rede virtual tem de ser na mesma localização que a rede virtual.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI do Azure|[Criar AZ rede vnet](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Novo-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Ver redes virtuais e definições

Para ver as definições e redes virtuais:

1. Iniciar sessão para o [portal](https://portal.azure.com) com uma conta que está a atribuir permissões para a função de contribuinte de rede (no mínimo) para a sua subscrição. Para saber mais sobre a atribuição de funções e permissões a contas, consulte o artigo [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa de portal, introduza **redes virtuais**. Nos resultados da pesquisa, clique em **redes virtuais**.
3. No **redes virtuais** painel, clique na rede virtual que pretende ver para as definições.
4. As seguintes definições estão listadas no painel da rede virtual que selecionou:
    - **Descrição geral**: fornece informações sobre a rede virtual, incluindo o espaço de endereços e servidores DNS. A seguinte captura de ecrã mostra as definições de descrição geral de uma rede virtual denominada **MyVNet**:

        ![Descrição geral da interface de rede](./media/virtual-network-manage-network/vnet-overview.png)

      No **descrição geral** painel, pode mover uma rede virtual para um grupo de subscrição ou recurso diferente. Para saber como mover uma rede virtual, consulte [mover recursos para um grupo de recursos diferente ou uma subscrição](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). O artigo apresenta uma lista de pré-requisitos e como mover recursos utilizando o portal do Azure, o PowerShell e a CLI do Azure. Tem de mover todos os recursos que estão ligados à rede virtual com a rede virtual.
    - **Espaço de endereços**: os espaços de endereços que estão atribuídos à rede virtual são listados. Para saber como adicionar e remover um espaço de endereços, concluir os passos [adicionar ou remover um espaço de endereço](#address-spaces) neste artigo.
    - **Dispositivos ligados**: são listados todos os recursos que estão ligados à rede virtual. Captura de ecrã anterior, três interfaces de rede e um balanceador de carga estão ligados à rede virtual. Quaisquer recursos novos que criam e ligar à rede virtual são listados. Se eliminar um recurso que foi ligado à rede virtual, já não aparece na lista.
    - **Sub-redes**: é apresentada uma lista de sub-redes existentes na rede virtual. Para saber como adicionar e remover uma sub-rede, consulte o artigo [criar uma sub-rede](virtual-network-manage-subnet.md#create-subnet) e [eliminar uma sub-rede](virtual-network-manage-subnet.md#delete-subnet) no [adicionar, alterar ou eliminar sub-redes](virtual-network-manage-subnet.md).
    - **Servidores DNS**: pode especificar se o servidor DNS interno do Azure ou um servidor DNS personalizado fornece resolução de nomes para os dispositivos que estão ligados à rede virtual. Quando cria uma rede virtual com o portal do Azure, os servidores DNS do Azure são utilizadas para a resolução do nome dentro de uma rede virtual, por predefinição. Para modificar os servidores DNS, execute os passos do [adicionar, alterar ou remover um servidor DNS](#dns-servers) neste artigo.
    - **Peerings**: se existirem peerings existente na subscrição, estes são apresentados aqui. Pode ver as definições para peerings existente, ou criar, alterar ou eliminar peerings. Para saber mais sobre peerings, consulte o artigo [peering de rede Virtual](virtual-network-peering-overview.md).
    - **Propriedades**: apresenta as definições de sobre a rede virtual, incluindo o ID de recurso da rede virtual e a subscrição do Azure está a ser.
    - **Diagrama**: O diagrama proporciona uma representação visual de todos os dispositivos que estejam ligados à rede virtual. O diagrama tem algumas informações chaves sobre os dispositivos. Para gerir um dispositivo nesta vista, no diagrama, clique no dispositivo.
    - **Definições comuns de Azure**: para saber mais sobre as definições do Azure comuns, consulte as seguintes informações:
        *   [Registo de atividades](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Controlo de acesso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Etiquetas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Bloqueia](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Script de automatização](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI do Azure|[Mostrar de vnet AZ rede](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="add-address-spaces"></a>Adicionar ou remover um espaço de endereço

Pode adicionar e remover espaços de endereços de rede virtual. Um espaço de endereços tem de ser especificado em notação CIDR e não pode sobrepor-se com outros espaços de endereços na mesma rede virtual. Os espaços de endereços que define podem ser public ou private (RFC 1918). Se definir o espaço de endereços como público ou privado, o espaço de endereços é acessível apenas a partir de dentro da rede virtual, redes virtuais interligados e quaisquer redes no local que tiver estabelecido ligação à rede virtual. Não é possível adicionar os seguintes espaços de endereços:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (difusão)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (locais)
- 168.63.129.16/32 (DNS interno)

Para adicionar ou remover um espaço de endereço:

1. Iniciar sessão para o [portal](https://portal.azure.com) com uma conta que está a atribuir permissões para a função de contribuinte de rede (no mínimo) para a sua subscrição. Para saber mais sobre a atribuição de funções e permissões a contas, consulte o artigo [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa de portal, introduza **redes virtuais**. Nos resultados da pesquisa, selecione **redes virtuais**.
3. No **redes virtuais** painel, clique na rede virtual para o qual pretende adicionar ou remover um espaço de endereços.
4. Em virtual de rede painel, em **definições**, clique em **espaço de endereços**.
5. No painel de espaço de endereços, efetue uma das seguintes opções:
    - **Adicionar um espaço de endereço**: introduza o novo espaço de endereço. O espaço de endereços não pode sobrepor-se com um espaço de endereço existente que está definido para a rede virtual.
    - **Remover um espaço de endereço**: faça duplo clique um espaço de endereços e, em seguida, clique em **remover**. Se existir uma sub-rede no espaço de endereços, não é possível remover o espaço de endereços. Para remover um espaço de endereços, tem primeiro de eliminar quaisquer sub-redes (e quaisquer recursos que estão ligados a sub-redes) que existe no espaço de endereços.
6. Clique em **Guardar**.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI do Azure|Apenas o Gestor de recursos|[atualização do AZ rede vnet](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Adicionar, alterar ou remover um servidor DNS

Todas as VMs que estão ligadas ao registar a rede virtual com os servidores DNS que especificar para a rede virtual. Também utilizam o servidor DNS especificado para a resolução do nome. Cada interface de rede (NIC) numa VM pode ter as suas próprias definições de servidor DNS. Se um NIC tem as suas próprias definições de servidor DNS, elas substituirão as definições do servidor DNS para a rede virtual. Para saber mais sobre as definições de DNS de NIC, consulte o artigo [interface tarefas e definições de rede](virtual-network-network-interface.md#change-dns-servers). Para mais informações sobre resolução de nomes para VMs e instâncias de função nos serviços de nuvem do Azure, consulte o artigo [a resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md). Para adicionar, alterar ou remover um servidor DNS:

1. Iniciar sessão para o [portal](https://portal.azure.com) com uma conta que está a atribuir permissões para a função de contribuinte de rede (no mínimo) para a sua subscrição. Para saber mais sobre a atribuição de funções e permissões a contas, consulte o artigo [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa de portal, escreva **redes virtuais**. Nos resultados da pesquisa, selecione **redes virtuais**.
3. No **redes virtuais** painel, clique na rede virtual que pretende alterar as definições de DNS.
4. Em virtual de rede painel, em **definições**, clique em **servidores DNS**.
5. Selecione uma das seguintes opções no painel de lista de servidores DNS:
    - **Predefinição (fornecidos pelo Azure)**: todos os nomes de recursos e endereços IP privados estão registados automaticamente para os servidores de DNS do Azure. Pode resolver nomes entre todos os recursos que estão ligados à mesma rede virtual. Não é possível utilizar esta opção para resolver nomes através de redes virtuais. Para resolver nomes através de redes virtuais, tem de utilizar um servidor DNS personalizado.
    - **Personalizada**: pode adicionar um ou mais servidores, até ao limite do Azure para uma rede virtual. Para saber mais sobre os limites do servidor DNS, consulte o artigo [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Existem as seguintes opções:
        - **Adicionar um endereço**: adiciona-à sua lista de servidores DNS de rede virtual. Esta opção também regista o servidor DNS com o Azure. Se já registou um servidor DNS com o Azure, pode selecionar esse servidor DNS na lista.
        - **Remover um endereço**: junto do servidor que pretende remover, clique em **X**. A eliminação do servidor remove o servidor apenas a partir desta lista de rede virtual. O servidor DNS permanece registado no Azure para as outras redes virtuais a utilizar.
        - **Reordenar os endereços do servidor DNS**: é importante verificar que lista os servidores DNS na ordem correta para o seu ambiente. Listas de servidor DNS são utilizadas pela ordem que estão especificadas. Não funcionam como uma configuração round robin. Se o primeiro servidor DNS na lista pode ser acedido, o cliente utiliza esse servidor DNS, independentemente se o servidor DNS está a funcionar corretamente. Remova todos os servidores DNS que estão listados e, em seguida, adicioná-las novamente na ordem em que pretende.
        - **Alterar um endereço**: realce o servidor DNS na lista e, em seguida, introduza o novo nome.
6. Clique em **Guardar**.
7. Reinicie as VMs que estejam ligadas à rede virtual, pelo que são atribuídas as novas definições de servidor DNS. VMs continuam a utilizar as definições de DNS atuais até que o se forem reiniciados.

**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI do Azure|[atualização do AZ rede vnet](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Eliminar uma rede virtual

É possível eliminar uma rede virtual só se existem não existem recursos ligados ao mesmo. Se existirem recursos ligados a nenhuma sub-rede na rede virtual, tem primeiro de eliminar os recursos que estão ligados a todas as sub-redes na rede virtual. Os passos que efetuar para eliminar um recurso variam consoante o recurso. Para saber como eliminar os recursos que estão ligados a sub-redes, leia a documentação para cada tipo de recurso que pretende eliminar. Para eliminar uma rede virtual:

1. Iniciar sessão para o [portal](https://portal.azure.com) com uma conta que seja atribuídas (no mínimo) permissões para a função de contribuinte de rede para a sua subscrição. Para saber mais sobre a atribuição de funções e permissões a contas, consulte o artigo [funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa de pesquisa de portal, introduza **redes virtuais**. Nos resultados da pesquisa, clique em **redes virtuais**.
3. No **redes virtuais** painel, selecione a rede virtual que pretende eliminar.
4. No painel de rede virtual confirmar que não há dispositivos que não estão ligados à rede virtual, em **definições**, clique em **dispositivos ligados**. Se existirem dispositivos ligados, tem de as eliminar antes de poder eliminar a rede virtual. Se não houver nenhuma dispositivos ligados, clique em **descrição geral**.
5. Na parte superior do painel, clique em de **eliminar** ícone.
6. Para confirmar a eliminação da rede virtual, clique em **Sim**.


**Comandos**

|Ferramenta|Comando|
|---|---|
|CLI do Azure|[rede Azure vnet eliminar](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Passos seguintes

- Para criar uma VM e, em seguida, ligue-o a uma rede virtual, consulte [criar uma rede virtual e ligar as VMs](virtual-network-get-started-vnet-subnet.md#create-vms).
- Para filtrar o tráfego de rede entre sub-redes dentro de uma rede virtual, consulte [criar grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
- Para o elemento de uma rede virtual a outra rede virtual, consulte o artigo [criar um peering de rede virtual](virtual-network-create-peering.md#portal).
- Para saber mais sobre as opções para ligar uma rede virtual a uma rede no local, consulte [sobre o Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
