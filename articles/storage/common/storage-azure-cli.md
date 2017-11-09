---
title: Utilizar a CLI do Azure 2.0 com o Storage do Azure | Microsoft Docs
description: "Saiba como utilizar a Interface de linha de comandos do Azure (CLI do Azure) 2.0 com armazenamento do Azure para criar e gerir contas de armazenamento e de trabalhar com blobs do Azure e os ficheiros. O 2.0 CLI do Azure é uma ferramenta de plataforma escrita no Python."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.openlocfilehash: 4f4070c5a02e559bd299033865aa5258532498aa
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Utilizar a CLI do Azure 2.0 com o Storage do Azure

O open source, plataforma 2.0 de CLI do Azure fornece um conjunto de comandos para trabalhar com a plataforma do Azure. Fornece muito a mesma funcionalidade que foi encontrada no [portal do Azure](https://portal.azure.com), incluindo acesso a dados avançado.

Neste guia, iremos mostrar-lhe como utilizar o [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) para efetuar várias tarefas de trabalhar com recursos na sua conta do Storage do Azure. Recomendamos que pode transfere e instalar ou atualizar para a versão mais recente do CLI 2.0 antes de utilizar este guia.

Os exemplos do guia assumem a utilização da shell de deteção no Ubuntu, mas outras plataformas devem efetuar da mesma forma. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este guia pressupõe que compreende os conceitos básicos do Storage do Azure. Também parte do princípio que está capazes de satisfazer os requisitos de criação de conta que são especificados abaixo para o Azure e o serviço de armazenamento.

### <a name="accounts"></a>Contas
* **Conta do Azure**: Se ainda não tiver uma subscrição do Azure, [criar uma conta do Azure gratuita](https://azure.microsoft.com/free/).
* **Conta de Armazenamento**: veja [Criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](storage-create-storage-account.md).

### <a name="install-the-azure-cli-20"></a>Instalar o Azure CLI 2.0

Transfira e instale o 2.0 CLI do Azure ao seguir as instruções descritas no [instalar o Azure CLI 2.0](/cli/azure/install-az-cli2).

> [!TIP]
> Se tiver problemas com a instalação, veja o [resolução de problemas de instalação](/cli/azure/install-az-cli2#installation-troubleshooting) secção do artigo e o [instalar resolução de problemas](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) guia no GitHub.
>

## <a name="working-with-the-cli"></a>Trabalhar com a CLI

Depois de instalar a CLI, pode utilizar o `az` comando na sua interface de linha de comandos (Bash Terminal, a linha de comandos) para aceder aos comandos da CLI do Azure. Tipo de `az` comando para ver uma lista completa dos comandos base (seguinte exemplo de saída foi truncado):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

Na sua interface de linha de comandos, execute o comando `az storage --help` à lista de `storage` comando subgrupos. As descrições dos subgrupos fornecem uma descrição geral da funcionalidade que fornece a CLI do Azure para trabalhar com os recursos de armazenamento.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ligar a CLI à sua subscrição do Azure

Para trabalhar com os recursos na sua subscrição do Azure, tem primeiro de iniciar sessão sua conta do Azure com `az login`. Existem várias formas, que pode iniciar sessão:

* **Início de sessão interativo**:`az login`
* **Iniciar sessão com o nome de utilizador e palavra-passe**:`az login -u johndoe@contoso.com -p VerySecret`
  * Esta ação não funcionar com contas Microsoft ou contas que utilizam a autenticação multifator.
* **Iniciar sessão com um principal de serviço**:`az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Script de exemplo 2.0 da CLI do Azure

Em seguida, iremos irá trabalhar com um script de shell pequenas que emite alguns comandos de Azure CLI 2.0 básicos para interagir com recursos de armazenamento do Azure. O script primeiro cria um novo contentor na sua conta de armazenamento, em seguida, carrega um ficheiro existente (como um blob) para esse contentor. Em seguida, apresenta uma lista de todos os blobs no contentor e por fim, transfere o ficheiro para um destino no seu computador local que especificar.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Configure e execute o script**

1. Abra o editor de texto favorito, em seguida, copie e cole o script anterior no editor.

2. Em seguida, Atualize as variáveis o script para refletir as definições de configuração. Substitua os seguintes valores conforme especificado:

   * **\<storage_account_name\>**  o nome da sua conta de armazenamento.
   * **\<storage_account_key\>**  a chave de acesso primária ou secundária para a sua conta de armazenamento.
   * **\<container_name\>**  um nome novo contentor para criar, por exemplo, "azure-cli-exemplo-container".
   * **\<blob_name\>**  um nome para o blob de destino no contentor.
   * **\<file_to_upload\>**  o caminho para pequenos ficheiros no seu computador local, tais como "~ / images/HelloWorld.png".
   * **\<destination_file\>**  o destino de ficheiro caminho, tal como "~ / downloadedImage.png".

3. Depois de atualizou as variáveis necessárias, guarde o script e saia do editor. Os passos partem do princípio de que com o nome do script **my_storage_sample.sh**.

4. Marque o script como executável, se necessário:`chmod +x my_storage_sample.sh`

5. Execute o script. Por exemplo, no Bash:`./my_storage_sample.sh`

Deverá ver um resultado semelhante ao seguinte e o  **\<destination_file\>**  que especificou no script deve ser apresentadas no computador local.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> A saída anterior está a ser **tabela** formato. Pode especificar que o formato a utilizar com a especificação de saída a `--output` argumento nos seus comandos da CLI, ou defina-globalmente utilizando `az configure`.
>

## <a name="manage-storage-accounts"></a>Gerir contas de armazenamento

### <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento
Para utilizar o Armazenamento do Azure, é necessária uma conta de armazenamento. Pode criar uma nova conta de armazenamento do Azure depois de configurar o computador para [ligar à sua subscrição](#connect-to-your-azure-subscription).

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location`[Necessário]: localização. Por exemplo, "EUA Oeste".
* `--name`[Necessário]: O nome da conta de armazenamento. O nome tem de ter 3 a 24 carateres de comprimento e utilizar apenas carateres alfanuméricos minúsculos.
* `--resource-group`[Necessário]: nome do grupo de recursos.
* `--sku`[Necessário]: A conta de armazenamento SKU. Valores permitidos:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>Definir variáveis de ambiente de conta do storage do Azure predefinida
Pode ter várias contas de armazenamento na sua subscrição do Azure. Para selecionar um para utilizar para todos os comandos de armazenamento subsequentes, pode definir estas variáveis de ambiente:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Outra forma de definir uma conta do storage predefinida é através da utilização de uma cadeia de ligação. Em primeiro lugar, obter a cadeia de ligação com o `show-connection-string` comando:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Em seguida, copie a cadeia de ligação de saída e defina o `AZURE_STORAGE_CONNECTION_STRING` variável de ambiente (poderá ter de incluir a cadeia de ligação aspas):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Todos os exemplos das secções seguintes deste artigo partem do princípio de que definiu o `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` variáveis de ambiente.
>

## <a name="create-and-manage-blobs"></a>Criar e gerir os blobs
Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, tais como texto ou dados binários, que podem ser acedidos de qualquer local no mundo através de HTTP ou HTTPS. Esta secção assume que já estiver familiarizado com conceitos de armazenamento de Blobs do Azure. Para obter informações detalhadas, consulte [introdução ao Blob storage do Azure através do .NET](../blobs/storage-dotnet-how-to-use-blobs.md) e [conceitos do serviço Blob](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Criar um contentor
Todos os BLOBs no armazenamento do Azure tem de ser um contentor. Pode criar um contentor utilizando o `az storage container create` comando:

```azurecli
az storage container create --name <container_name>
```

Pode definir um de três níveis de acesso de leitura para um novo contentor, especificando o opcional `--public-access` argumento:

* `off`(predefinição): os dados de contentor são privados para o proprietário da conta.
* `blob`: Público acesso de leitura de blobs.
* `container`: Lista de leitura e acesso público para o contentor inteiro.

Para obter mais informações, consulte [gerir o acesso de leitura anónimo a contentores e blobs](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Carregar um blob para um contentor
Blob storage do Azure suporta bloco, de acréscimo e blobs de páginas. Carregar os blobs para um contentor utilizando o `blob upload` comando:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

 Por predefinição, o `blob upload` comando carrega *.vhd ficheiros para blobs de páginas ou blobs de blocos, caso contrário. Para especificar outro tipo quando carregar um blob, pode utilizar o `--type` argumento – valores permitido são `append`, `block`, e `page`.

 Para obter mais informações sobre os tipos de blob diferente, consulte [compreender os Blobs de blocos, os Blobs de acréscimo e Blobs de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Transferir um blob a partir de um contentor
Este exemplo demonstra como transferir um blob a partir de um contentor:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Listar os blobs num contentor com o [lista de BLOBs de armazenamento az](/cli/azure/storage/blob#list) comando.

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Blobs de cópia
Pode copiar blobs dentro ou entre contas de armazenamento e regiões de forma assíncrona.

O exemplo seguinte demonstra como copiar blobs de uma conta de armazenamento para outra. Primeiro, vamos criar um contentor na conta de armazenamento de origem, especificando o acesso de leitura público para os respetivos blobs. Em seguida, vamos carregar um ficheiro para o contentor e, por último, vamos copiar o blob desse contentor para um contentor na conta de armazenamento de destino.

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
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

No exemplo acima, o contentor de destino tem de existir na conta de armazenamento de destino para a operação de cópia com êxito. Além disso, o blob de origem especificado no argumento `--source-uri` tem de incluir um token de assinatura de acesso partilhado (SAS) ou tem de estar acessível publicamente, tal como acontece neste exemplo.

### <a name="delete-a-blob"></a>Eliminar um blob
Para eliminar um blob, utilize o `blob delete` comando:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Criar e gerir partilhas de ficheiros
Ficheiros do Azure oferece um armazenamento partilhado para aplicações utilizando o protocolo Server Message Block (SMB). Máquinas virtuais do Microsoft Azure e serviços em nuvem, bem como as aplicações no local, podem partilhar os dados de ficheiro através de partilhas montadas. Pode gerir partilhas de ficheiros e dados de ficheiros através da CLI do Azure. Para obter mais informações sobre ficheiros do Azure, consulte [introdução ao Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros
Uma partilha de ficheiros do Azure é uma partilha de ficheiros SMB no Azure. Todos os ficheiros e diretórios têm de ser criados numa partilha de ficheiros. Uma conta pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até os limites de capacidade da conta de armazenamento. O exemplo seguinte cria uma partilha de ficheiros com o nome **myshare**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Criar um diretório
Um diretório fornece uma estrutura hierárquica numa partilha de ficheiros do Azure. O exemplo seguinte cria um diretório com o nome **myDir** na partilha de ficheiros.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Um caminho de diretório pode incluir vários níveis, por exemplo **dir1/dir2**. No entanto, tem de se certificar de que todos os diretórios de principal de existir antes de criar um subdiretório. Por exemplo, para o caminho **dir1/dir2**, deve primeiro criar diretório **dir1**, em seguida, criar o diretório **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Carregar um ficheiro local para uma partilha
O exemplo seguinte carrega um ficheiro de **~/temp/samplefile.txt** raiz do **myshare** partilha de ficheiros. O `--source` argumento especifica o ficheiro local existente para carregar.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Como com a criação de diretórios, pode especificar um caminho de diretório na partilha de carregar o ficheiro para um diretório existente na partilha de:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Um ficheiro na partilha pode ser até 1 TB de tamanho.

### <a name="list-the-files-in-a-share"></a>Lista os ficheiros numa partilha
Pode listar ficheiros e diretórios numa partilha, utilizando o `az storage file list` comando:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Copiar ficheiros      
Pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Por exemplo, para copiar um ficheiro para um diretório numa partilha diferentes:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Criar partilha de instantâneos
Pode criar um instantâneo de partilha, utilizando o `az storage share snapshot` comando:

```cli
az storage share snapshot -n <share name>
```

Saída de exemplo
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Instantâneos de partilha de lista

Pode listar os instantâneos de partilha de uma determinada partilha utilizando`az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Saída de exemplo**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Procurar a partilha de instantâneos
Também pode navegar para uma partilha específica para ver os conteúdos através de instantâneos `az storage file list`. Um tem de especificar o nome da partilha `--share-name <snare name>` e o carimbo`--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Saída de exemplo**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Restaurar a partir de instantâneos de partilha

Pode restaurar um ficheiro, copiar ou transferir um ficheiro a partir de uma partilha através de instantâneos `az storage file download` comando

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Saída de exemplo**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Eliminar instantâneo de partilha
Pode eliminar um instantâneo de partilha utilizando o `az storage share delete` comando fornecendo `--snapshot` parâmetro com o carimbo de instantâneo de partilha:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Saída de exemplo
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Passos seguintes
Seguem-se alguns recursos adicionais para aprender mais sobre como trabalhar com o 2.0 CLI do Azure.

* [Introdução ao Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Referência de comandos do Azure CLI 2.0](/cli/azure)
* [CLI do Azure 2.0 no GitHub](https://github.com/Azure/azure-cli)
