---
title: Obter os metadados atividade no Azure Data Factory | Microsoft Docs
description: Saiba como pode utilizar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado numa SQL Database do Azure ou do Azure SQL Data Warehouse a partir de um pipeline do Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: shlo
ms.openlocfilehash: 99182b2ed91f6d60f499be0078077bf52fe8b366
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Obter os metadados atividade no Azure Data Factory
A atividade Obter Metadados pode ser utilizada para obter os metadados de quaisquer dados no Azure Data Factory. Esta atividade é suportada apenas para fábricas de dados da versão 2. Podem ser utilizado nos seguintes cenários:

- Validar as informações de metadados de quaisquer dados
- Acionar um pipeline, quando os dados pronto / disponíveis

A seguinte funcionalidade está disponível no fluxo de controlo:
- A saída da atividade de GetMetadata pode ser utilizada em expressões condicionais para efetuar a validação.
- Um pipeline pode ser acionado quando a condição é satisfeita através de efetue-até ciclo

A utiliza GetMetadata atividade um conjunto de dados como um necessária de entrada e saídas as informações de metadados disponível como saída. Atualmente, o conjunto de dados do blob do Azure só é suportado. Os campos de metadados suportados são tamanho, a estrutura e a hora de lastModified.  

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [documentação do Data Factory V1](v1/data-factory-introduction.md).


## <a name="syntax"></a>Sintaxe

### <a name="get-metadata-activity-definition"></a>Obter a definição de metadados de atividade:
No exemplo seguinte, a atividade de GetMetadata devolve metadados sobre os dados representados pelo MyDataset. 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>Definição do conjunto de dados:

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>Saída
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>Propriedades do tipo
Atualmente GetMetadata atividade pode obter os seguintes tipos de informações de metadados de um conjunto de dados do storage do Azure.

Propriedade | Descrição | Valores Permitidos | Necessário
-------- | ----------- | -------------- | --------
fieldList | Lista os tipos de informações de metadados necessários.  | <ul><li>Tamanho</li><li>estrutura</li><li>LastModified</li></ul> |    Não<br/>Se estiver vazia, a atividade devolve todas as informações de metadados suportados 3. 
Conjunto de dados | O conjunto de dados de referência, cujo metadados da atividade é possível obter pela atividade GetMetadata. <br/><br/>O tipo de conjunto de dados suportado atualmente é Blob do Azure. Duas propriedades sub são: <ul><li><b>referenceName</b>: referência a um conjunto de dados de Blobs do Azure existente</li><li><b>tipo</b>: uma vez que o conjunto de dados está a ser referenciado, é do tipo "DatasetReference"</li></ul> |    <ul><li>Cadeia</li><li>DatasetReference</li></ul> | Sim

## <a name="next-steps"></a>Passos seguintes
Outras atividades de fluxo de controlo suportadas pela fábrica de dados, consulte: 

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade de Web](control-flow-web-activity.md)