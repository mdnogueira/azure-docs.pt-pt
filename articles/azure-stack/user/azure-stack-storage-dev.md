---
title: "Começar a utilizar as ferramentas de desenvolvimento do armazenamento de pilha do Azure"
description: "Orientações para começar a utilizar as ferramentas de desenvolvimento do armazenamento de pilha do Azure"
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 9/25/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.openlocfilehash: 5b2898c64c0f1b5d804e63fa4e4e1218fa7a672c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Começar a utilizar as ferramentas de desenvolvimento do armazenamento de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*


Pilha do Microsoft Azure fornece um conjunto de serviços de armazenamento, incluindo o armazenamento de Blobs do Azure, tabela e fila.

Este artigo fornece orientação rápida sobre como começar a utilizar as ferramentas de desenvolvimento do armazenamento de pilha do Azure. Pode encontrar informações mais detalhadas e código de exemplo nos tutoriais correspondentes do Storage do Azure.

São conhecidos diferenças entre o armazenamento do Azure e Azure pilha de armazenamento, incluindo alguns requisitos específicos para cada plataforma. Por exemplo, existem requisitos de sufixo de ponto final específico para a pilha do Azure e bibliotecas de cliente específico. Para obter mais informações, consulte [Azure pilha de armazenamento: diferenças e as considerações](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliotecas de cliente do Azure
A versão suportada da REST API para o armazenamento de pilha do Azure é 2015-04-05. Não tem paridade completa com a versão mais recente da API de REST do Storage do Azure. Por isso, para as bibliotecas de cliente de armazenamento, tem de ter em consideração a versão que é compatível com REST API 2015-04-05.


|Biblioteca de cliente|Versão suportada de pilha do Azure|Ligação|Especificação de ponto final|
|---------|---------|---------|---------|
|.NET     |6.2.0|Pacote Nuget:<br>[https://www.nuget.org/Packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/Azure-Storage-NET/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|ficheiro App. config|
|Java|4.1.0|Pacote maven:<br>[http://mvnrepository.com/Artifact/com.microsoft.Azure/Azure-Storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Versão do GitHub:<br> [https://github.com/Azure/Azure-Storage-Java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Configuração de cadeia de ligação|
|Node.js     |1.1.0|Ligação NPM:<br>[https://www.npmjs.com/Package/Azure-Storage](https://www.npmjs.com/package/azure-storage)<br>(executar:`npm install azure-storage@1.1.0)`<br><br>Versão do Github:<br>[https://github.com/Azure/Azure-Storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Declaração de instância de serviço||C++|2.4.0|Pacote Nuget:<br>[https://www.nuget.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuração de cadeia de ligação|
|C++|2.4.0|Pacote Nuget:<br>[https://www.nuget.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuração de cadeia de ligação|
|PHP|0.15.0|Versão do GitHub:<br>[https://github.com/Azure/Azure-Storage-PHP/releases/tag/V0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Instalar através do compositor (consulte os detalhes abaixo)|Configuração de cadeia de ligação|
|Python     |0.30.0|Pacote PIP:<br> [https://pypi.python.org/pypi/Azure-Storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Executar:`pip install -v azure-storage==0.30.0)`<br><br>Versão do GitHub:<br> [https://github.com/Azure/Azure-Storage-Python/releases/tag/V0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Declaração de instância de serviço|
|Ruby|0.12.1<br>Pré-visualização|Pacote de RubyGems:<br> [https://rubygems.org/GEMS/Azure-Storage/versions/0.12.1.Preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Versão do GitHub:<br> [https://github.com/Azure/Azure-Storage-Ruby/releases/tag/V0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Configuração de cadeia de ligação|

> [!NOTE]
> Detalhes do PHP<br><br>
>Para instalar através do compositor:
>1. Crie um ficheiro denominado `composer.json` na raiz do projeto com o código seguinte:<br>
>
>   ```
>   {
>       "require":{
>           "Microsoft/azure-storage":"0.15.0"
>        }
>    }
>   ```
>
>2. Transferir [composer.phar](http://getcomposer.org/composer.phar) para a raiz do projeto.
>3. Execute: `php composer.phar install`.
>


## <a name="endpoint-declaration"></a>Declaração de ponto final
Um ponto final de pilha do Azure inclui duas partes: o nome de uma região e o domínio de pilha do Azure.
O Kit de desenvolvimento de pilha do Azure, o ponto final predefinido é **local.azurestack.external**.
Se não tiver a certeza sobre o ponto final, contacte o administrador da nuvem.

## <a name="examples"></a>Exemplos


### <a name="net"></a>.NET

Para a pilha do Azure, o sufixo de ponto final é especificado no ficheiro App. config:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

Para a pilha do Azure, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Para a pilha do Azure, o sufixo de ponto final é especificado na instância da declaração:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

Para a pilha do Azure, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Para a pilha do Azure, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para a pilha do Azure, o sufixo de ponto final é especificado na instância da declaração:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

Para a pilha do Azure, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Armazenamento de blobs

Os seguintes tutoriais de armazenamento de Blobs do Azure são aplicáveis a pilha do Azure. Tenha em atenção os requisitos de sufixo de ponto final específico para a pilha de Azure descrito anterior [exemplos](#examples) secção.

* [Introdução ao armazenamento de Blobs do Azure através do .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Como utilizar o Armazenamento de blobs do Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Como utilizar o Blob storage do Node.js]... /.. / storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Como utilizar o Blob storage do C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como utilizar o Armazenamento de blobs do PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Como utilizar o Blob storage do Azure do Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de blobs do Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Armazenamento de filas

Os seguintes tutoriais de armazenamento de filas do Azure são aplicáveis a pilha do Azure. Tenha em atenção os requisitos de sufixo de ponto final específico para a pilha de Azure descrito anterior [exemplos](#examples) secção.

* [Introdução ao Armazenamento de filas do Azure através do .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Como utilizar o Armazenamento de filas do Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Como utilizar o Armazenamento de filas do Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Como utilizar o armazenamento de filas do C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Como utilizar o Armazenamento de filas do PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Como utilizar o Armazenamento de filas do Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Como utilizar o Armazenamento de filas do Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Table Storage

Os tutoriais de armazenamento de Azure Table seguintes são aplicáveis a pilha do Azure. Tenha em atenção os requisitos de sufixo de ponto final específico para a pilha de Azure descrito anterior [exemplos](#examples) secção.

* [Introdução ao armazenamento de Tabelas do Azure através do .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Como utilizar o Armazenamento de tabelas do Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Como utilizar o Table storage do Azure do Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Como utilizar o Table storage do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como utilizar o Armazenamento de tabelas do PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Como utilizar o Table storage no Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Como utilizar o Armazenamento de tabelas do Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)