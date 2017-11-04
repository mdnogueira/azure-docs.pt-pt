---
title: "Carregar grandes quantidades de dados no Data Lake Store utilizando métodos offline | Microsoft Docs"
description: Utilize a ferramenta de AdlCopy para copiar dados de blobs de armazenamento do Azure para o Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/01/2017
ms.author: nitinme
ms.openlocfilehash: 744759968706e0a2c9fe8c1c153f44cc958e31b8
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Utilizar o serviço de importação/exportação do Azure para cópia offline dos dados ao Data Lake Store
Neste artigo, irá aprender como copiar conjuntos enormes de dados (> 200 GB) para um Azure Data Lake Store utilizando métodos de cópia offline, como o [serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md). Especificamente, o ficheiro utilizado como um exemplo neste artigo é 339,420,860,416 bytes ou cerca de 319 GB no disco. Vamos chamar 319GB.tsv este ficheiro.

O serviço importar/exportar do Azure ajuda-o a transferir grandes quantidades de dados seja mais segura para o Blob storage do Azure por envio unidades de disco rígido para um datacenter do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento do Azure**.
* **Uma conta do Azure Data Lake Store**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Preparar os dados
Antes de utilizar o serviço de importação/exportação, interromper o ficheiro de dados a serem transferidos **em cópias são menos 200 GB** de tamanho. A ferramenta de importação não funciona com ficheiros maiores do que 200 GB. Neste tutorial, iremos dividir o ficheiro em segmentos de 100 GB. Pode fazê-lo utilizando [Cygwin](https://cygwin.com/install.html). Cygwin suporta comandos de Linux. Neste caso, utilize o seguinte comando:

    split -b 100m 319GB.tsv

A operação de divisão cria ficheiros com os seguintes nomes.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Preparar os discos com os dados
Siga as instruções em [utilizando o serviço do Azure para importar/exportar](../storage/common/storage-import-export-service.md) (sob a **preparar as suas unidades** secção) para preparar as unidades de disco rígido. Segue-se a sequência geral:

1. Obter um disco rígido que cumpre os requisitos para ser utilizado para o serviço importar/exportar do Azure.
2. Identifique uma conta de armazenamento do Azure onde os dados serão copiados depois-é enviada para o datacenter do Azure.
3. Utilize o [ferramenta de importação/exportação do Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), um utilitário da linha de comandos. Eis um fragmento de exemplo que mostra como utilizar a ferramenta.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Consulte [utilizando o serviço do Azure para importar/exportar](../storage/common/storage-import-export-service.md) para obter mais fragmentos de exemplo.
4. O comando anterior cria um ficheiro do diário de alterações na localização especificada. Utilizar este ficheiro de diário de alterações para criar uma tarefa de importação do [portal do Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Criar uma tarefa de importação
Agora, pode criar uma tarefa de importação utilizando as instruções em [utilizando o serviço do Azure para importar/exportar](../storage/common/storage-import-export-service.md) (sob a **criar a tarefa de importação** secção). Para esta tarefa de importação com outros detalhes também fornecem o ficheiro de diário criado durante a preparação das unidades de disco.

## <a name="physically-ship-the-disks"></a>Fisicamente são enviados os discos
Agora pode enviar fisicamente os discos para um datacenter do Azure. Não existe, os dados são copiados ao longo para os blobs Storage do Azure que forneceu ao criar a tarefa de importação. Além disso, ao criar a tarefa, caso optado para fornecer as informações de controlo mais tarde, pode voltar para a tarefa de importar e atualizar o número de controlo.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copiar dados de blobs de armazenamento do Azure ao Azure Data Lake Store
Depois do Estado da tarefa de importação do mostra que é concluída, pode verificar se os dados estão disponíveis nos blobs de armazenamento do Azure que especificou. Em seguida, pode utilizar uma variedade de métodos para mover os dados de blobs para o Azure Data Lake Store. Para todas as opções disponíveis para carregar dados, consulte [ingestão de dados no Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

Nesta secção, iremos fornecer as definições de JSON que pode utilizar para criar um pipeline do Azure Data Factory para copiar dados. Pode utilizar estas definições JSON do [portal do Azure](../data-factory/v1/data-factory-copy-activity-tutorial-using-azure-portal.md), ou [Visual Studio](../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md), ou [Azure PowerShell](../data-factory/v1/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Serviço ligado de origem (blob Storage do Azure)
````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Serviço ligado (Azure Data Lake Store) de destino
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Conjunto de dados de entrada
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Conjunto de dados de saída
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Pipeline (atividade de cópia)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Para obter mais informações, consulte [mover dados do blob Storage do Azure para o Azure Data Lake Store utilizando o Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Reconstrua os ficheiros de dados no Azure Data Lake Store
Vamos começar a utilizar um ficheiro que foi 319 GB e quebrou-lo para baixo para ficheiros de tamanho mais pequeno, para que pode ser transferida através do serviço de importação/exportação do Azure. Agora que os dados no Azure Data Lake Store, iremos pode reconstrua o ficheiro para o tamanho original. Pode utilizar o seguinte cmldts do Azure PowerShell para o fazer.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Passos seguintes
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight with Data Lake Store (Utilizar o Azure HDInsight com o Data Lake Store)](data-lake-store-hdinsight-hadoop-use-portal.md)
