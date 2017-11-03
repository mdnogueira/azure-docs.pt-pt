---
title: "Mapeamento de esquema na atividade de cópia | Microsoft Docs"
description: "Saiba mais sobre como tipos de esquemas e dados a partir dos dados de origem para sink de dados ao copiar dados os mapas de atividade de cópia no Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jingwang
ms.openlocfilehash: 459c792028d3eede059814324597811b24e65ac2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="schema-mapping-in-copy-activity"></a>Mapeamento de esquema na atividade de cópia
Este artigo descreve como a atividade de cópia de Azure Data Factory funciona o mapeamento de esquema e o mapeamento do tipo de dados a partir dos dados de origem para sink de dados ao efetuar a cópia de dados.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja [Data Factory version 1 documentation](v1/data-factory-introduction.md) (Documentação da versão 1 do Data Factory).


## <a name="column-mapping"></a>Mapeamento de colunas

Por predefinição, a atividade de cópia **mapear dados de origem para sink por nomes de coluna**, a menos que [mapeamento de colunas explícita](#explicit-column-mapping) está configurado. Mais especificamente, a atividade de cópia:

1. Ler os dados de origem e determinar o esquema de origem

    * Para origens de dados com o esquema predefinida no dados arquivo/formato de ficheiro, por exemplo, as bases de dados/ficheiros com os metadados (Avro/ORC/Parquet/texto com o cabeçalho), o esquema de origem é extraída dos metadados de ficheiro ou o resultado da consulta.
    * Para origens de dados com o esquema flexível, por exemplo, o Azure tabela/Cosmos DB, esquema de origem é inferida do resultado da consulta. Pode substituí-lo, fornecendo a "estrutura" no conjunto de dados.
    * Para o ficheiro de texto sem cabeçalho, os nomes predefinidos das colunas são geradas com o padrão "Prop_0", "Prop_1",... Pode substituí-lo, fornecendo a "estrutura" no conjunto de dados.
    * Para a origem de Dynamics, tem de fornecer as informações de esquema na secção "estrutura" conjunto de dados.

2. Aplica o mapeamento de colunas explícita se for especificado.

3. Escrever os dados para sink

    * Para os arquivos de dados com o esquema predefinido, os dados são escritos para as colunas com o mesmo nome.
    * Para os arquivos de dados sem esquema fixo e formatos de ficheiro, os nomes de coluna/metadados será gerado com base no esquema de origem.

### <a name="explicit-column-mapping"></a>Mapeamento de colunas explícita

Pode especificar **columnMappings** no **typeProperties** secção a atividade de cópia para efetuar o mapeamento de colunas explícita. Neste cenário, a secção "estrutura" é necessária para conjuntos de dados de entrada e de saída. Suporta de mapeamento de coluna **subconjunto de colunas no conjunto de dados de origem "estrutura" para todas as colunas no conjunto de dados de sink "estrutura" ou um mapeamento de todas as**. Seguem-se as condições de erro que resultam numa exceção:

* Consulta de resultado tem um nome de coluna especificada na secção "estrutura" conjunto de dados de entrada de arquivo de dados de origem.
* Receptor de arquivo de dados (se com o esquema predefinido) não tem um nome de coluna que é especificado na secção de "estrutura" do conjunto de dados de saída.
* Menos colunas ou mais colunas na "estrutura de" do sink conjunto de dados ao especificado no mapeamento.
* Mapeamento duplicado.

#### <a name="explicit-column-mapping-example"></a>Exemplo de mapeamento de colunas explícita

Neste exemplo, a tabela de entrada tem uma estrutura e aponta para uma tabela numa base de dados SQL no local.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Neste exemplo, a tabela de saída tem uma estrutura e aponta para uma tabela numa base de dados SQL do Azure.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

O JSON seguinte define uma atividade de cópia num pipeline. As colunas a partir da origem mapeado para colunas no sink (**columnMappings**) utilizando o **tradutor** propriedade.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    }
}
```

**Fluxo de mapeamento de colunas:**

![Fluxo de mapeamento de coluna](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>Mapeamento de tipos de dados

Atividade de cópia realiza os tipos de origem para sink tipos mapeamento com a seguinte abordagem de passo 2:

1. Conversão de tipos de origens nativo em tipos de dados intermédio do Azure Data Factory
2. Converter o tipo de sink nativo do Azure Data Factory provisória tipos de dados

Pode localizar o mapeamento entre o tipo nativo para o tipo provisória na secção "Mapeamento do tipo de dados" no tópico cada conector.

### <a name="supported-data-types"></a>Tipos de dados suportados

Fábrica de dados suporta os seguintes tipos de dados intermédio: pode especificar abaixo valores quando fornecer informações de tipo no [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure) configuração:

* Byte]
* Valor booleano
* DateTime
* Datetimeoffset
* Decimal
* duplo
* GUID
* Int16
* Int32
* Int64
* Único
* Cadeia
* Timespan

### <a name="explicit-data-type-conversion"></a>A conversão do tipo de dados explícitos

Quando copiar dados para dados armazena com esquema fixo, por exemplo, SQL Server/Oracle, quando a origem e dependente tem o tipo de diferentes na mesma coluna, a conversão de tipos explícitas deverá ser declarada no lado de origem:

* Para a origem de ficheiro, por exemplo, CSV/Avro, a conversão do tipo deverá ser declarada através de estrutura de origem com a lista de colunas completo (lado coluna nome e o sink lado tipo de origem)
* Para a origem relacional (por exemplo, SQL Server/Oracle), a conversão de tipo deve ser possível através de conversão de tipos explícitas na instrução de consulta.

## <a name="when-to-specify-dataset-structure"></a>Quando especificar o conjunto de dados "estrutura"

Numa abaixo cenários, "estrutura" no conjunto de dados é necessária:

* Aplicar [conversão do tipo de dados explícitos](#explicit-data-type-conversion) para as origens de ficheiros durante a cópia (conjunto de dados de entrada)
* Aplicar [mapeamento de colunas explícita](#explicit-column-mapping) durante a cópia (ambos de entrada e saída do conjunto de dados)
* Copiar da origem de / CRM Dynamics 365 (conjunto de dados de entrada)
* Copiar a base de dados do Cosmos como objeto aninhado quando a origem não é ficheiros JSON (conjunto de dados de saída)

Na abaixo cenários, é sugerida "estrutura" no conjunto de dados:

* Copiar a partir do ficheiro de texto sem cabeçalho (conjunto de dados de entrada). Pode especificar os nomes de coluna para o ficheiro de texto alinhar com as colunas de receptores correspondente, para guardar a partir de mapeamento de colunas explícita.
* Copiar a partir dos dados armazena com o esquema flexível, por exemplo, base de dados do Azure-tabela/Cosmos (conjunto de dados de entrada), para garantir os dados esperados (colunas) que está a ser copiados em vez de copiar permitem atividade inferir o esquema com base nas linhas principais durante cada execução da atividade.


## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Copie a tolerância a falhas de atividade](copy-activity-fault-tolerance.md)
- [Desempenho de atividade de cópia](copy-activity-performance.md)
