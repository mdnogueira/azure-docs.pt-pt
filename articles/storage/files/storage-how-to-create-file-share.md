---
title: Como criar uma partilha de Ficheiros do Azure | Microsoft Docs
description: Como criar uma partilha de ficheiros do Azure nos Ficheiros do Azure com o portal do Azure, o PowerShell e a CLI do Azure.
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
ms.openlocfilehash: bc01e5427f32e9532e39694f6de9f0b1146eda35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-file-share-in-azure-files"></a>Criar uma partilha de ficheiros nos Ficheiros do Azure
Pode criar partilhas de Ficheiros do Azure com o [portal do Azure](https://portal.azure.com/), os cmdlets do PowerShell do Armazenamento do Azure, as bibliotecas de cliente do Armazenamento do Azure ou a API REST do Armazenamento do Azure. Neste tutorial, irá aprender:
* [Como criar uma partilha de Ficheiros do Azure com o portal do Azure](#Create file share through the Portal)
* [Como criar uma partilha de Ficheiros do Azure com o PowerShell](#Create file share using PowerShell)
* [Como criar uma partilha de Ficheiros do Azure com a CLI](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>Pré-requisitos
Para criar uma partilha de Ficheiros do Azure, pode utilizar uma Conta de Armazenamento já existente ou [criar uma Conta de Armazenamento do Azure nova](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para criar uma partilha de Ficheiros do Azure com o PowerShell, precisa da chave de conta e do nome da sua conta de armazenamento. Se quiser utilizar o PowerShell ou a CLI, precisa da chave da conta de Armazenamento.

## <a name="create-file-share-through-the-azure-portal"></a>Criar a partilha de ficheiros através do portal do Azure
1. **Aceda ao painel da Conta de Armazenamento no portal do Azure**:    
    ![Painel da Conta de Armazenamento](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Clique no botão Adicionar Partilha de Ficheiros**:    
    ![Clique no botão adicionar partilha de ficheiros](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Indique o Nome e a Quota. Atualmente, a quota pode ter um máximo de 5 TiB**:    
    ![Indique um nome e uma quota pretendida para a partilha de ficheiros nova](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Veja a partilha de ficheiros nova**: ![Veja a partilha de ficheiros nova](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Carregue um ficheiro**: ![Carregue um ficheiro](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Navegue para a partilha de ficheiros e faça a gestão dos seus diretórios e ficheiros**: ![Navegar para a partilha de ficheiros](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Criar a partilha de Ficheiros através do PowerShell
Para preparar para utilizar o PowerShell, transfira e instale os cmdlets do Azure PowerShell. Veja [Como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) para obter as instruções de instalação e do ponto de instalação.

> [!Note]  
> É recomendado que transfira e instale ou atualize para o módulo do Azure PowerShell mais recente.

1. **Crie um contexto para a conta de armazenamento e a chave** O contexto contém o nome da conta de armazenamento e a chave da conta. Para obter instruções sobre a cópia da chave de conta a partir do [portal do Azure](https://portal.azure.com/), veja [View and copy storage access keys (Ver e copiar chaves de acesso de armazenamento)](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Criar uma partilha de ficheiros nova**:    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Criar a partilha de Ficheiros através da Interface de Linha de Comandos (CLI)
1. **Para se preparar para utilizar uma Interface de Linha de Comandos (CLI), transfira e instale a CLI do Azure.**  
    Veja [Instalar a CLI 2.0 do Azure](/cli/azure/install-az-cli2.md) e [Introdução à CLI 2.0 do Azure](/cli/azure/get-started-with-azure-cli.md).

2. **Crie uma cadeia de ligação para a conta de armazenamento na qual quer criar a partilha.**  
    Substitua ```<storage-account>``` e ```<resource_group>``` pelo nome da conta de armazenamento e pelo grupo de recursos do exemplo seguinte:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Criar partilha de ficheiros**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Passos seguintes
* [Connect and Mount File Share - Windows](storage-how-to-use-files-windows.md) (Ligar e Montar Partilha de Ficheiros - Windows)
* [Connect and Mount File Share - Linux](../storage-how-to-use-files-linux.md) (Ligar e Montar Partilha de Ficheiros - Linux)
* [Connect and Mount File Share - macOS](storage-how-to-use-files-mac.md) (Ligar e Montar Partilha de Ficheiros - macOS)

Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.

* [FAQ](../storage-files-faq.md)
* [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Resolução de Problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)   