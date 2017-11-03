---
title: Atividade de pesquisa no Azure Data Factory | Microsoft Docs
description: Saiba como utilizar a atividade de pesquisa para procurar um valor de uma origem externa. Este resultado pode ser ainda referenciado por atividades bem-sucedidas.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: 30173f8eea2ccbbcd44018596cf34b3769a64b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de pesquisa no Azure Data Factory
A atividade de Pesquisa pode ser utilizada para ler ou procurar registos/nomes de tabelas/valores em qualquer origem externa. Este resultado pode ser ainda referenciado por atividades bem-sucedidas. 

As seguintes origens de dados são atualmente suportadas para a pesquisa:
- Ficheiro JSON no Blob do Azure
- Ficheiro JSON no local
- Base de dados SQL do Azure (convertidos a partir da consulta de dados JSON)
- Table Storage do Azure (convertidos a partir da consulta de dados JSON)

Atividade de pesquisa é útil quando pretender dinamicamente obter uma lista de ficheiros / registos/tabelas a partir de um ficheiro de configuração ou de uma origem de dados. A saída da atividade pode ser mais utilizada por outras atividades para efetuar específicas de processamento apenas esses itens.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [documentação do Data Factory V1](v1/data-factory-introduction.md).


## <a name="example"></a>Exemplo
Neste exemplo, a atividade de cópia copia dados de uma tabela SQL na base de dados SQL do Azure para armazenamento de Blobs do Azure. O nome da tabela SQL é armazenado num ficheiro JSON no armazenamento de Blobs. A atividade de pesquisa procura o nome da tabela no tempo de execução. Esta abordagem permite JSON ser modificado dinamicamente se desejar pipelines/conjuntos de dados. 

### <a name="pipeline"></a>Pipeline
Este pipeline contém duas atividades: **procurar** e **cópia**. 

- A atividade de pesquisa está configurada para utilizar o LookupDataset, que se refere a uma localização de um Blob Storage do Azure. A atividade de pesquisa lê o nome da tabela SQL a partir de um ficheiro JSON nesta localização. 
- A atividade de cópia utiliza a saída da atividade de pesquisa (nome da tabela SQL). TableName no conjunto de dados de origem (SourceDataset) está configurado para utilizar a saída da atividade de pesquisa. A atividade de cópia copia dados da tabela SQL para uma localização de armazenamento de Blobs do Azure que é especificado pelo SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Conjunto de dados de pesquisa
O conjunto de dados de referência refere-se para o ficheiro de sourcetable.json na pasta de pesquisa no Storage do Azure especificado pelo AzureStorageLinkedService. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>Conjunto de dados de origem para a atividade de cópia
O conjunto de dados de origem utiliza a saída da atividade de pesquisa, que é o nome da tabela SQL. A atividade de cópia copia os dados desta tabela de SQL para uma localização de armazenamento de Blobs do Azure especificada pelo conjunto de dados sink. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Sink do conjunto de dados para a atividade de cópia
A atividade de cópia copia dados da tabela SQL filebylookup.csv ficheiro na pasta de csv no Storage do Azure especificado pelo AzureStorageLinkedService. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Esta conta de armazenamento contém o ficheiro JSON com os nomes das tabelas SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Serviço ligado da Base de Dados SQL do Azure
Esta base de dados SQL do Azure contém os dados para ser copiado para o armazenamento de Blobs. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>SourceTable.JSON

#### <a name="set-of-objects"></a>Conjunto de objetos

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```
#### <a name="array-of-objects"></a>Matriz de objetos

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```



## <a name="type-properties"></a>Propriedades do tipo
Nome | Descrição | Tipo | Necessário
---- | ----------- | ---- | --------
Conjunto de dados | O atributo de conjunto de dados é fornecer a referência de conjunto de dados para a pesquisa. Atualmente, os tipos de conjunto de dados suportados são:<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | par chave-valor | Sim
origem | Propriedades específicas do conjunto de dados de origem, mesmo que a origem de atividade de cópia | Par chave/valor | Não
firstRowOnly | Devolve a primeira linha ou todas as linhas. | Valor booleano | Não

## <a name="next-steps"></a>Passos seguintes
Outras atividades de fluxo de controlo suportadas pela fábrica de dados, consulte: 

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade Obter Metadados](control-flow-get-metadata-activity.md)
- [Atividade de Web](control-flow-web-activity.md)
