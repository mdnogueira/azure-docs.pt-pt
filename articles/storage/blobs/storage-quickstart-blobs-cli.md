---
title: "Guia de introdução do Azure - objetos de transferência da Blob storage do Azure utilizando a CLI do Azure | Microsoft Docs"
description: "Saiba rapidamente a transferência de objetos do Blob storage do Azure utilizando a CLI do Azure"
services: storage
documentationcenter: na
author: mmacy
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
ms.author: marsma
ms.openlocfilehash: c9b7e7a1fbc6b67821183ce31bdf2527de490c92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>Transferência de objetos para/de Blob storage do Azure utilizando a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este detalhes de início rápido, utilizando a CLI do Azure para carregar e transferir dados para e do armazenamento de Blobs do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Criar um contentor

Os BLOBs sempre são carregados num contentor. Contentores permitem organizar grupos de blobs, tal como organizar os ficheiros em diretórios no seu computador.

Crie um contentor para armazenar blobs com o comando [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Carregar um blob

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. A maioria dos ficheiros armazenados no Blob storage são armazenados como blobs de blocos. Acrescentar blobs são utilizados quando dados tem de ser adicionados a um blob existente sem modificar o respetivo existente contents, tais como o registo. Os blobs de páginas dão suporte aos ficheiros VHD de máquinas virtuais IaaS.

Neste exemplo, podemos carregar um blob para o contentor foi criada no último passo com o [carregamento de blob de armazenamento az](/cli/azure/storage/blob#upload) comando.

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Esta operação cria o blob, caso este ainda não exista, ou substitui-o se o mesmo já existir. Carregar ficheiros tantos como pretender antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Liste os blobs no contentor com o comando [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Transferir um blob

Utilize o [transferências de blob de armazenamento az](/cli/azure/storage/blob#download) comando para transferir um blob que carregou anteriormente.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com o AzCopy

O [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) utilitário é outra opção para a transferência de dados de script de elevado desempenho para o Storage do Azure. Pode utilizar o AzCopy para transferir dados para e do armazenamento de BLOBs, de ficheiros e de tabela.

Como obter um exemplo rápido, eis o comando do AzCopy para carregar um ficheiro denominado *omeuficheiro.txt* para o *mystoragecontainer* contentor.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se já não necessita de qualquer um dos recursos no seu grupo de recursos, incluindo a conta de armazenamento que criou neste guia de introdução, elimine o grupo de recursos com o [eliminação do grupo de az](/cli/azure/group#delete) comando.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, aprendeu como transferir ficheiros entre disco local e um contentor do Blob storage do Azure. Para obter mais informações sobre como trabalhar com blobs no armazenamento do Azure, avance para o tutorial para trabalhar com o Blob storage do Azure.

> [!div class="nextstepaction"]
> [Como: Blob operações de armazenamento com a CLI do Azure](storage-how-to-use-blobs-cli.md)
