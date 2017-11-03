---
title: "Copiar dados da fábrica de dados a utilizar o Azure SQL Data Warehouse | Microsoft Docs"
description: "Saiba como copiar dados de arquivos de origem suportada para o Azure SQL Data Warehouse (ou) do armazém de dados do SQL Server para lojas de receptores suportadas utilizando o Data Factory."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: fb67c70d1e85307c38a185e2b47729880880d55b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiar os dados de ou para o Azure SQL Data Warehouse, utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão 2 - Pré-visualização](connector-azure-sql-data-warehouse.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados e de um Azure SQL Data Warehouse. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector do Azure SQL Data Warehouse no V1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados de/para o Azure SQL Data Warehouse para qualquer arquivo de dados suportados sink ou copiar dados a partir de qualquer arquivo de dados de origem suportada para o Azure SQL Data Warehouse. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de armazém de dados SQL do Azure suporta:

- Cópia de dados utilizando a autenticação SQL.
- Como origem, a obtenção de dados através de consulta de SQL Server ou o procedimento armazenado.
- Como sink, carregar dados utilizando **PolyBase** ou inserção em massa. O anterior é **recomendado** para melhorar o desempenho de cópia.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector do Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Azure SQL Data Warehouse:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **AzureSqlDW** | Sim |
| connectionString |Especificar as informações necessárias para ligar à instância do armazém de dados SQL do Azure para a propriedade connectionString. Apenas autenticação básica é suportada. Marcar este campo como um SecureString. |Sim |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o tempo de execução do Self-hosted integração (se o arquivo de dados esteja localizado numa rede privada). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |


> [!IMPORTANT]
> Configurar [Firewall de armazém de dados de SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e no servidor de base de dados para [permitir que os serviços do Azure aceder ao servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se estiver a copiar dados para o Azure SQL Data Warehouse a partir de fora do Azure incluindo de origens de dados no local com o tempo de execução do Self-hosted integração, configurar o intervalo de endereços IP adequado para a máquina que está a enviar dados para dados de SQL do Azure Armazém.

**Exemplo:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados do Azure SQL Data Warehouse.

Para copiar dados de/para o Azure SQL Data Warehouse, defina a propriedade de tipo do conjunto de dados para **AzureSqlDWTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **AzureSqlDWTable** | Sim |
| tableName |Nome da tabela ou vista na instância do Azure SQL Data Warehouse pelo serviço ligado refere-se. | Sim |

**Exemplo:**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pelo Azure SQL Data Warehouse origem e dependente.

### <a name="azure-sql-data-warehouse-as-source"></a>O Azure SQL Data Warehouse como origem

Para copiar dados do Azure SQL Data Warehouse, defina o tipo de origem na atividade de cópia para **SqlDWSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **SqlDWSource** | Sim |
| sqlReaderQuery |Utilize a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados a partir da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Valores permitidos são: pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |

**Pontos a ter em atenção:**

- Se o **sqlReaderQuery** está especificado para o SqlSource, a atividade de cópia executa esta consulta contra a origem do armazém de dados SQL do Azure para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).
- Se não especificar "sqlReaderQuery" ou "sqlReaderStoredProcedureName", as colunas definidas na secção "estrutura" do conjunto de dados JSON são utilizadas para construir uma consulta (`select column1, column2 from mytable`) para executar no Azure SQL Data Warehouse. Se a definição do conjunto de dados não tiver a estrutura"", todas as colunas são selecionadas da tabela.
- Quando utiliza **sqlReaderStoredProcedureName**, terá de especificar um fictício **tableName** propriedade no conjunto de dados JSON.

**Exemplo: utilizar a consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exemplo: utilizar o procedimento armazenado**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**A definição de procedimento armazenado:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>O Azure SQL Data Warehouse como sink

Para copiar dados para o Azure SQL Data Warehouse, defina o tipo de sink na atividade de cópia para **SqlDWSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **SqlDWSink** | Sim |
| allowPolyBase |Indica se deve utilizar o PolyBase (quando aplicável) em vez de mecanismo BULKINSERT. <br/><br/> **Utilizando o PolyBase é a forma recomendada para carregar dados para o SQL Data Warehouse.** Consulte [PolyBase de utilização para carregar dados para o Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) restrições e os detalhes na secção.<br/><br/>Valores permitidos são: **verdadeiro** (predefinição), e **falso**.  |Não |
| polyBaseSettings |Um grupo de propriedades que podem ser especificados quando o **allowPolybase** propriedade está definida como **verdadeiro**. |Não |
| rejectValue |Especifica o número ou a percentagem de linhas que pode ser rejeitada antes da consulta falha.<br/><br/>Saiba mais sobre as opções de rejeitar o PolyBase no **argumentos** secção [criar tabela externa (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tópico. <br/><br/>Valores permitidos são: 0 (predefinição), 1, 2,... |Não |
| rejectType |Especifica se a opção de rejectValue é especificada como um valor literal ou uma percentagem.<br/><br/>Valores permitidos são: **valor** (predefinição), e **percentagem**. |Não |
| rejectSampleValue |Determina o número de linhas a obter antes do PolyBase recalcula a percentagem de linhas rejeitadas.<br/><br/>Valores permitidos são: 1, 2,... |Sim, se **rejectType** é **percentagem** |
| useTypeDefault |Especifica como processar os valores em falta nos ficheiros de texto delimitado quando PolyBase obtém dados a partir do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade da secção de argumentos no [criar formato de ficheiro externo (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Valores permitidos são: **verdadeiro**, **falso** (predefinição). |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize. Aplica-se apenas quando não for utilizado o PolyBase.<br/><br/>Valores permitidos são: número inteiro (número de linhas). |Não (a predefinição é 10000) |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite. Aplica-se apenas quando não for utilizado o PolyBase.<br/><br/>Valores permitidos são: timespan. Exemplo: "00: 30:00" (30 minutos). |Não |
| preCopyScript |Especifique uma consulta de SQL Server ser executado antes de escrever dados no Azure SQL Data Warehouse em cada execução de atividade de cópia. Pode utilizar esta propriedade para limpar os dados previamente carregados. |Não |(#repeatability durante-cópia). |Uma instrução de consulta. |Não |

**Exemplo:**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

Saiba mais sobre como utilizar o PolyBase para carregar para o SQL Data Warehouse eficiente da secção seguinte.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Utilize o PolyBase para carregar dados para o Azure SQL Data Warehouse

Utilizar  **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)**  é uma forma eficaz de carregar grande quantidade de dados no Azure SQL Data Warehouse com débito elevado. Pode ver um grandes ganhos de débito da utilizando o PolyBase em vez do mecanismo BULKINSERT predefinido. Consulte [copiar o número de referência de desempenho](copy-activity-performance.md#performance-reference) com comparação detalhada. Para obter instruções com um caso de utilização, consulte [1 TB no Azure SQL Data Warehouse em 15 minutos de carga com o Azure Data Factory](connector-azure-sql-data-warehouse.md).

* Se os dados de origem estão a ser **Blob do Azure ou do Azure Data Lake Store**e o formato é compatível com o PolyBase, pode copiar diretamente ao Azure SQL Data Warehouse, utilizando o PolyBase. Consulte  **[cópia direta, utilizando o PolyBase](#direct-copy-using-polybase)**  com detalhes.
* Se o arquivo de dados de origem e o formato não é suportado originalmente pelo PolyBase, pode utilizar o  **[cópia testado utilizando o PolyBase](#staged-copy-using-polybase)**  em vez disso, a funcionalidade. Também fornece a melhor débito automaticamente ao converter os dados em formato compatível com o PolyBase e armazenar os dados no Blob storage do Azure. Em seguida, carrega dados para o SQL Data Warehouse.

### <a name="direct-copy-using-polybase"></a>Cópia direta, utilizando o PolyBase

O PolyBase do armazém de dados SQL diretamente suporta Blob do Azure e o Azure Data Lake Store (utilizando o principal de serviço) como origem e com os requisitos de formato de ficheiro específico. Se os dados de origem satisfaz os critérios descritos nesta secção, pode copiar diretamente do arquivo de dados de origem para o Azure SQL Data Warehouse, utilizando o PolyBase. Caso contrário, pode utilizar [cópia testado utilizando o PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Para copiar dados de Data Lake Store ao SQL Data Warehouse forma eficiente, mais de [do Azure Data Factory torna mais fácil e conveniente desvendar informações a partir dos dados ao utilizar o Data Lake Store com o SQL Data Warehouse mesmo](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se não são cumpridos os requisitos, o Azure Data Factory verifica as definições e automaticamente retrocede para o mecanismo BULKINSERT para o movimento de dados.

1. **Origem de serviço ligado** é do tipo: **AzureStorage** ou **AzureDataLakeStore**.
2. O **conjunto de dados de entrada** é do tipo: **AzureBlob** ou **AzureDataLakeStoreFile**e o formato de tipo em `type` propriedades **OrcFormat** , ou **TextFormat** com as seguintes configurações:

   1. `rowDelimiter`tem de ser  **\n** .
   2. `nullValue`está definido como **uma cadeia vazia** (""), ou `treatEmptyAsNull` está definido como **verdadeiro**.
   3. `encodingName`está definido como **utf-8**, que é **predefinido** valor.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, e `skipLineCount` não foram especificados.
   5. `compression`pode ser **sem compressão**, **GZip**, ou **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. Não existe nenhum `skipHeaderLineCount` em **BlobSource** ou **AzureDataLakeStore** para a atividade de cópia no pipeline.
4. Não existe nenhum `sliceIdentifierColumnName` em **SqlDWSink** para a atividade de cópia no pipeline. (PolyBase garante que todos os dados são atualizados ou nada é atualizado numa execução única. Para alcançar **repetibilidade**, pode utilizar `sqlWriterCleanupScript`).
5. Não existe nenhum `columnMapping` a ser utilizado na cópia associada na atividade.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>Cópia testada utilizando o PolyBase

Quando os dados de origem não cumprem os critérios introduzidos na secção anterior, pode ativar a cópia de dados através de um provisória transição Blob Storage do Azure (não pode ser armazenamento Premium). Neste caso, o Azure Data Factory efetua automaticamente a transformações nos dados para satisfazer os requisitos de formato de dados do PolyBase, em seguida, utilize o PolyBase para carregar dados para o SQL Data Warehouse e, em seguida, limpe os dados temporários o armazenamento de Blobs. Consulte [testado cópia](copy-activity-performance.md#staged-copy) para obter detalhes sobre como copiar dados através de um Blob do Azure transição funciona em geral.

Para utilizar esta funcionalidade, crie um [serviço ligado do Storage do Azure](connector-azure-blob-storage.md#linked-service-properties) que refere-se a conta de armazenamento do Azure com o armazenamento de BLOBs intermédio, em seguida, especifique o `enableStaging` e `stagingSettings` propriedades para a atividade de cópia, conforme mostrado no seguinte código:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob"
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>Melhores práticas quando utilizar o PolyBase

As secções seguintes fornecem procedimentos adicionais para aqueles que são mencionados nos [melhores práticas para o Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permissão de base de dados necessária

Para utilizar o PolyBase, requer o utilizador que está a ser utilizado para carregar dados para o armazém de dados do SQL Server tem o [permissão "Controlo"](https://msdn.microsoft.com/library/ms191291.aspx) na base de dados de destino. Uma forma de alcançar que consiste em adicionar esse utilizador como um membro da função "db_owner". Saber como fazê-lo ao seguir [nesta secção](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Limitação de tipo de dados e de tamanho de linha

Cargas de Polybase estão limitadas a carregar as linhas ambos inferior **1 MB** e não é possível carregar VARCHR(MAX), nvarchar (Max) ou varbinary (Max). Consulte [aqui](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Se tiver dados de origem com linhas de tamanho superior a 1 MB, poderá pretender dividir as tabelas de origem verticalmente em várias relações de pequenas onde o maior tamanho de linha de cada um deles não pode exceder o limite. As tabelas de menores, em seguida, podem ser carregadas com o PolyBase e intercaladas em conjunto no Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse

Para obter uma melhor débito possíveis, considere atribuir maior classe de recursos para o utilizador que está a ser utilizado para carregar dados para o SQL Data Warehouse através do PolyBase. Saber como fazê-lo ao seguir [alterar um exemplo de classe de recursos de utilizador](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName no Azure SQL Data Warehouse

A tabela seguinte fornece exemplos sobre como especificar o **tableName** propriedade no conjunto de dados JSON para várias combinações de nome de esquema e de tabela.

| Esquema da BD | Nome da tabela | propriedade JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable ou dbo. MyTable ou [dbo]. [MyTable] |
| dbo1 |MyTable |dbo1. MyTable ou [dbo1]. [MyTable] |
| dbo |My.Table |[My.Table] ou [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Se vir o seguinte erro, isto pode dever um problema com o valor especificado para a propriedade tableName. Consulte a tabela para a forma correta especificar valores para a propriedade JSON tableName.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colunas com valores predefinidos

Atualmente, a funcionalidade PolyBase na fábrica de dados só aceita o mesmo número de colunas como tabela de destino. Imaginemos que tem uma tabela com quatro colunas e um deles está definido com um valor predefinido. Os dados de entrada ainda devem conter quatro colunas. Fornecer um conjunto de dados de entrada de 3-coluna seria produzir um erro semelhante a mensagem seguinte:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Valor nulo é um formulário especial de valor predefinido. Se a coluna pode ser nula, os dados de entrada (no blob) para essa coluna pode estar vazios (não pode estar em falta o conjunto de dados de entrada). O PolyBase insere um valor nulo para os mesmos no armazém de dados SQL do Azure.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo de dados para o Azure SQL Data Warehouse

Quando copiar dados de/para o Azure SQL Data Warehouse, os seguintes mapeamentos são utilizados Azure SQL Data Warehouse tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do armazém de dados SQL do Azure | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| bigint |Int64 |
| Binário |Byte] |
| bits |Valor booleano |
| char |Cadeia, Char [] |
| Data |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte] |
| Número de vírgula flutuante |duplo |
| Imagem |Byte] |
| Int |Int32 |
| dinheiro |Decimal |
| nchar |Cadeia, Char [] |
| ntext |Cadeia, Char [] |
| um valor numérico |Decimal |
| nvarchar |Cadeia, Char [] |
| real |Único |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| em smallmoney |Decimal |
| sql_variant |Objeto * |
| Texto |Cadeia, Char [] |
| hora |TimeSpan |
| carimbo de data/hora |Byte] |
| tinyint |Bytes |
| uniqueidentifier |GUID |
| varbinary |Byte] |
| varchar |Cadeia, Char [] |
| xml |XML |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).