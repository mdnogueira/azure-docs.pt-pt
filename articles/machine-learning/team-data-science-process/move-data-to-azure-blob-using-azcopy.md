---
title: Mover dados para e do armazenamento de Blobs do Azure utilizando o AzCopy | Microsoft Docs
description: Mover dados de e para o Armazenamento de Blobs do Azure com AzCopy
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c309ceb2-0e83-4a07-b16d-c997dcd62d5c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 928d579da0cb469fd714b8a456a64917158d8ebc
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Mover dados para e do armazenamento de Blobs do Azure utilizando o AzCopy
O AzCopy é um utilitário da linha de comandos concebido para carregar, transferir e cópia de dados de e para BLOBs, ficheiros e armazenamento de tabelas do Microsoft Azure.

Para obter instruções sobre como instalar o AzCopy e informações adicionais utilizá-lo com a plataforma do Azure, consulte [introdução com o utilitário de linha de comandos do AzCopy](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se estiver a utilizar VM que foi configurado com os scripts fornecidos pelo [máquinas virtuais de ciência de dados no Azure](virtual-machines.md), em seguida, o AzCopy já está instalado na VM.
> 
> [!NOTE]
> Para obter uma introdução completa para o armazenamento de Blobs do Azure, consulte [Noções básicas de Blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [serviço Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento parte do princípio de que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/transferência de dados, tem de conhecer a chave de conta e o nome da conta de armazenamento do Azure.

* Para configurar uma subscrição do Azure, consulte [um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e para obter a conta e informações da chave, consulte [contas do storage do Azure sobre](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Execute comandos do AzCopy
Para executar comandos do AzCopy, abra uma janela de comandos e navegue para o diretório de instalação do AzCopy no seu computador, onde está localizado o AzCopy.exe executável. 

A sintaxe básica para comandos do AzCopy é:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Pode adicionar a localização de instalação do AzCopy para o caminho do sistema e, em seguida, execute os comandos a partir de qualquer diretório. Por predefinição, o AzCopy é instalado para *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* ou *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Carregar ficheiros para um blob do Azure
Para carregar um ficheiro, utilize o seguinte comando:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Transferir os ficheiros de um blob do Azure
Para transferir um ficheiro de um blob do Azure, utilize o seguinte comando:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Transferir blobs entre os contentores do Azure
Para transferir blobs entre os contentores do Azure, utilize o seguinte comando:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Sugestões de utilização do AzCopy
> [!TIP]
> 1. Quando **carregar** ficheiros, */S* carrega recursivamente de ficheiros. Sem este parâmetro, os ficheiros em subdiretórios não são carregados.  
> 2. Quando **transferir** ficheiro, */S* procura recursivamente o contentor até que todos os ficheiros no diretório especificado e nos seus subdiretórios ou todos os ficheiros que correspondam ao padrão especificado no diretório especificado e nos seus subdiretórios, são transferidos.  
> 3. Não é possível especificar um **ficheiro blob específico** para transferir a utilizar o */origem* parâmetro. Para transferir um ficheiro específico, especifique o nome de ficheiro do blob para transferir a utilizar o */padrão* parâmetro. **/S** parâmetro pode ser utilizado para ter AzCopy procure um recursivamente de padrão de nome de ficheiro. Sem o parâmetro do padrão, o AzCopy transfere todos os ficheiros nesse diretório.
> 
> 

