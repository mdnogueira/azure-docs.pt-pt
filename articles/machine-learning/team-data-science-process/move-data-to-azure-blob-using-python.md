---
title: Mover dados para e do armazenamento de Blobs do Azure com o Python | Microsoft Docs
description: Mover dados de e para o Armazenamento de Blobs do Azure com Python
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 24276252-b3dd-4edf-9e5d-f6803f8ccccc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: f6e325f70a37200552832b9c0ac38f6c56471f2c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Mover dados para e do armazenamento de Blobs do Azure com o Python
Este tópico descreve como listar, carregar e transferir blobs utilizando a API do Python. Com a API de Python fornecido no SDK do Azure, pode:

* Criar um contentor
* Carregar um blob para um contentor
* Transferir blobs
* Listar os blobs num contentor
* Eliminar um blob

Para obter mais informações sobre como utilizar a API de Python, consulte [como utilizar o serviço de armazenamento de Blobs do Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md).

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

## <a name="upload-data-to-blob"></a>Carregar dados para o Blob
Adicione o seguinte fragmento perto da parte superior de qualquer código Python no qual pretende aceder programaticamente ao Storage do Azure:

    from azure.storage.blob import BlobService

O **BlobService** objeto permite-lhe trabalhar com blobs e contentores. O código seguinte cria um objeto de BlobService utilizando a chave de conta e o nome da conta de armazenamento. Substitua o nome da conta e chave da conta com a sua conta real e a chave.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Utilize os seguintes métodos para carregar dados para um blob:

1. colocar\_bloco\_blob\_de\_caminho (carrega o conteúdo de um ficheiro do caminho especificado)
2. colocar\_block_blob\_de\_ficheiro (carrega o conteúdo a partir de um ficheiro já está aberto/stream)
3. colocar\_bloco\_blob\_de\_bytes (carregamentos uma matriz de bytes)
4. colocar\_bloco\_blob\_de\_texto (carrega o valor de texto especificado, utilizando a codificação especificada)

O código de exemplo seguinte carrega um ficheiro local para um contentor:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O código de exemplo seguinte carrega todos os ficheiros (excluindo diretórios) num diretório local para o blob storage:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Transferência de dados a partir do Blob
Utilize os seguintes métodos para transferir dados a partir de um blob:

1. obter\_blob\_para\_caminho
2. obter\_blob\_para\_ficheiro
3. obter\_blob\_para\_bytes
4. obter\_blob\_para\_texto

Estes métodos que executam a segmentação necessárias quando o tamanho dos dados exceder 64 MB.

O código de exemplo seguinte transfere os conteúdos de um blob num contentor para um ficheiro local:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O código de exemplo seguinte transfere todos os blobs a partir de um contentor. Utiliza a lista\_para obter a lista de blobs disponíveis no contentor de blobs e transfere-os para um diretório local.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
