---
title: Como utilizar o PowerShell para gerir os Ficheiros do Azure | Microsoft Docs
description: Aprenda a utilizar o PowerShell para gerir os Ficheiros do Azure.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: f919e1880f709b416867a29de14f1dcc63a165fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-powershell-to-manage-azure-files"></a>Como utilizar o PowerShell para gerir os Ficheiros do Azure
Pode utilizar o Azure PowerShell para criar e gerir partilhas de ficheiros.

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Instalar os cmdlets do PowerShell para o Storage do Azure
Para preparar para utilizar o PowerShell, transfira e instale os cmdlets do Azure PowerShell. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter as instruções de instalação e do ponto de instalação.

> [!NOTE]
> É recomendado que transfira e instale ou atualize para o módulo do Azure PowerShell mais recente.
> 
> 

Abra uma janela do Azure PowerShell ao clicar em **Iniciar** e escrever **Windows PowerShell**. A janela do PowerShell carrega o módulo do Azure Powershell.

## <a name="create-a-context-for-your-storage-account-and-key"></a>Criar um contexto para a conta de armazenamento e a chave
Crie o contexto da conta de armazenamento. O contexto contém a chave de conta e o nome da conta de armazenamento. Para obter instruções sobre a cópia da chave de conta a partir do [portal do Azure](https://portal.azure.com), veja [View and copy storage access keys (Ver e copiar chaves de acesso de armazenamento)](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

Substitua `storage-account-name` e `storage-account-key` pelo nome da conta de armazenamento e a chave no exemplo seguinte.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>Criar uma nova partilha de ficheiros
Crie a nova partilha, com o nome `logs`.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Tem agora uma partilha de ficheiros no Armazenamento de ficheiros. Em seguida, vamos adicionar um diretório e um ficheiro.

> [!IMPORTANT]
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](https://msdn.microsoft.com/library/azure/dn167011.aspx).
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>Criar um diretório na partilha de ficheiros
Crie um diretório na partilha. No exemplo seguinte, o diretório chama-se `CustomLogs`.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>Carregar um ficheiro local para o diretório
Agora, carregue um ficheiro local para o diretório. O exemplo seguinte carrega um ficheiro de `C:\temp\Log1.txt`. Edite o caminho do ficheiro para que aponte para um ficheiro válido no computador local.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>Listar os ficheiros no diretório
Para ver o ficheiro no diretório, pode listar todos os ficheiros do diretório. Este comando devolve os ficheiros e subdiretórios (se existirem) no diretório CustomLogs.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile devolve uma lista de ficheiros e diretórios para qualquer diretório no qual o objeto passa. “Get AzureStorageFile-partilhar $s” devolve uma lista de ficheiros e diretórios no diretório de raiz. Para obter uma lista de ficheiros num subdiretório, tem de passar o subdiretório para Get-AzureStorageFile. É isso que isto faz – a primeira parte do comando até ao pipe devolve uma instância de diretório do subdiretório CustomLogs. Em seguida, é passado para o Get-AzureStorageFile, que devolve os ficheiros e os diretórios no CustomLogs.

## <a name="copy-files"></a>Copiar ficheiros
A partir da versão 0.9.7 do Azure PowerShell, pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Abaixo, demonstramos como fazer estas operações de cópia com cmdlets do PowerShell.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>Passos seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.

* [FAQ](../storage-files-faq.md)
* [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Resolução de Problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)    