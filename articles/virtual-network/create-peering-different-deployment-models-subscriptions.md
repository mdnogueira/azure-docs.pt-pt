---
title: "Criar um peering de rede virtual do Azure - modelos de implementação diferentes - diferentes subscrições | Microsoft Docs"
description: "Saiba como criar uma rede virtual peering entre redes virtuais criadas através de modelos de implementação do Azure diferentes que existam em diferentes subscrições do Azure."
services: virtual-network
documentationcenter: 
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
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: 441bb0a269de400c82abc083118f5e0642523640
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Criar um peering de rede virtual - diferentes modelos de implementação e as subscrições

Neste tutorial, pode aprende a criar uma rede virtual peering entre redes virtuais criadas através de modelos de implementação diferentes. Existem redes virtuais em diferentes subscrições. Peering dois recursos de permite redes virtuais em redes virtuais diferentes para comunicar entre si com o mesmo largura de banda e latência dado a entender, os recursos foram na mesma rede virtual. Saiba mais sobre [peering de rede Virtual](virtual-network-peering-overview.md).

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão no mesmo, ou outro, subscrições e que [modelo de implementação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através do. Saiba como criar uma rede virtual peering noutros cenários, clicando no cenário da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[O Gestor de recursos](virtual-network-create-peering.md) |mesmo|
|[O Gestor de recursos](create-peering-different-subscriptions.md) |Diferentes|
|[Um Gestor de recursos, um clássico](create-peering-different-deployment-models.md) |mesmo|

Não é possível criar uma rede virtual peering entre duas redes virtuais implementadas através do modelo de implementação clássica. A capacidade de elemento redes virtuais criadas através de modelos de implementação diferentes que existam em diferentes subscrições está atualmente em pré-visualização. Para concluir este tutorial, tem de primeiro [registar](#register) utilizar a capacidade. Este tutorial utiliza redes virtuais que existem na mesma região. A capacidade de elemento redes virtuais em diferentes regiões também está em pré-visualização. Para utilizar essa capacidade, tem também [registar](#register) para o mesmo. Os dois recursos são independentes. Para concluir este tutorial, tem de registar apenas para a capacidade de elemento redes virtuais criadas através de modelos de implementação diferentes que existam em diferentes subscrições. 

Ao criar uma rede virtual peering entre redes virtuais que existam em diferentes subscrições, as subscrições têm de ter ambos associadas ao mesmo inquilino do Azure Active Directory. Se ainda não tiver um inquilino do Azure Active Directory, pode rapidamente [criar um](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch). 

A capacidade de ligar redes virtuais criados através do modelo de implementação, modelos de implementação diferentes, diferentes regiões ou subscrições associadas ao mesmo ou outro Azure Active Directory inquilinos com um Azure [doVPNGateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) na versão de pré-visualização e não necessita de registo.

Pode utilizar o [portal do Azure](#portal), o Azure [interface de linha de comandos](#cli) (CLI), ou do Azure [PowerShell](#powershell) para criar um peering de rede virtual. Clique em qualquer uma das ligações de ferramenta anterior para ir diretamente para os passos para criar um peering de rede virtual com a ferramenta de escolha.

## <a name="portal"></a>Criar peering - portal do Azure

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para registo fora do portal e ignore os passos para atribuir permissões de outro utilizador para as redes virtuais. Antes de concluir qualquer um dos seguintes passos, tem de se registar para a pré-visualização. Para registar, concluir os passos a [registar-se para a pré-visualização](#register) secção deste artigo. Os passos restantes falharem se não registar ambas as subscrições para a pré-visualização.
 
1. Inicie sessão no [portal do Azure](https://portal.azure.com) como UserA. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Consulte o [permissões](#permissions) secção deste artigo para obter mais detalhes.
2. Clique em **+ novo**, clique em **redes**, em seguida, clique em **rede Virtual**.
3. No **criar rede virtual** painel, introduza, ou selecione os valores para as seguintes definições, em seguida, clique em **criar**:
    - **Nome**: *myVnetA*
    - **Espaço de endereços**: *10.0.0.0/16*
    - **Nome da sub-rede**: *predefinido*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Subscrição**: selecione a subscrição A.
    - **Grupo de recursos**: selecione **criar nova** e introduza *myResourceGroupA*
    - **Localização**: *EUA leste*
4. No **procurar recursos** caixa na parte superior do portal, tipo *myVnetA*. Clique em **myVnetA** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnetA** rede virtual.
5. No **myVnetA** painel apresentado, clique em **(IAM) do controlo de acesso** na vertical lista das opções no lado esquerdo do painel.
6. No **myVnetA - controlo de acesso (IAM)** painel apresentado, clique em **+ adicionar**.
7. No **adicionar permissões** painel que aparece, selecione **contribuinte de rede** no **função** caixa.
8. No **selecione** caixa, selecione o utilizador b ou escreva o endereço de correio eletrónico do User-b para procurá-lo. A lista de utilizadores apresentados é do mesmo inquilino do Azure Active Directory como a rede virtual que está a configurar o peering para. Clique em UserB quando é apresentado na lista.
9. Clique em **Guardar**.
10. Termine sessão no portal como UserA, em seguida, inicie sessão como utilizador b.
11. Clique em **+ novo**, tipo *rede Virtual* no **procurar no Marketplace** caixa, em seguida, clique em **rede Virtual** nos resultados da pesquisa.
12. No **rede Virtual** painel que aparece, selecione **clássico** no **selecionar um modelo de implementação** caixa, em seguida, clique em **criar**.
13.   A criar rede virtual (clássica) caixa que aparece, introduza os seguintes valores:

    - **Nome**: *myVnetB*
    - **Espaço de endereços**: *10.1.0.0/16*
    - **Nome da sub-rede**: *predefinido*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Subscrição**: selecione a subscrição B.
    - **Grupo de recursos**: selecione **criar nova** e introduza *myResourceGroupB*
    - **Localização**: *EUA leste*

14. No **procurar recursos** caixa na parte superior do portal, tipo *myVnetB*. Clique em **myVnetB** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnetB** rede virtual.
15. No **myVnetB** painel apresentado, clique em **propriedades** na vertical lista das opções no lado esquerdo do painel. Copiar o **ID de recurso**, que é utilizado num passo posterior. O ID de recurso é semelhante ao seguinte exemplo: /subscriptions/{targetsubscriptionid}/resourcegroups/{targetresourcegroupname}<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Concluir os passos 5 9 para myVnetB, introduzir **UserA** no passo 8.
17. Termine sessão no portal como UserB e inicie sessão como utilizador.
18. No **procurar recursos** caixa na parte superior do portal, tipo *myVnetA*. Clique em **myVnetA** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnet** rede virtual.
19. Clique em **myVnetA**.
20. No **myVnetA** painel apresentado, clique em **Peerings** na vertical lista das opções no lado esquerdo do painel.
21. No **myVnetA - Peerings** painel que antes eram, clique em **+ adicionar**
22. No **adicionar peering** painel apresentado, introduza, ou selecione as seguintes opções, em seguida, clique em **OK**:
     - **Nome**: *myVnetAToMyVnetB*
     - **Modelo de implementação de rede virtual**: selecione **clássico**.
     - **Sei o ID de recurso**: esta caixa de verificação.
     - **ID de recurso**: introduza o ID de recurso do myVnetB do passo 15.
     - **Permitir o acesso de rede virtual:** Certifique-se de que **ativado** está selecionada.
    Não existem outras definições são utilizadas neste tutorial. Para saber mais sobre todas as definições de peering, leia [gerir peerings de rede virtual](virtual-network-manage-peering.md#create-a-peering).
23. Depois de clicar em **OK** no passo anterior, o **adicionar peering** painel fecha e ver o **myVnetA - Peerings** painel novamente. Após alguns segundos, o peering que criou é apresentado no painel. **Ligado** está listado no **PEERING estado** coluna para o **myVnetAToMyVnetB** peering é criado. Agora é estabelecido o peering. Não é necessário para o elemento da rede virtual (clássica) para a rede virtual (Resource Manager).

    Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

24. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
25. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos a [eliminar recursos](#delete-portal) secção deste artigo.

## <a name="cli"></a>Criar peering - CLI do Azure

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para registo no Azure e remover as linhas de script que criar atribuições de funções de utilizador. Substitua UserA@azure.com e UserB@azure.com em todas os scripts seguintes com os nomes de utilizador que está a utilizar para UserA e UserB. 

Antes de concluir qualquer um dos seguintes passos, tem de se registar para a pré-visualização. Para registar, concluir os passos a [registar-se para a pré-visualização](#register) secção deste artigo. Os passos restantes falharem se não registar ambas as subscrições para a pré-visualização.

1. [Instalar](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 1.0 de CLI do Azure para criar a rede virtual (clássica).
2. Abra uma sessão CLI e inicie sessão no Azure como UserB utilizando o `azure login` comando.
3. Execute o CLI no modo de gestão do serviço introduzindo o `azure config mode asm` comando.
4. Introduza o seguinte comando para criar a rede virtual (clássica):
 
    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
5. Os passos restantes tem de ser concluídos com um bash shell com a CLI do Azure 2.0.4 ou posterior [instalado](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), ou utilizando a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Clique em de **experimente** clique no botão nos scripts que se seguem, que abre uma Shell de nuvem que iniciar sessão na sua conta do Azure. Para obter as opções de execução bash scripts CLI num cliente Windows, consulte [com a CLI do Azure no Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
6. Copie o seguinte script para um editor de texto no seu PC. Substitua `<SubscriptionB-Id>` com o ID de subscrição. Se não souber o Id de subscrição, introduza o `az account show` comando. O valor para **id** na saída é o ID de subscrição Copie o script modificado, cole-a à sua sessão CLI 2.0 e, em seguida, prima `Enter`. 

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Quando criou a rede virtual (clássica) no passo 4, o Azure criar a rede virtual no *predefinido redes* grupo de recursos.
7. Inicie sessão UserB fora do Azure e inicie sessão como UserA no 2.0 a CLI.
8. Crie um grupo de recursos e uma rede virtual (Resource Manager). Copie o seguinte script, cole-a à sua sessão CLI e, em seguida, prima `Enter`. 

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

9. Crie uma rede virtual peering entre duas redes virtuais criadas através de modelos de implementação diferentes. Copie o seguinte script para um editor de texto no seu PC. Substitua `<SubscriptionB-id>` com o ID de subscrição Se não souber o Id de subscrição, introduza o `az account show` comando. O valor para **id** na saída é o ID de subscrição Azure criar a rede virtual (clássica) que criou no passo 4 num grupo de recursos com o nome *predefinido redes*. Cole o script modificado na sua sessão CLI e, em seguida, prima `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

10. Depois do script executa, reveja o peering da rede virtual (Resource Manager). Copie o seguinte script e, em seguida, cole-o na sua sessão do CLI:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    O resultado mostra **ligado** no **PeeringState** coluna.

    Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

11. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
12. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos [eliminar recursos](#delete-cli) neste artigo.

## <a name="powershell"></a>Criar peering - PowerShell

Este tutorial utiliza contas diferentes para cada subscrição. Se estiver a utilizar uma conta que tenha permissões para ambas as subscrições, pode utilizar a mesma conta para todos os passos, ignore os passos para registo no Azure e remover as linhas de script que criar atribuições de funções de utilizador. Substitua UserA@azure.com e UserB@azure.com em todas os scripts seguintes com os nomes de utilizador que está a utilizar para UserA e UserB. 

Antes de concluir qualquer um dos seguintes passos, tem de se registar para a pré-visualização. Para registar, concluir os passos a [registar-se para a pré-visualização](#register) secção deste artigo. Os passos restantes falharem se não registar ambas as subscrições para a pré-visualização.

1. Instale a versão mais recente do PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) e [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) módulos. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, inicie sessão na subscrição do User-b como UserB, introduzindo o `Add-AzureAccount` comando.
4. Para criar uma rede virtual (clássica) com o PowerShell, tem de criar um novo ou modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md). O ficheiro deve incluir o seguinte **VirtualNetworkSite** elemento da rede virtual que utilizou neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importar um ficheiro de configuração foi alterada de rede pode fazer com que as alterações às redes virtuais existentes (clássica) na sua subscrição. Certifique-se apenas a adicionar a rede virtual anterior e que não alterar ou remover quaisquer redes virtuais existentes da sua subscrição. 

5. Inicie sessão na subscrição do User-b como UserB utilizar comandos do Gestor de recursos, introduzindo o `login-azurermaccount` comando.
6. Atribuir permissões de utilizador para a rede virtual cópia B. o seguinte script para um editor de texto no seu PC e substituir `<SubscriptionB-id>` com o ID de subscrição B. Se não souber o Id de subscrição, introduza o `Get-AzureRmSubscription` comando para vê-la. O valor para **Id** na saída devolvida é o ID de subscrição. Azure criar a rede virtual (clássica) que criou no passo 4 num grupo de recursos com o nome *predefinido redes*. Ao executar o script, copie o script modificado, cole-a para o PowerShell e, em seguida, prima `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Termine sessão no Azure como UserB e inicie sessão na subscrição do User-como UserA, introduzindo o `login-azurermaccount` comando. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Consulte o [permissões](#permissions) secção deste artigo para obter mais detalhes.
8. Criar a rede virtual (Resource Manager) ao copiar o script seguinte, colando-para o PowerShell e, em seguida, premindo `Enter`:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Atribua permissões de UserB ao myVnetA. Copie o seguinte script para um editor de texto no seu PC e substituir `<SubscriptionA-Id>` com o ID de subscrição A. Se não souber o Id de subscrição, introduza o `Get-AzureRmSubscription` comando para vê-la. O valor para **Id** na saída devolvida é o ID de subscrição. Cole a versão modificada do script do PowerShell e, em seguida, prima `Enter` para executá-lo.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Copie o seguinte script para um editor de texto no seu PC e substitua `<SubscriptionB-id>` com o ID de subscrição B. A ponto myVnetA para myVNetB, copie o script modificado, cole-a para o PowerShell e, em seguida, prima `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Ver o estado do peering de myVnetA ao copiar o script seguinte, colando PowerShell e premir `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O estado é **ligado**. Alterar para **ligado** depois de configurar o peering para myVnetA myVnetB.

    Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

12. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
13. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos [eliminar recursos](#delete-powershell) neste artigo.

## <a name="permissions"></a>Permissões

As contas que utilizar para criar um peering de rede virtual tem de ter a função necessária ou permissões. Por exemplo, se foram peering duas redes virtuais com o nome myVnetA e myVnetB, deve ser atribuída à conta a seguinte função mínima ou as permissões para cada rede virtual:
    
|Rede virtual|Modelo de implementação|Função|Permissões|
|---|---|---|---|
|myVnetA|Resource Manager|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássica|[Contribuidor de Rede Clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnetB|Resource Manager|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássica|[Contribuidor de Rede Clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções incorporadas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e atribuir permissões específicas para [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (apenas para o Resource Manager).

## <a name="delete"></a>Eliminar recursos
Quando tiver terminado neste tutorial, pode querer eliminar os recursos que criou no tutorial, pelo que não implicar custos de utilização. Também eliminar um grupo de recursos elimina todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa de portal, introduza **myResourceGroupA**. Nos resultados da pesquisa, clique em **myResourceGroupA**.
2. No **myResourceGroupA** painel, clique em de **eliminar** ícone.
3. Para confirmar a eliminação no **tipo o nome de grupo de recursos** box, introduza **myResourceGroupA**e, em seguida, clique em **eliminar**.
4. No **procurar recursos** caixa na parte superior do portal, tipo *myVnetB*. Clique em **myVnetB** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myVnetB** rede virtual.
5. No **myVnetB** painel, clique em **eliminar**.
6. Para confirmar a eliminação, clique em **Sim** no **Delete de rede virtual** caixa.

### <a name="delete-cli"></a>CLI do Azure

1. Inicie sessão Azure utilizando o CLI 2.0 para eliminar a rede virtual (Resource Manager) com o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```

2. Inicie sessão Azure utilizando a CLI do Azure 1.0 para eliminar a rede virtual (clássica) com os seguintes comandos:

    ```azurecli
    azure config mode asm 

    azure network vnet delete --vnet myVnetB --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Na linha de comandos do PowerShell, introduza o seguinte comando para eliminar a rede virtual (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
    ```

2. Para eliminar a rede virtual (clássica) com o PowerShell, tem de modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md). Remova o seguinte elemento VirtualNetworkSite para a rede virtual que utilizou neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importar um ficheiro de configuração foi alterada de rede pode fazer com que as alterações às redes virtuais existentes (clássica) na sua subscrição. Certifique-se apenas a remover a rede virtual anterior e que não alterar ou remover quaisquer outras redes virtuais existentes da sua subscrição. 

## <a name="register"></a>Registar-se na pré-visualização

A capacidade de elemento redes virtuais criadas através de modelos de implementação do Azure diferentes que existam em diferentes subscrições está atualmente em pré-visualização. Funcionalidades de pré-visualização não podem ter o mesmo nível de disponibilidade e fiabilidade como funcionalidades de versão em geral. Para as notificações mais atualizadas à sua disponibilidade e o estado das funcionalidades de pré-visualização, verifique o [de atualizações de rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network) página. 

Primeiro tem de registar para a funcionalidade de modelo entre subscrições, entre a implementação, para poder utilizá-lo. Conclua os passos seguintes dentro da subscrição que cada rede virtual que pretende elemento está no, utilizando o Azure PowerShell ou a CLI do Azure:

### <a name="powershell"></a>PowerShell

1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Iniciar uma sessão do PowerShell e inicie sessão no Azure com o `Login-AzureRmAccount` comando.
3. Registe a subscrição que cada rede virtual que pretende ponto destina-se na pré-visualização, introduzindo os comandos seguintes:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Confirme que são registadas para a pré-visualização, introduzindo o seguinte comando:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Não efetue os passos nas secções de Portal, CLI do Azure, PowerShell ou Gestor de recursos do modelo deste artigo até o **RegistrationState** recebe depois de introduzir os comandos anteriores é de saída  **Registado** para ambas as subscrições.

> [!NOTE]
> Este tutorial utiliza redes virtuais que existem na mesma região. A capacidade de elemento redes virtuais em diferentes regiões também está em pré-visualização. Para se registar para por várias regiões ou global peering, concluir os passos 1 a 4 novamente, utilizando `-FeatureName AllowGlobalVnetPeering` em vez de `-FeatureName AllowClassicCrossSubscriptionPeering`. Os dois recursos são independentes entre si. Não é necessário para se registar para ambos, a menos que pretenda utilizar ambas. A capacidade está disponível um conjunto limitado de regiões (inicialmente, EUA oeste Central, Canadá Central e dos EUA oeste 2).

### <a name="azure-cli"></a>CLI do Azure

1. [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. Certifique-se de que está a utilizar a versão 2.0.18 ou superior da CLI do Azure, introduzindo o `az --version` comando. Se não tiver, instale a versão mais recente.
3. Inicie sessão no Azure com o `az login` comando.
4. Registar-se para a pré-visualização, introduzindo os seguintes comandos:

   ```azurecli-interactive
   az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
   az provider register --name Microsoft.Network
   ```

5. Confirme que são registadas para a pré-visualização, introduzindo o seguinte comando:

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    Não efetue os passos nas secções de Portal, CLI do Azure, PowerShell ou Gestor de recursos do modelo deste artigo até o **RegistrationState** recebe depois de introduzir o comando anterior é de saída **registada**  para ambas as subscrições.

> [!NOTE]
> Este tutorial utiliza redes virtuais que existem na mesma região. A capacidade de elemento redes virtuais em diferentes regiões também está em pré-visualização. Para se registar para por várias regiões ou global peering, concluir os passos 1 a 5 novamente, utilizando `--name AllowGlobalVnetPeering` em vez de `--name AllowClassicCrossSubscriptionPeering`. Os dois recursos são independentes entre si. Não é necessário para se registar para ambos, a menos que pretenda utilizar ambas. A capacidade está disponível um conjunto limitado de regiões (inicialmente, EUA oeste Central, Canadá Central e dos EUA oeste 2).

## <a name="next-steps"></a>Passos seguintes

- Exaustivamente familiarizar-se com importante [restrições de peering de rede virtual e comportamentos](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar uma rede virtual para a produção de peering utilize.
- Saiba mais sobre todos os [definições de rede virtual peering](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar um hub- and -spoke topologia de rede](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering de rede virtual.
