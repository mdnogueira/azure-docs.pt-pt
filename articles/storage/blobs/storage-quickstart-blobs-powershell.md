---
title: "Guia de introdução do Azure - objetos de transferência da Blob storage do Azure com o PowerShell | Microsoft Docs"
description: "Saiba rapidamente a transferência de objetos do Blob storage do Azure com o PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: robinsh
ms.openlocfilehash: 1a9941b21b92c70dd0a46ce2e4c75142e1786650
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-azure-powershell"></a>Transferência de objetos para/de Blob storage do Azure com o Azure PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este detalhes guia através do PowerShell para transferir ficheiros entre discos locais e de armazenamento de Blobs do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este guia de introdução requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Criar um contentor

Os BLOBs sempre são carregados num contentor. Isto permite-lhe organizar grupos de blobs, tal como organizar os ficheiros no seu computador em pastas.

Defina o nome do contentor, em seguida, criar o contentor utilizando [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer), definir as permissões para o blob para permitir o acesso público dos ficheiros. O nome do contentor neste exemplo é *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Carregar os blobs no contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Ficheiros VHD utilizados para efetuar cópias de VMs de IaaS são blobs de páginas. Acrescentar blobs são utilizados para registo, por exemplo, se pretender escrever um ficheiro e, em seguida, mantenha a adição de mais informações. A maioria dos ficheiros armazenados no Blob storage são blobs de blocos. 

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor, em seguida, obtenha uma referência para o blob de bloco no contentor. Assim que tiver a referência de blob, pode carregar dados para a mesma utilizando [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Esta operação cria o blob caso ainda não existir ou substitui-lo se já existir.

Os exemplos seguintes carregarem Image001.jpg e Image002.png de d:\\_TestImages pasta no disco local para o contentor que acabou de criar.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Carregar ficheiros tantos como pretender antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Obter uma lista de blobs no contentor com [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Este exemplo mostra apenas os nomes dos blobs carregados.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Transferir blobs

Transferir os blobs para o seu disco local. Para cada blob ser transferidos, defina o nome e a chamada [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) para transferir o blob.

Neste exemplo transfere os blobs para d:\\_TestImages\Downloads no disco local. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com o AzCopy

O [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) utilitário é outra opção para a transferência de dados de script de elevado desempenho para o Storage do Azure. Pode utilizar o AzCopy para transferir dados para e do armazenamento de BLOBs, de ficheiros e de tabela.

Como obter um exemplo rápido, eis o comando do AzCopy para carregar um ficheiro denominado *omeuficheiro.txt* para o *mystoragecontainer* contentor a partir da janela do PowerShell.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Limpar recursos

Remova todos os recursos que criou. A forma mais fácil para o fazer consiste em eliminar o grupo de recursos. Isto também elimina todos os recursos contidos dentro do grupo. Neste caso, remove a conta de armazenamento e o grupo de recursos em si.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, aprendeu como transferir ficheiros entre um disco local e o armazenamento de Blobs do Azure. Para obter mais informações sobre como trabalhar com armazenamento de BLOBs, continue para o Blob storage procedimentos.

> [!div class="nextstepaction"]
> [Procedimentos de operações de armazenamento de BLOBs](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referência de cmdlets de armazenamento do Microsoft Azure PowerShell
* [Cmdlets do PowerShell do armazenamento](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure
* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.