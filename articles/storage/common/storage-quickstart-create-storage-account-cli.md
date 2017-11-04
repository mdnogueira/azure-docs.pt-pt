---
title: "Início rápido do Azure - criar uma conta de armazenamento utilizando a CLI do Azure | Microsoft Docs"
description: Saiba mais rapidamente criar uma nova conta de armazenamento utilizando a CLI do Azure.
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
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: b1fb2da4acf6e06219d790f2354cada4f1e34285
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Criar uma conta de armazenamento utilizando a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este detalhes de início rápido, utilizando a CLI do Azure para criar uma conta de armazenamento do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group#create). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Este exemplo cria um grupo de recursos denominado *myResourceGroup* no *eastus* região.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

Se não tiver a certeza que região para especificar para o `--location` parâmetro, pode obter uma lista de regiões suportadas para a sua subscrição com o [az conta lista-localizações](/cli/azure/account#list) comando.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Criar uma conta de armazenamento standard para fins gerais

Existem vários tipos de contas de armazenamento adequada para cenários de utilização diferentes, cada um deles suporta um ou mais dos serviços de armazenamento (blobs, ficheiros, tabelas ou filas). A tabela seguinte fornece detalhes sobre os tipos de conta de armazenamento disponível.

|**Tipo de conta de armazenamento**|**Standard para fins gerais**|**Premium para fins gerais**|**Armazenamento de blobs, camadas de acesso frequente e esporádico**|
|-----|-----|-----|-----|
|**Serviços suportados**| BLOB, ficheiro, tabela, serviços da fila | Serviço Blob | Serviço Blob|
|**Tipos de blobs suportados**|Os blobs de blocos, blobs de páginas, blobs de acréscimo | Blobs de páginas | Blobs de blocos e blobs de acréscimo|

Crie uma conta de armazenamento padrão para fins gerais com o comando [az storage account create](/cli/azure/storage/account#create).

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>Limpar recursos

Se já não necessita de qualquer um dos recursos no seu grupo de recursos, incluindo a conta de armazenamento que criou neste guia de introdução, elimine o grupo de recursos com o [eliminação do grupo de az](/cli/azure/group#delete) comando.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, criou um grupo de recursos e uma conta de armazenamento standard para fins gerais. Para saber como transferir dados para e da sua conta de armazenamento, avance para armazenamento de Blobs do início rápido.

> [!div class="nextstepaction"]
> [Transferência de objetos de e para o Blob storage do Azure utilizando a CLI do Azure](../blobs/storage-quickstart-blobs-cli.md)