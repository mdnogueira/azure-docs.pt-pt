---
title: "Criar um peering de rede virtual do Azure - Resource Manager - subscrição mesmo | Microsoft Docs"
description: "Saiba como criar uma rede virtual peering entre redes virtuais criadas através do Gestor de recursos que existe na mesma subscrição do Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: ab62164c85ece30181217a36a51d19fda52907bc
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>Criar um peering de rede virtual - Resource Manager, a mesma subscrição

Neste tutorial, pode aprende a criar uma peering entre redes virtuais criadas através do Gestor de recursos de rede virtual. Ambas as redes virtuais existem na mesma subscrição. Peering dois recursos de permite redes virtuais em redes virtuais diferentes para comunicar entre si com o mesmo largura de banda e latência dado a entender, os recursos foram na mesma rede virtual. Saiba mais sobre [peering de rede Virtual](virtual-network-peering-overview.md). 

Os passos para criar um peering de rede virtual são diferentes, dependendo se as redes virtuais estão no mesmo, ou outro, subscrições e que [modelo de implementação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) as redes virtuais são criadas através do. Saiba como criar uma rede virtual peering noutros cenários, clicando no cenário da tabela seguinte:

|Modelo de implementação do Azure  | Subscrição do Azure  |
|--------- |---------|
|[O Gestor de recursos](create-peering-different-subscriptions.md) |Diferentes|
|[Um Gestor de recursos, um clássico](create-peering-different-deployment-models.md) |mesmo|
|[Um Gestor de recursos, um clássico](create-peering-different-deployment-models-subscriptions.md) |Diferentes|

Não é possível criar uma rede virtual peering entre duas redes virtuais implementadas através do modelo de implementação clássica. Se pretender ligar redes virtuais que foram criados através do modelo de implementação clássica, pode utilizar um Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ligar as redes virtuais. 

Este tutorial elementos redes virtuais na mesma região. A capacidade de elemento redes virtuais em diferentes regiões está atualmente em pré-visualização. Execute os passos do [registar para peering de rede virtual global](#register) antes de tentar elemento redes virtuais em diferentes regiões ou a falha de peering. A capacidade de ligar redes virtuais em diferentes regiões com um Azure [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) estiver geralmente disponível e não necessita de registo.

Pode utilizar o [portal do Azure](#portal), o Azure [interface de linha de comandos](#cli) (CLI), Azure [PowerShell](#powershell), ou um [modelo Azure Resource Manager](#template)para criar um peering de rede virtual. Clique em qualquer uma das ligações de ferramenta anterior para ir diretamente para os passos para criar um peering de rede virtual com a ferramenta de escolha.

## <a name="portal"></a>Criar peering - portal do Azure

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
4. Conclua os passos 2-3 novamente, especificando os seguintes valores no passo 3:
    - **Nome**: *myVnet2*
    - **Espaço de endereços**: *10.1.0.0/16*
    - **Nome da sub-rede**: *predefinido*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Subscrição**: selecione a sua subscrição
    - **Grupo de recursos**: selecione **utilizar existente** e selecione *myResourceGroup*
    - **Localização**: *EUA leste*
5. No **procurar recursos** caixa na parte superior do portal, tipo *myResourceGroup*. Clique em **myResourceGroup** quando for apresentada nos resultados da pesquisa. É apresentado um painel para o **myresourcegroup** grupo de recursos. O grupo de recursos contém as duas redes virtuais criadas nos passos anteriores.
6. Clique em **myVNet1**.
7. No **myVnet1** painel apresentado, clique em **Peerings** na vertical lista das opções no lado esquerdo do painel.
8. No **myVnet1 - Peerings** painel que antes eram, clique em **+ adicionar**
9. No **adicionar peering** painel apresentado, introduza, ou selecione as seguintes opções, em seguida, clique em **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modelo de implementação de rede virtual**: selecione **Resource Manager**. 
     - **Subscrição**: selecione a sua subscrição
     - **Rede virtual**: clique em **escolha uma rede virtual**, em seguida, clique em **myVnet2**.
     - **Permitir o acesso de rede virtual:** Certifique-se de que **ativado** está selecionada.
    Não existem outras definições são utilizadas neste tutorial. Para saber mais sobre todas as definições de peering, leia [gerir peerings de rede virtual](virtual-network-manage-peering.md#create-a-peering).
10. Depois de clicar em **OK** no passo anterior, o **adicionar peering** painel fecha e ver o **myVnet1 - Peerings** painel novamente. Após alguns segundos, o peering que criou é apresentado no painel. **Iniciou** está listado no **PEERING estado** coluna para o **myVnet1ToMyVnet2** peering é criado. Tiver executado o peering da Vnet1 para a Vnet2, mas agora tem elemento myVnet2 para myVnet1. O peering têm de ser criado em ambas as direções para ativar a recursos nas redes virtuais para comunicar entre si.
11. Execute os passos 5 a 10 novamente para myVnet2. Nome do peering *myVnet2ToMyVnet1*.
12. Alguns segundos depois de clicar em **OK** para criar peering para MyVnet2, o **myVnet2ToMyVnet1** peering que acabou de criar está listado com **ligado** no  **Estado do PEERING** coluna.
13. Conclua os passos 5 a 7 novamente para MyVnet1. O **PEERING estado** para o **myVnet1ToVNet2** peering é agora também **ligado**. O peering é estabelecido com êxito depois de confirmar **ligado** no **PEERING estado** coluna para ambas as redes virtuais no peering de.
14. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
15. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos a [eliminar recursos](#delete-portal) secção deste artigo.

Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Criar peering - CLI do Azure

O script seguinte:

- Requer a CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão, execute o `az --version` comando. Se precisar de atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funciona numa shell de deteção. Para opções sobre a execução de scripts da CLI do Azure num cliente Windows, consulte [com a CLI do Azure no Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Em vez de instalar a CLI e as respetivas dependências, pode utilizar a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Clique em de **experimente** botão no script de forma, que invoca uma Shell de nuvem que os registos pode iniciar sessão para a sua conta do Azure com. Para executar o script, clique em de **cópia** botão e colar o conteúdo na sua Shell de nuvem.

1. Crie um grupo de recursos e duas redes virtuais.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. Crie uma rede virtual peering entre duas redes virtuais.
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. Depois do script executa, reveja os peerings para cada rede virtual. 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Volte a executar o comando anterior, substituindo *myVnet1* com *myVnet2*. A saída de ambos os comandos mostra **ligado** no **PeeringState** coluna.

     Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

4. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
5. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos [eliminar recursos](#delete-cli) neste artigo.


## <a name="powershell"></a>Criar peering - PowerShell

1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Para iniciar uma sessão do PowerShell, aceda a **Iniciar**, introduza **powershell** e, em seguida, clique em **PowerShell**.
3. No PowerShell, inicie sessão no Azure introduzindo o comando `login-azurermaccount`. A conta que iniciar sessão com tem de ter as permissões necessárias para criar um peering de rede virtual. Consulte o [permissões](#permissions) secção deste artigo para obter mais detalhes.
4. Crie um grupo de recursos e duas redes virtuais. Ao executar o script, copie o seguinte script, cole-o PowerShell e, em seguida, prima `Enter` depois da última linha é apresentada no ecrã:

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. Crie uma rede virtual peering entre duas redes virtuais. Copie o seguinte script, cole para o PowerShell e, em seguida, prima `Enter` depois da última linha é apresentada no ecrã:
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. Para rever as sub-redes da rede virtual, copie o seguinte comando, cole para o PowerShell e, em seguida, prima `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Volte a executar o comando anterior, substituindo *myVnet1* com *myVnet2*. A saída de ambos os comandos mostra **ligado** no **PeeringState** coluna.
7. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
8. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos [eliminar recursos](#delete-powershell) neste artigo.

Quaisquer recursos do Azure, que criar a rede virtual estão agora capazes de comunicar entre si através dos respetivos endereços IP. Se estiver a utilizar a resolução do nome do Azure de predefinido para as redes virtuais, os recursos nas redes virtuais não são possível resolver nomes através de redes virtuais. Se pretender resolver nomes através de redes virtuais num peering, terá de criar o seu próprio servidor DNS. Saiba como configurar [resolução de nomes utilizando o seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Criar peering - modelo do Resource Manager

1. Referência [criar um peering de rede virtual](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) modelo do Resource Manager. O modelo inclui instruções para a sua implementação utilizando o portal do Azure, o PowerShell ou o CLI do Azure. Início de sessão para que a ferramenta escolher para implementar o modelo com a utilização de uma conta que tenha as permissões necessárias para criar um peering de rede virtual. Consulte o [permissões](#permissions) secção deste artigo para obter mais detalhes.
2. **Opcional**: Apesar de criação de máquinas virtuais não é abrangido neste tutorial, pode criar uma máquina virtual em cada rede virtual e ligar a partir de uma máquina virtual para outro, para validar a conetividade.
3. **Opcional**: para eliminar os recursos que criou neste tutorial, concluir os passos a [eliminar recursos](#delete) secção deste artigo, utilizando o portal do Azure, PowerShell ou a CLI do Azure.

## <a name="permissions"></a>Permissões

As contas que utilizar para criar um peering de rede virtual tem de ter a função necessária ou permissões. Por exemplo, se foram peering duas redes virtuais com o nome VNet1 e VNet2, deve ser atribuída à conta a seguinte função mínima ou as permissões para cada rede virtual:
    
|Rede virtual|Função|Permissões|
|---|---|---|
|VNet1|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[Contribuidor de Rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Saiba mais sobre [funções incorporadas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e atribuir permissões específicas para [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (apenas para o Resource Manager).

## <a name="delete"></a>Eliminar recursos
Quando tiver terminado neste tutorial, pode querer eliminar os recursos que criou no tutorial, pelo que não implicar custos de utilização. Também eliminar um grupo de recursos elimina todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa de portal, introduza **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. No **myResourceGroup** painel, clique em de **eliminar** ícone.
3. Para confirmar a eliminação no **tipo o nome de grupo de recursos** box, introduza **myResourceGroup**e, em seguida, clique em **eliminar**.

### <a name="delete-cli"></a>CLI do Azure

Introduza o seguinte comando:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Introduza o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

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
