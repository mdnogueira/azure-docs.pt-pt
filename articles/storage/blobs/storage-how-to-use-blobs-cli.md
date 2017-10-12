---
title: "Realizar operações no Armazenamento de blobs do Azure (armazenamento de objetos) com o Azure CLI | Microsoft Docs"
description: Saiba como carregar e transferir blobs no Armazenamento de blobs do Azure, bem como construir uma assinatura de acesso partilhado (SAS) para gerir o acesso a um blob na sua conta de armazenamento.
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: marsma
ms.openlocfilehash: c37fc0b701b668ab6bb9213a487ec8baa33fe663
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Realizar operações de Armazenamento de blobs com o Azure CLI

O Blob Storage do Azure é um serviço para armazenar grandes quantidades de dados de objetos não estruturados, como texto ou dados binários, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS. Este tutorial abrange operações básicas no Armazenamento blobs do Azure como, por exemplo, carregar, transferir e eliminar blobs. Saiba como:

> [!div class="checklist"]
> * Criar um contentor
> * Carregar um ficheiro (blob) para um contentor
> * Listar os blobs num contentor
> * Transferir um blob a partir de um contentor
> * Copiar um blob entre contas de armazenamento
> * Eliminar um blob
> * Apresentar e modificar propriedades e metadados do blob
> * Gerir a segurança com uma assinatura de acesso partilhado (SAS)

Este tutorial precisa da versão 2.0.4 do Azure CLI ou de uma versão posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Criar um contentor

Os contentores assemelham-se aos diretórios existentes no seu computador, na medida em que permitem organizar grupos de blobs num contentor da mesma forma que organiza ficheiros num diretório. Uma conta de armazenamento pode conter um número indeterminado de contentores. Pode armazenar até 500 TB de dados de blobs num contentor, o que corresponde à quantidade máxima de dados de uma conta de armazenamento.

Crie um contentor para armazenar blobs com o comando [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

Os nomes dos contentores têm de começar com uma letra ou um número e só podem conter letras, números e o caráter de hífen (-). Para ter acesso a outras regras sobre como atribuir nomes a blobs e contentores, consulte [Atribuir nomes e referenciar contentores, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="enable-public-read-access-for-a-container"></a>Ativar o acesso de leitura público para um contentor

Um contentor recém-criado é privado por predefinição. Ou seja, ninguém pode aceder ao contentor sem uma [assinatura de acesso partilhado](#create-a-shared-access-signature-sas) ou as chaves de acesso da conta de armazenamento. A utilização do Azure CLI permite modificar este comportamento mediante a definição de permissões de contentor para um de três níveis:

| | |
|---|---|
| `--public-access off` | (Predefinição) Sem acesso de leitura público |
| `--public-access blob` | Acesso de leitura público para blobs apenas |
| `--public-access container` | Acesso de leitura público para blobs, com permissão para listar os blobs no contentor |

Quando define o acesso público para `blob` ou `container`, ativa o acesso só de leitura para qualquer pessoa na Internet. Por exemplo, se quiser apresentar imagens armazenadas como blobs no seu site, terá de ativar o acesso de leitura público. Se quiser ativar o acesso de leitura/escrita, terá de utilizar uma [assinatura de acesso partilhado (SAS)](#create-a-shared-access-signature-sas) em alternativa.

Ative o acesso de leitura público para o contentor com o comando [az storage container set-permission](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>Carregar um blob para um contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são o tipo de blob mais comum armazenado no Armazenamento do Azure. Os blobs de acréscimo são utilizados quando é preciso adicionar dados a um blob existente sem modificar o conteúdo existente, como é o caso das operações de registo. Os blobs de páginas dão suporte aos ficheiros VHD de máquinas virtuais IaaS.

Neste exemplo, vamos carregar um blob para o contentor criado no último passo com o comando [az storage blob upload](/cli/azure/storage/blob#upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Esta operação cria o blob, caso este ainda não exista, ou substitui-o se o mesmo já existir. Carregue vários ficheiros para ver várias entradas quando listar os blobs no passo seguinte.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Liste os blobs no contentor com o comando [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

Resultado do exemplo:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Transferir um blob

Transfira o blob que carregou num passo anterior com o comando [az storage blob download](/cli/azure/storage/blob#download).

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>Copiar um blob entre contas de armazenamento

Pode copiar blobs dentro ou entre contas de armazenamento e regiões de forma assíncrona.

O exemplo seguinte demonstra como copiar blobs de uma conta de armazenamento para outra. Primeiro, vamos criar um contentor na conta de armazenamento de origem, especificando o acesso de leitura público para os respetivos blobs. Em seguida, vamos carregar um ficheiro para o contentor e, por último, vamos copiar o blob desse contentor para um contentor na conta de armazenamento de destino.

Neste exemplo, o contentor de destino já tem de existir na conta de armazenamento de destino para a operação de cópia ser bem-sucedida. Além disso, o blob de origem especificado no argumento `--source-uri` tem de incluir um token de assinatura de acesso partilhado (SAS) ou tem de estar acessível publicamente, tal como acontece neste exemplo.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Eliminar um blob

Elimine o blob do contentor com o comando [az storage blob delete](/cli/azure/storage/blob#delete).

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>Apresentar e modificar propriedades e metadados do blob

Cada blob tem várias propriedades definidas pelo serviço que pode apresentar com o comando [az storage blob show](/cli/azure/storage/blob#show), incluindo o respetivo nome, tipo, comprimento, entre outros. Também pode configurar um blob com as suas próprias propriedades e os respetivos valores com o comando [az storage blob metadata update](/cli/azure/storage/blob/metadata#update).

Neste exemplo, primeiro vamos apresentar as propriedades definidas pelo serviço de um blob e, em seguida, vamos atualizar o blob com duas das nossas próprias propriedades de metadados. Por último, vamos apresentar as propriedades de metadados do blob e os respetivos valores com o comando [az storage blob metadata show](/cli/azure/storage/blob/metadata#show).

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Criar uma assinatura de acesso partilhado (SAS)

As assinaturas de acesso partilhado (SAS) oferecem uma forma de conceder acesso limitado aos objetos existentes na sua conta de armazenamento sem expor as chaves de acesso da sua conta de armazenamento. Para produzir um URI que permita o acesso a um recurso privado, pode criar um token SAS com as permissões e o período de validade (ou seja, a duração efetiva do mesmo) pretendidos, e acrescentá-lo como uma cadeia de consulta ao URL de um recurso, formando assim o URI de SAS completo. Assim, qualquer pessoa que tenha este URI de SAS (isto é, o URL do recurso aliado ao token SAS) pode aceder ao mesmo durante o período de validade do token SAS. Por exemplo, pode querer permitir o acesso de leitura a um blob privado durante dois minutos para que alguém o possa ver.

Nos passos seguintes, irá desativar o acesso público ao seu contentor, testar o acesso privado apenas e, em seguida, gerar e testar um URI de SAS.

### <a name="disable-container-public-access"></a>Desativar o acesso público ao contentor

Em primeiro lugar, defina o nível de acesso do contentor como `off`. Isto indica que não é possível aceder ao contentor ou aos respetivos blobs sem uma assinatura de acesso partilhado ou uma chave de acesso da conta de armazenamento.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>Verificar o acesso privado

Para se certificar de que não existe qualquer acesso de leitura público aos blobs existentes nesse contentor, obtenha o URL para um dos respetivos blobs com o comando [az storage blob url](/cli/azure/storage/blob#url).

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

Navegue até ao URL do blob numa janela do browser privada. É-lhe apresentado um erro `ResourceNotFound` porque o blob é privado e não incluiu uma assinatura de acesso partilhado.

### <a name="create-a-sas-uri"></a>Criar um URI de SAS

Agora, vamos criar um URI de SAS que permite o acesso ao blob. No exemplo seguinte, primeiro vamos povoar uma variável com o URL para o blob com o comando [az storage blob url](/cli/azure/storage/blob#url); em seguida, vamos povoar outra variável com um token SAS gerado com o comando [az storage blob generate-sas](/cli/azure/storage/blob#generate-sas). Por último, vamos produzir o URI de SAS completo para o blob concatenando as duas variáveis, separadas pelo separador de cadeia de consulta `?`.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>Testar o URI de SAS

Numa janela do browser privada, navegue para o URI de SAS completo que apresentou com o comando `echo` no fragmento de código anterior. Desta vez, não lhe é apresentado qualquer erro e pode ver ou transferir o blob.

Aguarde o tempo suficiente para que o URL expire (dois minutos, neste exemplo) e, em seguida, navegue para o URI noutra janela do browser privada. Agora, é-lhe apresentado um erro `AuthenticationFailed` dado que o token SAS expirou entretanto.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta de armazenamento que criou e os blobs que carregou neste tutorial, elimine o grupo de recursos com o comando [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu as noções básicas de como trabalhar com blobs no Armazenamento do Azure:

> [!div class="checklist"]
> * Criar um contentor
> * Carregar um ficheiro (blob) para um contentor
> * Listar os blobs num contentor
> * Transferir um blob a partir de um contentor
> * Copiar um blob entre contas de armazenamento
> * Eliminar um blob
> * Apresentar e modificar propriedades e metadados do blob
> * Gerir a segurança com uma assinatura de acesso partilhado (SAS)

Os recursos que se seguem fornecem informações adicionais sobre como trabalhar com o Azure CLI e com os recursos existentes na sua conta de armazenamento.

* CLI do Azure
  * [Iniciar sessão com o Azure CLI 2.0](/cli/azure/authenticate-azure-cli) – Saiba mais sobre os diferentes métodos de realizar a autenticação na CLI, incluindo o início de sessão não interativo através do [principal de serviço](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal) para executar scripts automáticos do Azure CLI.
  * [Referência de comandos do Azure CLI 2.0](/cli/azure/)
* Explorador de Armazenamento do Microsoft Azure
  * O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é uma aplicação autónoma gratuita da Microsoft que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
