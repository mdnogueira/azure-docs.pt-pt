---
title: "Criar uma rede virtual do Azure (clássica) com várias sub-redes | Microsoft Docs"
description: "Saiba como criar uma rede virtual (clássica) com várias sub-redes no Azure."
services: virtual-network
documentationcenter: 
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
ms.date: 07/31/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 95c2f4fe40590a8d809f634fb5b2c92d07421bb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Criar uma rede virtual (clássica) com várias sub-redes

> [!IMPORTANT]
> O Azure tem dois [modelos de implementação diferentes](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar e trabalhar com recursos: Resource Manager e clássico. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda a criação a maioria das redes virtuais novo através do [Resource Manager](virtual-networks-create-vnet-arm-pportal.md) modelo de implementação.

Neste tutorial, saiba como criar uma básica rede virtual do Azure (clássica) que tenha sub-redes separadas públicas e privadas. Pode criar recursos do Azure, como máquinas virtuais e serviços em nuvem numa sub-rede. Recursos criados no redes virtuais (clássico) podem comunicar entre si e com os recursos nas outras redes ligadas a uma rede virtual.

Saiba mais sobre todos os [rede virtual](virtual-network-manage-network.md) e [sub-rede](virtual-network-manage-subnet.md) definições.

> [!WARNING]
> Redes virtuais (clássicas) são imediatamente eliminados pelo Azure quando um [a subscrição está desativada](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Redes virtuais (clássica) são eliminados independentemente se recursos existem na rede virtual. Se ativar mais tarde voltar a subscrição, tem de ser recriados recursos que existiam na rede virtual.

Pode criar uma rede virtual (clássica) utilizando o [portal do Azure](#portal), a [interface de linha de comandos do Azure (CLI) 1.0](#azure-cli), ou [PowerShell](#powershell).

## <a name="portal"></a>Portal

1. Num browser da Internet, vá para o [portal do Azure](https://portal.azure.com). Inicie sessão com a sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Clique em **+ novo** no portal.
3. Introduza *rede Virtual* no **procurar no Marketplace** caixa na parte superior a **novo** painel que aparece.  Clique em **rede Virtual** quando for apresentada nos resultados da pesquisa.
4. Selecione **clássico** no **selecionar um modelo de implementação** caixa o **rede Virtual** painel apresentado, clique em **criar**. 
5. Introduza os seguintes valores **criar rede virtual (clássica)** painel e clique em **criar**:

    |Definição|Valor|
    |---|---|
    |Nome|myVnet|
    |Espaço de endereços|10.0.0.0/16|
    |Nome da sub-rede|Público|
    |Intervalo de endereços da sub-rede|10.0.0.0/24|
    |Grupo de recursos|Deixe **criar nova** selecionada e, em seguida, introduza **myResourceGroup**.|
    |Subscrição e localização|Selecione a sua subscrição e localização.

    Se estiver familiarizado com o Azure, saiba mais sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [subscrições](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), e [localizações](https://azure.microsoft.com/regions) (também referido como *regiões*).
4. No portal, pode criar apenas uma sub-rede quando criar uma rede virtual. Neste tutorial, crie uma sub-rede do segundo depois de criar a rede virtual. Mais tarde poderá criar recursos acessível através da Internet a **pública** sub-rede. Também poderá criar recursos que não estão acessíveis a partir da Internet no **privada** sub-rede. Para criar a segunda sub-rede, introduza **myVnet** no **procurar recursos** caixa na parte superior da página. Clique em **myVnet** quando for apresentada nos resultados da pesquisa.
5. Clique em **sub-redes** (no **definições** secção) no **criar rede virtual (clássica)** painel que aparece.
6. Clique em **+ adicionar** no **myVnet - sub-redes** painel que aparece.
7. Introduza **privada** para **nome** no **adicionar sub-rede** painel. Introduza **10.0.1.0/24** para **intervalo de endereços**.  Clique em **OK**.
8. No **myVnet - sub-redes** painel, pode ver o **pública** e **privada** sub-redes que criou.
9. **Opcional**: Quando concluir este tutorial, pode querer eliminar os recursos que criou, para que não implicar custos de utilização:
    - Clique em **descrição geral** no **myVnet** painel.
    - Clique em de **eliminar** ícone no **myVnet** painel.
    - Para confirmar a eliminação, clique em **Sim** no **Delete de rede virtual** caixa.

## <a name="azure-cli"></a>CLI do Azure

1. Pode [instalar e configurar a CLI do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ou utilizar a CLI na Shell de nuvem do Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Para obter ajuda para comandos da CLI, escreva `azure <command> --help`. 
2. Numa sessão CLI, inicie sessão no Azure com o comando que se segue. Se clicar em **experimente** na caixa abaixo, uma Shell de nuvem abre. Pode iniciar sessão sua subscrição do Azure, sem introduzir o seguinte comando:

    ```azurecli-interactive
    azure login
    ```

3. Para garantir que o CLI está no modo de gestão de serviço, introduza o seguinte comando:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Crie uma rede virtual com uma sub-rede privada:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Crie uma sub-rede pública dentro da rede virtual:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Reveja a rede virtual e sub-redes:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Opcional**: É aconselhável eliminar os recursos que criou quando concluir este tutorial, pelo que não implicar custos de utilização:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Apesar de não é possível especificar um grupo de recursos para criar uma rede virtual (clássica) utilizando a CLI, o Azure cria a rede virtual num grupo de recursos com o nome *predefinido redes*.

## <a name="powershell"></a>PowerShell

1. Instale a versão mais recente do PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) módulo. Se não estiver familiarizado com o Azure PowerShell, consulte a [Descrição geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell.
3. No PowerShell, inicie sessão no Azure introduzindo o comando `Add-AzureAccount`.
4. Alterar o seguinte caminho e nome de ficheiro, conforme adequado, em seguida, Exportar ficheiro de configuração de rede existente:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Para criar uma rede virtual com a sub-redes públicas e privadas, utilize qualquer editor de texto para adicionar o **VirtualNetworkSite** elemento que se segue para o ficheiro de configuração de rede.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Reveja o completo [esquema do ficheiro de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importe o ficheiro de configuração de rede:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Importar um ficheiro de configuração foi alterada de rede pode fazer com que as alterações às redes virtuais existentes (clássica) na sua subscrição. Certifique-se apenas a adicionar a rede virtual anterior e que não alterar ou remover quaisquer redes virtuais existentes da sua subscrição. 

7. Reveja a rede virtual e sub-redes:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Opcional**: É aconselhável eliminar os recursos que criou quando concluir este tutorial, pelo que não implicar custos de utilização. Para eliminar a rede virtual, concluir os passos 4 a 6 novamente, este tempo, removendo a **VirtualNetworkSite** elemento adicionado no passo 5.
 
> [!NOTE]
> Apesar de não é possível especificar um grupo de recursos para criar uma rede virtual (clássica) utilizando o PowerShell, o Azure cria a rede virtual num grupo de recursos com o nome *predefinido redes*.

---

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre todas as definições de sub-rede e de rede virtual, consulte [gerir redes virtuais](virtual-network-manage-network.md) e [gerir sub-redes da rede virtual](virtual-network-manage-subnet.md). Tem várias opções para utilizar redes virtuais e sub-redes num ambiente de produção para satisfazer requisitos diferentes.
- Para filtrar o tráfego de sub-rede de entrada e saída, criar e aplicar [grupos de segurança de rede](virtual-networks-nsg.md) a sub-redes.
- Criar um [Windows](../virtual-machines/windows/classic/createportal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um [Linux](../virtual-machines/linux/classic/createportal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquina virtual e, em seguida, ligue-o a uma rede virtual existente.
- Para ligar duas redes virtuais na mesma localização do Azure, criar um [peering de rede virtual](create-peering-different-deployment-models.md) entre as redes virtuais. Pode elemento uma rede virtual (Resource Manager) a uma rede virtual (clássica), mas não é possível criar um peering entre duas redes virtuais (clássica).
- Ligar a rede virtual a uma rede no local utilizando um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) circuito.
