---
title: "Criar uma Azure virtual network com várias sub-redes | Microsoft Docs"
description: "Saiba como criar uma rede virtual com várias sub-redes no Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f82a95ec9543b2d53ef28bf7f15315e23cf4893a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Criar uma rede virtual com várias sub-redes

Neste tutorial, saiba como criar uma rede virtual da Azure básica que tem sub-redes separadas públicas e privadas. Recursos na redes virtuais podem comunicar entre si e com os recursos nas outras redes ligadas a uma rede virtual. Pode criar recursos do Azure, como máquinas virtuais, ambientes do App Service, conjuntos de dimensionamento de Máquina Virtual, do Azure HDInsight e serviços em nuvem nas sub-redes idêntica ou diferentes dentro de uma rede virtual. Criação de recursos em sub-redes diferentes permite-lhe para filtrar o tráfego de rede e terminar de sub-redes independentemente com [grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md)e a [encaminhar o tráfego entre sub-redes](virtual-network-create-udr-arm-ps.md) através da rede aplicações virtuais, tais como uma firewall, se optar por. 

As secções seguintes incluem passos que pode tomar para criar uma rede virtual, utilizando o [portal do Azure](#portal), a interface de linha de comandos do Azure ([CLI do Azure](#azure-cli)), [Azure PowerShell](#powershell)e um [modelo Azure Resource Manager](#resource-manager-template). O resultado é o mesmo, independentemente da ferramenta que utilizar para criar a rede virtual. Clica numa ligação de ferramenta para aceder à secção do tutorial. Saiba mais sobre todos os [rede virtual](virtual-network-manage-network.md) e [sub-rede](virtual-network-manage-subnet.md) definições.

Este artigo fornece os passos para criar uma rede virtual através do modelo de implementação Resource Manager, que é o modelo de implementação, que recomendamos a utilização quando criar novas redes virtuais. Se precisar de criar uma rede virtual (clássica), consulte [criar uma rede virtual (clássica)](create-virtual-network-classic.md). Se não estiver familiarizado com os modelos de implementação do Azure, consulte [modelos de implementação do Azure compreender](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="portal"></a>Portal do Azure

1. Num browser da Internet, vá para o [portal do Azure](https://portal.azure.com). Inicie sessão com a sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. No portal, clique em **+ novo** > **redes** > **rede Virtual**.
3. No **criar rede virtual** painel, introduza os valores seguintes e, em seguida, clique em **criar**:

    |Definição|Valor|
    |---|---|
    |Nome|myVnet|
    |Espaço de endereços|10.0.0.0/16|
    |Nome da sub-rede|Público|
    |Intervalo de endereços da sub-rede|10.0.0.0/24|
    |Grupo de recursos|Deixe **criar nova** selecionada e, em seguida, introduza **myResourceGroup**.|
    |Subscrição e localização|Selecione a sua subscrição e localização.

    Se estiver familiarizado com o Azure, saiba mais sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [subscrições](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), e [localizações](https://azure.microsoft.com/regions) (também referido como *regiões*).
4. No portal, pode criar apenas uma sub-rede quando criar uma rede virtual. Neste tutorial, crie uma sub-rede do segundo depois de criar a rede virtual. Mais tarde poderá criar recursos acessível através da Internet a **pública** sub-rede. Também poderá criar recursos que não estão acessíveis a partir da Internet no **privada** sub-rede. Para criar a segunda sub-rede, no **procurar recursos** na parte superior da página, introduza **myVnet**. Nos resultados da pesquisa, clique em **myVnet**. Se tiver várias redes virtuais com o mesmo nome na sua subscrição, verifique os grupos de recursos que estão listados em cada rede virtual. Certifique-se de que clica o **myVnet** resultado que tenha o grupo de recursos de pesquisa **myResourceGroup**.
5. No **myVnet** painel, em **definições**, clique em **sub-redes**.
6. No **myVnet - sub-redes** painel, clique em **+ sub-rede**.
7. No **adicionar sub-rede** painel, para **nome**, introduza **privada**. Para **intervalo de endereços**, introduza **10.0.1.0/24**.  Clique em **OK**.
8. No **myVnet - sub-redes** painel, reveja as sub-redes. Pode ver o **pública** e **privada** sub-redes que criou.
9. **Opcional:** concluir os tutoriais adicionais listados na [passos](#next-steps) para filtrar o tráfego de rede e terminar de cada sub-rede com grupos de segurança de rede, para encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede , ou para ligar a rede virtual com outras redes virtuais ou redes no local.
10. **Opcional:** eliminar os recursos que criou neste tutorial, efetuando os passos em [eliminar recursos](#delete-portal).

## <a name="azure-cli"></a>CLI do Azure

Comandos da CLI do Azure são os mesmos, se executar os comandos do Windows, Linux ou macOS. No entanto, existem diferenças scripts shells de sistema operativo. Executa o script nos passos seguintes na shell de deteção. 

1. [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que dispõe da versão mais recente da CLI do Azure instalados. Para obter ajuda para comandos da CLI, escreva `az <command> --help`. Em vez de instalar a CLI e respetivos pré-requisitos, pode utilizar a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. A Shell de nuvem tem a CLI do AZURE pré-instalado e configurado para utilizar com a sua conta. Para utilizar a Shell de nuvem, clique na Shell de nuvem (**> _**) na parte superior da parte a [portal](https://portal.azure.com) ou basta clicar o *experimente* botão nos passos que se seguem. 
2. Se executar o CLI localmente, inicie sessão no Azure com o `az login` comando. Se utilizar a Shell de nuvem, já está registado no.
3. Reveja o seguinte script e os seus comentários. No seu browser, copie o script e cole-o para a sua sessão CLI:

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. Quando o script estiver concluído em execução, reveja as sub-redes da rede virtual. Copie o seguinte comando e, em seguida, cole-o para a sua sessão CLI:

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **Opcional:** concluir os tutoriais adicionais listados na [passos](#next-steps) para filtrar o tráfego de rede e terminar de cada sub-rede com grupos de segurança de rede, para encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede , ou para ligar a rede virtual com outras redes virtuais ou redes no local.
6. **Opcional**: a eliminar os recursos que criou neste tutorial, efetuando os passos em [eliminar recursos](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Numa sessão do PowerShell, inicie sessão no Azure com o seu [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) utilizando o `login-azurermaccount` comando.

3. Reveja o seguinte script e os seus comentários. No seu browser, copie o script e cole-o sua sessão do PowerShell:

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. Para rever as sub-redes da rede virtual, copie o seguinte comando e, em seguida, cole-o a sessão do PowerShell:

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **Opcional:** concluir os tutoriais adicionais listados na [passos](#next-steps) para filtrar o tráfego de rede e terminar de cada sub-rede com grupos de segurança de rede, para encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede , ou para ligar a rede virtual com outras redes virtuais ou redes no local.
6. **Opcional**: a eliminar os recursos que criou neste tutorial, efetuando os passos em [eliminar recursos](#delete-powershell).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

Pode implementar uma rede virtual, utilizando um modelo Azure Resource Manager. Para saber mais sobre modelos, consulte [que é o Gestor de recursos](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Para aceder ao modelo de e para saber mais sobre os respetivos parâmetros, consulte o [criar uma rede virtual com duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) modelo. Pode implementar o modelo utilizando o [portal](#template-portal), [CLI do Azure](#template-cli), ou [PowerShell](#template-powershell).

Depois de implementar o modelo de passos opcionais:

1. Concluir os tutoriais adicionais listados na [passos](#next-steps) para filtrar o tráfego de rede e para cada sub-rede com grupos de segurança de rede, para encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede, ou para estabelecer a ligação virtual rede para redes virtuais ou outros redes no local.
2. Eliminar os recursos que criou neste tutorial, efetuando os passos em qualquer subsecções de [eliminar recursos](#delete).

### <a name="template-portal"></a>Portal do Azure

1. No seu browser, abra o [página modelo](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Clique em de **implementar no Azure** botão. Se ainda não tiver sessão iniciada Azure, inicie sessão no ecrã de início de sessão portal do Azure que aparece.
3. Inicie sessão no portal utilizando o [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Introduza os seguintes valores para os parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |Subscrição|Selecione a sua subscrição|
    |Grupo de recursos|myResourceGroup|
    |Localização|Selecione uma localização|
    |Nome da Vnet|myVnet|
    |Prefixo de endereço Vnet|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Público|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privado|

5. Concordo com os termos e condições e, em seguida, clique em **Compra** para implementar a rede virtual.

### <a name="template-cli"></a>CLI do Azure

1. [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Certifique-se de que dispõe da versão mais recente da CLI do Azure instalados. Para obter ajuda para comandos da CLI, escreva `az <command> --help`. Em vez de instalar a CLI e respetivos pré-requisitos, pode utilizar a Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. A Shell de nuvem tem a CLI do AZURE pré-instalado e configurado para utilizar com a sua conta. Para utilizar a Shell de nuvem, clique na Shell de nuvem **> _** na parte superior da parte a [portal](https://portal.azure.com), ou basta clicar o **experimente** botão nos passos que se seguem. 
2. Se executar o CLI localmente, inicie sessão no Azure com o `az login` comando. Se utilizar a Shell de nuvem, já está registado no.
3. Para criar um grupo de recursos para a rede virtual, copie o seguinte comando e cole-o para a sua sessão CLI:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. Pode implementar o modelo utilizando uma das seguintes opções de parâmetros:
    - **Valores de parâmetro predefinidos**. Introduza o seguinte comando:
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **Valores de parâmetro personalizado**. Transferir e modificar o modelo antes de implementar o modelo. Também pode implementar o modelo com parâmetros na linha de comandos ou implementar o modelo com um ficheiro de parâmetros separado. Para transferir os ficheiros de modelo e os parâmetros, clique em de **procurar no GitHub** botão no [criar uma rede virtual com duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) página do modelo. No GitHub, clique em de **azuredeploy.parameters.json** ou **azuredeploy. JSON** ficheiro. Em seguida, clique o **Raw** botão para apresentar o ficheiro. No seu browser, copie o conteúdo do ficheiro. Guarde o conteúdo para um ficheiro no seu computador. Pode modificar os valores de parâmetros no modelo ou implementar o modelo com um ficheiro de parâmetros separado.  

    Para obter mais informações sobre como implementar modelos através destes métodos, escreva `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Numa sessão do PowerShell, para iniciar sessão com a [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), introduza `login-azurermaccount`.
3. Para criar um grupo de recursos para a rede virtual, introduza o seguinte comando:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. Pode implementar o modelo utilizando uma das seguintes opções de parâmetros:
    - **Valores de parâmetro predefinidos**. Introduza o seguinte comando:
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **Valores de parâmetro personalizado**. Transferir e modificar o modelo antes de implementá-lo. Também pode implementar o modelo com parâmetros na linha de comandos ou implementar o modelo com um ficheiro de parâmetros separado. Para transferir os ficheiros de modelo e os parâmetros, clique em de **procurar no GitHub** botão no [criar uma rede virtual com duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) página do modelo. No GitHub, clique em de **azuredeploy.parameters.json** ou **azuredeploy. JSON** ficheiro. Em seguida, clique o **Raw** botão para apresentar o ficheiro. No seu browser, copie o conteúdo do ficheiro. Guarde o conteúdo para um ficheiro no seu computador. Pode modificar os valores de parâmetros no modelo ou implementar o modelo com um ficheiro de parâmetros separado.  

    Para obter mais informações sobre como implementar modelos através destes métodos, escreva `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Eliminar recursos

Quando concluir este tutorial, pode querer eliminar os recursos que criou, para que não implicar custos de utilização. Também eliminar um grupo de recursos elimina todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa de portal, introduza **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. No **myResourceGroup** painel, clique em de **eliminar** ícone.
3. Para confirmar a eliminação no **tipo o nome de grupo de recursos** box, introduza **myResourceGroup**e, em seguida, clique em **eliminar**.

### <a name="delete-cli"></a>CLI do Azure

Numa sessão CLI, introduza o seguinte comando:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Numa sessão do PowerShell, introduza o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre todas as definições de sub-rede e de rede virtual, consulte [gerir redes virtuais](virtual-network-manage-network.md#view-vnet) e [gerir sub-redes da rede virtual](virtual-network-manage-subnet.md#create-subnet). Tem várias opções para utilizar redes virtuais e sub-redes num ambiente de produção para satisfazer requisitos diferentes.
- Filtrar o tráfego de sub-rede de entrada e saída ao criar e aplicar [grupos de segurança de rede](virtual-networks-nsg.md) a sub-redes.
- Encaminhar o tráfego entre sub-redes através de uma aplicação virtual de rede, criando [rotas definidas pelo utilizador](virtual-network-create-udr-arm-ps.md) e aplicar as rotas para cada sub-rede.
- Criar um [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquina virtual na rede virtual existente.
- Ligar duas redes virtuais através da criação de um [peering de rede virtual](virtual-network-peering-overview.md) entre as redes virtuais.
- Ligar a rede virtual a uma rede no local utilizando um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) circuito.
