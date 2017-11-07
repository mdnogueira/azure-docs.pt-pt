---
title: "Criar um peering de rede virtual do Azure - modelos de implementação diferentes - subscrição mesmo | Microsoft Docs"
description: "Saiba como criar uma rede virtual peering entre redes virtuais criadas através de modelos de implementação do Azure diferentes que existe na mesma subscrição do Azure."
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
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: ebe489b6e0993dad42950acdafac48e662da7f77
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Criar um peering de rede virtual - diferentes modelos de implementação, a mesma subscrição 

Neste tutorial, pode aprende a criar uma rede virtual peering entre redes virtuais criadas através de modelos de implementação diferentes. Ambas as redes virtuais existem na mesma subscrição. Peering dois recursos de permite redes virtuais em redes virtuais diferentes para comunicar entre si com o mesmo largura de banda e latência dado a entender, os recursos foram na mesma rede virtual. Saiba mais sobre [peering de rede Virtual](virtual-network-peering-overview.md). 

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão no mesmo, ou outro, subscrições e que [modelo de implementação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através do. Saiba como criar uma rede virtual peering noutros cenários, clicando no cenário da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[O Gestor de recursos](virtual-network-create-peering.md) |mesmo|
|[O Gestor de recursos](create-peering-different-subscriptions.md) |Diferentes|
|[Um Gestor de recursos, um clássico](create-peering-different-deployment-models-subscriptions.md) |Diferentes|

Não é possível criar uma rede virtual peering entre duas redes virtuais implementadas através do modelo de implementação clássica. Se pretender ligar redes virtuais que foram criados através do modelo de implementação clássica, pode utilizar um Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar as redes virtuais. 

Este tutorial elementos redes virtuais na mesma região. A capacidade de elemento redes virtuais em diferentes regiões está atualmente em pré-visualização. Execute os passos do [registar para peering de rede virtual global](#register) antes de tentar elemento redes virtuais em diferentes regiões ou a falha de peering. A capacidade de ligar redes virtuais em diferentes regiões com um Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) estiver geralmente disponível e não necessita de registo.

Pode utilizar o [portal do Azure](#portal), o Azure [interface de linha de comandos](#cli) (CLI), Azure [PowerShell](#powershell), ou um [modelo Azure Resource Manager](#template)para criar um peering de rede virtual. Clique em qualquer uma das ligações de ferramenta anterior para ir diretamente para os passos para criar um peering de rede virtual com a ferramenta de escolha.

## <a name="create-peering---azure-portal"></a>Criar peering - portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Consulte o [permissões](#permissions) secção deste artigo para obter mais detalhes.
2. Clique em **+ novo**, clique em **redes**, em seguida, clique em **rede Virtual**.
3. No **criar rede virtual** painel, introduza, ou selecione os valores para as seguintes definições, em seguida, clique em **criar**:
    - **Nome**: *myVnet1*
    - **Espaço de endereços**: *10.0.0.0/16*
    - **Nome da sub-rede**: *predefinido*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Subscrição**: selecione a sua subscrição
    - **Grupo de recursos**: selecione **criar nova** e introduza *myResourceGroup*
    - **Localização**: *EUA leste*
4. Clique em **+ nova**. No **procurar no Marketplace** caixa, escreva *rede Virtual*. Clique em **rede Virtual** quando for apresentada nos resultados da pesquisa. 
5. No **rede Virtual** painel, selecione **clássico** no **selecionar um modelo de implementação** caixa e, em seguida, clique em **criar**.
6. No **criar rede virtual** painel, introduza, ou selecione os valores para as seguintes definições, em seguida, clique em **criar**:
    - **Nome**: *myVnet2*
    - **Espaço de endereços**: *10.1.0.0/16*
    - **Nome da sub-rede**: *predefinido*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Subscrição**: selecione a sua subscrição
    - **Grupo de recursos**: selecione **utilizar existente** e selecione *myResourceGroup*
    - **Localização**: *EUA leste*
7. No **procurar recursos** caixa na parte superior do portal, tipo *myResourceGroup*. Clique em **myResourceGroup** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myresourcegroup** grupo de recursos. O grupo de recursos contém as duas redes virtuais criadas nos passos anteriores.
8. Clique em **myVNet1**.
9. No **myVnet1** painel apresentado, clique em **Peerings** na vertical lista das opções no lado esquerdo do painel.
10. No **myVnet1 - Peerings** painel que antes eram, clique em **+ adicionar**
11. No **adicionar peering** painel apresentado, introduza, ou selecione as seguintes opções, em seguida, clique em **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modelo de implementação de rede virtual**: selecione **clássico**. 
     - **Subscrição**: selecione a sua subscrição
     - **Rede virtual**: clique em **escolha uma rede virtual**, em seguida, clique em **myVnet2**.
     - **Permitir o acesso de rede virtual:** Certifique-se de que **ativado** está selecionada.
    Não existem outras definições são utilizadas neste tutorial. Para saber mais sobre todas as definições de peering, leia [gerir peerings de rede virtual](virtual-network-manage-peering.md#create-a-peering).
12. Depois de clicar em **OK** no passo anterior, o **adicionar peering** painel fecha e ver o **myVnet1 - Peerings** painel novamente. Após alguns segundos, o peering que criou é apresentado no painel. **Ligado** está listado no **PEERING estado** coluna para o **myVnet1ToMyVnet2** peering é criado.

    Agora é estabelecido o peering. Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
13. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
14. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos a [eliminar recursos](#delete-portal) secção deste artigo.

## <a name="cli"></a>Criar peering - CLI do Azure

1. [Instalar](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 1.0 de CLI do Azure para criar a rede virtual (clássica).
2. Abra uma sessão de comando e inicie sessão no Azure com o `azure login` comando.
3. Execute o CLI no modo de gestão do serviço introduzindo o `azure config mode asm` comando.
4. Introduza o seguinte comando para criar a rede virtual (clássica):
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. Crie um grupo de recursos e uma rede virtual (Resource Manager). Pode utilizar a CLI 1.0 ou 2.0 ([instalar](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). Neste tutorial, o CLI 2.0 é utilizado para criar a rede virtual (Resource Manager), uma vez que 2.0 tem de ser utilizado para criar o peering. Execute o seguinte bash CLI de script da sua máquina local com a CLI 2.0.4 ou posterior instalado. Para obter as opções de execução bash scripts CLI num cliente Windows, consulte [com a CLI do Azure no Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Também pode executar o script utilizando a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Clique em de **experimente** botão no script de forma, que invoca uma Shell de nuvem que os registos pode iniciar sessão para a sua conta do Azure com. Para executar o script, clique em de **cópia** botão e colagem, o conteúdo na sua Shell de nuvem, em seguida, prima `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. Crie uma rede virtual peering entre duas redes virtuais criadas através de modelos de implementação diferentes. Copie o seguinte script para um editor de texto no seu PC. Substitua `<subscription id>` com o ID de subscrição Se não souber o Id de subscrição, introduza o `az account show` comando. O valor para **id** na saída é o ID de subscrição Cole o script modificado à sua sessão CLI e, em seguida, prima `Enter`.

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. Depois do script executa, reveja o peering da rede virtual (Resource Manager). Copie o seguinte comando, cole-o na sua sessão CLI e, em seguida, prima `Enter`:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    O resultado mostra **ligado** no **PeeringState** coluna. 

    Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
8. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
9. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos [eliminar recursos](#delete-cli) neste artigo.

## <a name="powershell"></a>Criar peering - PowerShell

1. Instale a versão mais recente do PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) e [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) módulos. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, inicie sessão no Azure introduzindo o comando `Add-AzureAccount`.
4. Para criar uma rede virtual (clássica) com o PowerShell, tem de criar um novo ou modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md). O ficheiro deve incluir o seguinte **VirtualNetworkSite** elemento da rede virtual que utilizou neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
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
5. Inicie sessão no Azure para criar a rede virtual (Resource Manager), introduzindo o `login-azurermaccount` comando. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Consulte o [permissões](#permissions) secção deste artigo para obter mais detalhes.
6. Crie um grupo de recursos e uma rede virtual (Resource Manager). Copie o script, cole-a PowerShell e, em seguida, prima `Enter`.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Crie uma rede virtual peering entre duas redes virtuais criadas através de modelos de implementação diferentes. Copie o seguinte script para um editor de texto no seu PC. Substitua `<subscription id>` com o ID de subscrição Se não souber o Id de subscrição, introduza o `Get-AzureRmSubscription` comando para vê-la. O valor para **Id** na saída devolvida é o ID de subscrição. Ao executar o script, copie o script de modificação do seu editor de texto, em seguida, rato na sua sessão do PowerShell e, em seguida, prima `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Depois do script executa, reveja o peering da rede virtual (Resource Manager). Copie o seguinte comando, colá-lo na sua sessão do PowerShell e, em seguida, prima `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    O resultado mostra **ligado** no **PeeringState** coluna.

    Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

9. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
10. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos [eliminar recursos](#delete-powershell) neste artigo.
 
## <a name="permissions"></a>Permissões

As contas que utilizar para criar um peering de rede virtual tem de ter a função necessária ou permissões. Por exemplo, se foram peering duas redes virtuais com o nome myVnet1 e myVnet2, deve ser atribuída à conta a seguinte função mínima ou as permissões para cada rede virtual:
    
|Rede virtual|Modelo de implementação|Função|Permissões|
|---|---|---|---|
|myVnet1|Resource Manager|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássica|[Contribuidor de Rede Clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myVnet2|Resource Manager|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássica|[Contribuidor de Rede Clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções incorporadas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e atribuir permissões específicas para [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (apenas para o Resource Manager).

## <a name="delete"></a>Eliminar recursos
Quando tiver terminado neste tutorial, pode querer eliminar os recursos que criou no tutorial, pelo que não implicar custos de utilização. Também eliminar um grupo de recursos elimina todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa de portal, introduza **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. No **myResourceGroup** painel, clique em de **eliminar** ícone.
3. Para confirmar a eliminação no **tipo o nome de grupo de recursos** box, introduza **myResourceGroup**e, em seguida, clique em **eliminar**.

### <a name="delete-cli"></a>CLI do Azure

1. Utilize o 2.0 CLI do Azure para eliminar a rede virtual (Resource Manager) com o seguinte comando:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Utilize a CLI do Azure 1.0 para eliminar a rede virtual (clássica) com os seguintes comandos:

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Introduza o seguinte comando para eliminar a rede virtual (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. Para eliminar a rede virtual (clássica) com o PowerShell, tem de modificar um ficheiro de configuração de rede existente. Saiba como [exportar, atualizar e importar ficheiros de configuração de rede](virtual-networks-using-network-configuration-file.md). Remova o seguinte elemento VirtualNetworkSite para a rede virtual que utilizou neste tutorial:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
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

## <a name="register"></a>Registar-se para a pré-visualização de peering de rede virtual global

A capacidade de elemento redes virtuais em diferentes regiões está atualmente em pré-visualização. A capacidade está disponível um conjunto limitado de regiões (inicialmente, EUA oeste Central, Canadá Central e dos EUA oeste 2). Peerings de rede virtual criada entre redes virtuais em regiões diferentes poderão não ter o mesmo nível de disponibilidade e fiabilidade como um peering entre redes virtuais na mesma região. Para obter as notificações mais atualizadas sobre a disponibilidade e o estado desta funcionalidade, veja a página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

A ponto redes virtuais em regiões, primeiro tem de registar para a pré-visualização, concluindo os seguintes passos (dentro da subscrição cada rede virtual que pretende elemento está no) utilizando o Azure PowerShell ou a CLI do Azure:

### <a name="powershell"></a>PowerShell

1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Iniciar uma sessão do PowerShell e inicie sessão no Azure com o `Login-AzureRmAccount` comando.
3. Registe a subscrição que cada rede virtual que pretende ponto destina-se na pré-visualização, introduzindo os comandos seguintes:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Confirme que são registadas para a pré-visualização, introduzindo o seguinte comando:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Não efetue os passos nas secções de Portal, CLI do Azure, PowerShell ou Gestor de recursos do modelo deste artigo até o **RegistrationState** recebe depois de introduzir o comando anterior é de saída **registada**  para ambas as subscrições.

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

## <a name="next-steps"></a>Passos seguintes

- Exaustivamente familiarizar-se com importante [restrições de peering de rede virtual e comportamentos](virtual-network-manage-peering.md#requirements-and-constraints) antes de criar uma rede virtual para a produção de peering utilize.
- Saiba mais sobre todos os [definições de rede virtual peering](virtual-network-manage-peering.md#create-a-peering).
- Saiba como [criar um hub- and -spoke topologia de rede](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com peering de rede virtual.
