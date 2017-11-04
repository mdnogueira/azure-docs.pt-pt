---
title: Mapeamento de colunas do conjunto de dados no Azure Data Factory | Microsoft Docs
description: Saiba como mapear colunas de origem para colunas de destino.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ae092308c5d2579a5b513657787ae6dbbfadaf05
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de destino
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). 

Mapeamento de colunas pode ser utilizado para especificar a forma como as colunas especificadas na "estrutura de" do mapa da tabela de origem para colunas especificada na "estrutura de" da tabela do sink. O **columnMapping** propriedade está disponível no **typeProperties** secção a atividade de cópia.

Mapeamento de colunas suporta os seguintes cenários:

* Todas as colunas na estrutura do conjunto de dados de origem estão mapeadas para todas as colunas na estrutura do conjunto de dados de sink.
* Um subconjunto de colunas na estrutura do conjunto de dados de origem está mapeado para todas as colunas na estrutura do conjunto de dados de sink.

Seguem-se as condições de erro que resultam numa exceção:

* Menos colunas ou mais colunas na "estrutura de" da tabela do sink ao especificado no mapeamento.
* Mapeamento duplicado.
* Resultado da consulta SQL não tem um nome de coluna especificado no mapeamento.

> [!NOTE]
> Os exemplos seguintes são para o SQL do Azure e BLOBs do Azure, mas são aplicáveis a qualquer arquivo de dados que suporta conjuntos de dados retangular. Ajuste o conjunto de dados e definições de serviço ligado nos exemplos para apontar para dados da origem de dados relevantes.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exemplo 1 – coluna mapeamento de SQL do Azure para BLOBs do Azure
Neste exemplo, a tabela de entrada tem uma estrutura e aponta para uma tabela SQL na base de dados do SQL do Azure.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Neste exemplo, a tabela de saída tem uma estrutura e aponta para um blob no armazenamento de Blobs do Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

O JSON seguinte define uma atividade de cópia num pipeline. As colunas a partir da origem mapeado para colunas no sink (**columnMappings**) utilizando o **tradutor** propriedade.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Fluxo de mapeamento de colunas:**

![Fluxo de mapeamento de coluna](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exemplo 2 – coluna mapeamento de consulta SQL do Azure SQL ao blob do Azure
Neste exemplo, uma consulta SQL é utilizada para extrair dados do SQL do Azure em vez de apenas especificar o nome da tabela e os nomes das colunas na secção "estrutura". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
Neste caso, os resultados da consulta primeiro estão mapeados para colunas especificadas na "estrutura" de origem. Em seguida, as colunas a partir da origem "estrutura" são mapeadas para colunas no sink "estrutura" com regras que especificado em columnMappings.  Suponha que a consulta devolve 5 colunas, dois mais colunas do que as especificadas em "estrutura" de origem.

**Fluxo de mapeamento de coluna**

![Mapeamento de coluna fluxo-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo para um tutorial sobre como utilizar a atividade de cópia: 

- [Copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
