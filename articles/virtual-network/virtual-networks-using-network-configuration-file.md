---
title: "Configurar uma rede Virtual do Azure (clássica) - ficheiro de configuração de rede | Microsoft Docs"
description: "Saiba como criar e modificar redes virtuais (clássicas) ao exportar, alterar e importar um ficheiro de configuração de rede."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f1e3ae26b6525f2235a6b0d53546b334dc027b94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Configurar uma rede virtual (clássica) utilizando um ficheiro de configuração de rede
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que implementações mais novas utilizem o modelo de implementação Resource Manager.

Pode criar e configurar uma rede virtual (clássica) com um ficheiro de configuração de rede através da interface de linha de comandos do Azure (CLI) 1.0 ou o Azure PowerShell. Não é possível criar ou modificar uma rede virtual através do modelo de implementação Azure Resource Manager utilizando um ficheiro de configuração de rede. Não é possível utilizar o portal do Azure para criar ou modificar uma rede virtual (clássica) utilizando um ficheiro de configuração de rede, no entanto, pode utilizar o portal do Azure para criar uma rede virtual (clássica), sem utilizar um ficheiro de configuração de rede.

Criar e configurar uma rede virtual (clássica) com um ficheiro de configuração de rede necessitam de exportação, alterar e importar o ficheiro.

## <a name="export"></a>Exportar um ficheiro de configuração de rede

Pode utilizar o PowerShell ou a CLI do Azure para exportar um ficheiro de configuração de rede. PowerShell exporta um ficheiro XML, enquanto a CLI do Azure exporta um ficheiro json.

### <a name="powershell"></a>PowerShell
 
1. [Instalar o Azure PowerShell e inicie sessão no Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Altere o diretório (e certifique-se de que existe) e nome de ficheiro no comando seguinte conforme pretendido, em seguida, execute o comando para exportar o ficheiro de configuração de rede:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>CLI do Azure 1.0

1. [Instalar a CLI do Azure 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Conclua os restantes passos de uma linha de comandos da CLI do Azure 1.0.
2. Inicie sessão no Azure, introduzindo o `azure login` comando.
3. Certifique-se de que está no modo asm introduzindo o `azure config mode asm` comando.
4. Altere o diretório (e certifique-se de que existe) e nome de ficheiro no comando seguinte conforme pretendido, em seguida, execute o comando para exportar o ficheiro de configuração de rede:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Criar ou modificar um ficheiro de configuração de rede

Um ficheiro de configuração de rede é um ficheiro XML (ao utilizar o PowerShell) ou um ficheiro json (ao utilizar a CLI do Azure). Pode editar o ficheiro em qualquer texto ou editor de XML/json. O [definições de esquema do ficheiro de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx) artigo inclui os detalhes de todas as definições. Para obter explicações adicionais das definições, consulte [ver redes virtuais e definições](virtual-network-manage-network.md#view-vnet). As alterações efetuadas ao ficheiro:

- Deve estar em conformidade com o esquema ou importar que o ficheiro de configuração de rede irá falhar.
- Substituir as definições de rede existente para a sua subscrição, por isso, tenha muito cuidado quando efetuar alterações. Por exemplo, referencie os ficheiros de configuração de rede de exemplo que se seguem. Indicar o ficheiro original continha dois **VirtualNetworkSite** instâncias e foi alterado, conforme mostrado nos exemplos. Quando importar o ficheiro, o Azure elimina a rede virtual para o **VirtualNetworkSite** instância removido no ficheiro. Este cenário simplificado assume que não existem recursos foram na rede virtual, como se existirem, não foi possível eliminar a rede virtual e a importação falhará.

> [!IMPORTANT]
> Azure considera uma sub-rede que tenha algo foram implementadas no mesmo como **em utilização**. Quando uma sub-rede em utilização, não pode ser modificado. Antes de modificar as informações de sub-rede num ficheiro de configuração de rede, mova tudo o que tiver implementado para a sub-rede para outra sub-rede que não está a ser modificada. Consulte [mover VM ou instância de função para outra sub-rede](virtual-networks-move-vm-role-to-subnet.md) para obter mais detalhes.

### <a name="example-xml-for-use-with-powershell"></a>Exemplo XML para utilização com o PowerShell

O ficheiro de configuração de rede de exemplo seguinte cria uma rede virtual denominada *myVirtualNetwork* com um espaço de endereços de *10.0.0.0/16* no *EUA Leste* Azure região. A rede virtual contém uma sub-rede designada *mySubnet* com um prefixo de endereço de *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Se o ficheiro de configuração de rede que exportou não contém nenhum conteúdo, pode copiar o XML no exemplo anterior e cole-o um novo ficheiro.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>JSON de exemplo para utilização com a CLI do Azure 1.0

O ficheiro de configuração de rede de exemplo seguinte cria uma rede virtual denominada *myVirtualNetwork* com um espaço de endereços de *10.0.0.0/16* no *EUA Leste* Azure região. A rede virtual contém uma sub-rede designada *mySubnet* com um prefixo de endereço de *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Se o ficheiro de configuração de rede que exportou não contém nenhum conteúdo, pode copiar o json no exemplo anterior e cole-o um novo ficheiro.

## <a name="import"></a>Importar um ficheiro de configuração de rede

Pode utilizar o PowerShell ou a CLI do Azure para importar um ficheiro de configuração de rede. PowerShell importa um ficheiro XML, enquanto a CLI do Azure importa um ficheiro json. Se a importação falhará, confirme que o ficheiro está em conformidade com a [esquema de configuração de rede](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Instalar o Azure PowerShell e inicie sessão no Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Altere o diretório e o nome de ficheiro no comando seguinte, conforme necessário, em seguida, execute o comando para importar o ficheiro de configuração de rede:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>CLI do Azure 1.0

1. [Instalar a CLI do Azure 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Conclua os restantes passos de uma linha de comandos da CLI do Azure 1.0.
2. Inicie sessão no Azure, introduzindo o `azure login` comando.
3. Certifique-se de que está no modo asm introduzindo o `azure config mode asm` comando.
4. Altere o diretório e o nome de ficheiro no comando seguinte, conforme necessário, em seguida, execute o comando para importar o ficheiro de configuração de rede:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
