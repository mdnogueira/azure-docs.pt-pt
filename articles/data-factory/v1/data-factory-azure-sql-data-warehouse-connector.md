---
title: Copiar dados da Azure SQL Data Warehouse | Microsoft Docs
description: Saiba como copiar dados para/a partir do Azure SQL Data Warehouse com o Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a56afa7c5200b53b398f8a99e8a36df3685b2f66
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Copiar dados de e para o Azure SQL Data Warehouse com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão 2 - Pré-visualização](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector do Azure SQL Data Warehouse no V2](../connector-azure-sql-data-warehouse.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados do Azure SQL Data Warehouse. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.  

> [!TIP]
> Para alcançar o melhor desempenho, utilize o PolyBase para carregar dados para o Azure SQL Data Warehouse. O [PolyBase de utilização para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) secção tenha detalhes. Para obter instruções com um caso de utilização, consulte [1 TB no Azure SQL Data Warehouse em 15 minutos de carga com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **do Azure SQL Data Warehouse** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **ao Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Quando copiar dados do SQL Server ou SQL Database do Azure para o Azure SQL Data Warehouse, se a tabela não existe no arquivo de destino, fábrica de dados pode criar automaticamente a tabela no SQL Data Warehouse, utilizando o esquema da tabela no arquivo de dados de origem. Consulte [automaticamente a criação da tabela](#auto-table-creation) para obter mais detalhes.

## <a name="supported-authentication-type"></a>Tipo de autenticação suportada
Azure SQL Data Warehouse conector suporte a autenticação básica.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de/para um Azure SQL Data Warehouse, utilizando ferramentas diferentes/APIs.

A forma mais fácil para criar um pipeline que copia dados do Azure SQL Data Warehouse é utilizar o Assistente de cópia de dados. Consulte [Tutorial: carregar dados para o SQL Data Warehouse com o Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar um **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines. 
2. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para um armazém de dados SQL do Azure, criar dois serviços ligados para ligar a sua conta de armazenamento do Azure e do armazém de dados SQL do Azure à fábrica de dados. As propriedades de serviço ligado que são específicas para o Azure SQL Data Warehouse, consulte [ligado propriedades do serviço](#linked-service-properties) secção. 
3. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de blob e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar a tabela no armazém de dados SQL do Azure que contém os dados copiados a partir do blob storage. As propriedades do conjunto de dados que são específicas para o Azure SQL Data Warehouse, consulte [propriedades do dataset](#dataset-properties) secção.
4. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. No exemplo mencionado anteriormente, utilizar BlobSource como uma origem e SqlDWSink como um sink para a atividade de cópia. Da mesma forma, se estiver a copiar do armazém de dados SQL do Azure para armazenamento de Blobs do Azure, utilizar SqlDWSource e BlobSink na atividade de cópia. Para as propriedades da atividade de cópia que são específicas para o Azure SQL Data Warehouse, consulte [copiar propriedades da atividade](#copy-activity-properties) secção. Para obter detalhes sobre como utilizar um arquivo de dados como uma origem ou de um receptor de mensagens em fila, clique na ligação na secção anterior para o arquivo de dados.

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados para/de um Azure SQL Data Warehouse, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) secção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para o Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço ligado do Azure SQL Data Warehouse.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **AzureSqlDW** |Sim |
| connectionString |Especificar as informações necessárias para ligar à instância do armazém de dados SQL do Azure para a propriedade connectionString. Apenas autenticação básica é suportada. |Sim |

> [!IMPORTANT]
> Configurar [Firewall de base de dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e no servidor de base de dados para [permitir que os serviços do Azure aceder ao servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se estiver a copiar dados para o Azure SQL Data Warehouse a partir de fora do Azure incluindo de origens de dados no local com o gateway de fábrica de dados, configure o intervalo de endereços IP adequado para a máquina que está a enviar dados para o Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

A secção de typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **AzureSqlDWTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na base de dados do Azure SQL Data Warehouse que referencia o serviço ligado. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia demora apenas uma entrada e produz saída de apenas um.

Enquanto, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

### <a name="sqldwsource"></a>SqlDWSource
Quando a origem é do tipo **SqlDWSource**, as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados a partir da tabela de origem. |Nome do procedimento armazenado. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |

Se o **sqlReaderQuery** está especificado para o SqlDWSource, a atividade de cópia executa esta consulta contra a origem do armazém de dados SQL do Azure para obter os dados.

Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para criar uma consulta para executar no Azure SQL Data Warehouse. Exemplo: `select column1, column2 from mytable`. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

#### <a name="sqldwsource-example"></a>Exemplo de SqlDWSource

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**A definição de procedimento armazenado:**

```SQL
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

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia para executar de forma a que os dados de um setor específico é limpa. Para obter mais informações, consulte [secção repetibilidade](#repeatability-during-copy). |Uma instrução de consulta. |Não |
| allowPolyBase |Indica se deve utilizar o PolyBase (quando aplicável) em vez de mecanismo BULKINSERT. <br/><br/> **Utilizando o PolyBase é a forma recomendada para carregar dados para o SQL Data Warehouse.** Consulte [PolyBase de utilização para carregar dados para o Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) restrições e os detalhes na secção. |Verdadeiro <br/>FALSE (predefinição) |Não |
| polyBaseSettings |Um grupo de propriedades que podem ser especificados quando o **allowPolybase** propriedade está definida como **verdadeiro**. |&nbsp; |Não |
| rejectValue |Especifica o número ou a percentagem de linhas que pode ser rejeitada antes da consulta falha. <br/><br/>Saiba mais sobre as opções de rejeitar o PolyBase no **argumentos** secção [criar tabela externa (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tópico. |0 (predefinição), 1, 2,... |Não |
| rejectType |Especifica se a opção de rejectValue é especificada como um valor literal ou uma percentagem. |Valor de percentagem (predefinição) |Não |
| rejectSampleValue |Determina o número de linhas a obter antes do PolyBase recalcula a percentagem de linhas rejeitadas. |1, 2, … |Sim, se **rejectType** é **percentagem** |
| useTypeDefault |Especifica como processar os valores em falta nos ficheiros de texto delimitado quando PolyBase obtém dados a partir do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade da secção de argumentos no [criar formato de ficheiro externo (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |TRUE, False (predefinição) |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite. |TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

#### <a name="sqldwsink-example"></a>Exemplo de SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Utilize o PolyBase para carregar dados para o Azure SQL Data Warehouse
Utilizar  **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)**  é uma forma eficaz de carregar grande quantidade de dados no Azure SQL Data Warehouse com débito elevado. Pode ver um grandes ganhos de débito da utilizando o PolyBase em vez do mecanismo BULKINSERT predefinido. Consulte [copiar o número de referência de desempenho](data-factory-copy-activity-performance.md#performance-reference) com comparação detalhada. Para obter instruções com um caso de utilização, consulte [1 TB no Azure SQL Data Warehouse em 15 minutos de carga com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Se os dados de origem estão a ser **Blob do Azure ou do Azure Data Lake Store**e o formato é compatível com o PolyBase, pode copiar diretamente ao Azure SQL Data Warehouse, utilizando o PolyBase. Consulte  **[cópia direta, utilizando o PolyBase](#direct-copy-using-polybase)**  com detalhes.
* Se o arquivo de dados de origem e o formato não é suportado originalmente pelo PolyBase, pode utilizar o  **[cópia testado utilizando o PolyBase](#staged-copy-using-polybase)**  em vez disso, a funcionalidade. Também fornece a melhor débito automaticamente ao converter os dados em formato compatível com o PolyBase e armazenar os dados no Blob storage do Azure. Em seguida, carrega dados para o SQL Data Warehouse.

Definir o `allowPolyBase` propriedade **verdadeiro** conforme mostrado no exemplo seguinte para o Azure Data Factory para utilizar o PolyBase para copiar dados para o Azure SQL Data Warehouse. Quando definir allowPolyBase como true, pode especificar propriedades específicas do PolyBase utilizando o `polyBaseSettings` grupo de propriedades. consulte o [SqlDWSink](#SqlDWSink) secção para obter detalhes sobre as propriedades que podem ser utilizados com polyBaseSettings.

```JSON
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

### <a name="direct-copy-using-polybase"></a>Cópia direta, utilizando o PolyBase
O PolyBase do armazém de dados SQL diretamente suporta Blob do Azure e o Azure Data Lake Store (utilizando o principal de serviço) como origem e com os requisitos de formato de ficheiro específico. Se os dados de origem satisfaz os critérios descritos nesta secção, pode copiar diretamente do arquivo de dados de origem para o Azure SQL Data Warehouse, utilizando o PolyBase. Caso contrário, pode utilizar [cópia testado utilizando o PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Para copiar dados de Data Lake Store ao SQL Data Warehouse forma eficiente, mais de [do Azure Data Factory torna mais fácil e conveniente desvendar informações a partir dos dados ao utilizar o Data Lake Store com o SQL Data Warehouse mesmo](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se não são cumpridos os requisitos, o Azure Data Factory verifica as definições e automaticamente retrocede para o mecanismo BULKINSERT para o movimento de dados.

1. **Origem de serviço ligado** é do tipo: **AzureStorage** ou **AzureDataLakeStore com a autenticação principal de serviço**.  
2. O **conjunto de dados de entrada** é do tipo: **AzureBlob** ou **AzureDataLakeStore**e o formato de tipo em `type` propriedades **OrcFormat**, ou **TextFormat** com as seguintes configurações:

   1. `rowDelimiter`tem de ser  **\n** .
   2. `nullValue`está definido como **uma cadeia vazia** (""), ou `treatEmptyAsNull` está definido como **verdadeiro**.
   3. `encodingName`está definido como **utf-8**, que é **predefinido** valor.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, e `skipLineCount` não foram especificados.
   5. `compression`pode ser **sem compressão**, **GZip**, ou **Deflate**.

    ```JSON
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

### <a name="staged-copy-using-polybase"></a>Cópia testada utilizando o PolyBase
Quando os dados de origem não cumprem os critérios introduzidos na secção anterior, pode ativar a cópia de dados através de um provisória transição Blob Storage do Azure (não pode ser armazenamento Premium). Neste caso, do Azure Data Factory efetua automaticamente transformações nos dados para satisfazer os requisitos de formato de dados do PolyBase, em seguida, utilize o PolyBase para carregar dados para o SQL Data Warehouse e em Limpar último os dados temporários o armazenamento de Blobs. Consulte [testado cópia](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre como copiar dados através de um Blob do Azure transição funciona em geral.

> [!NOTE]
> Quando o arquivo de dados de cópia de um local de dados no Azure SQL Data Warehouse, utilizando o PolyBase e JRE (ambiente de tempo de execução Java) de teste, se a sua versão do Data Management Gateway for inferior 2.4, é necessário no seu computador de gateway que é utilizado para transformar os dados de origem para o formato correto. Sugerimos a que atualizar o gateway para a versão mais recente para evitar esse dependência.
>

Para utilizar esta funcionalidade, crie um [serviço ligado do Storage do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) que refere-se a conta de armazenamento do Azure com o armazenamento de BLOBs intermédio, em seguida, especifique o `enableStaging` e `stagingSettings` propriedades para a atividade de cópia, conforme mostrado no seguinte código:

```json
"activities":[  
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
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
As secções seguintes fornecem procedimentos adicionais para aqueles que são mencionados nos [melhores práticas para o Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permissão de base de dados necessária
Para utilizar o PolyBase, requer o utilizador que está a ser utilizado para carregar dados para o armazém de dados do SQL Server tem o [permissão "Controlo"](https://msdn.microsoft.com/library/ms191291.aspx) na base de dados de destino. Uma forma de alcançar que consiste em adicionar esse utilizador como um membro da função "db_owner". Saber como fazê-lo ao seguir [nesta secção](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Limitação de tipo de dados e de tamanho de linha
Cargas de Polybase estão limitadas a carregar as linhas ambos inferior **1 MB** e não é possível carregar VARCHR(MAX), nvarchar (Max) ou varbinary (Max). Consulte [aqui](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Se tiver dados de origem com linhas de tamanho superior a 1 MB, poderá pretender dividir as tabelas de origem verticalmente em várias relações de pequenas onde o maior tamanho de linha de cada um deles não pode exceder o limite. As tabelas de menores, em seguida, podem ser carregadas com o PolyBase e intercaladas em conjunto no Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse
Para obter uma melhor débito possíveis, considere atribuir maior classe de recursos para o utilizador que está a ser utilizado para carregar dados para o SQL Data Warehouse através do PolyBase. Saber como fazê-lo ao seguir [alterar um exemplo de classe de recursos de utilizador](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

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

## <a name="auto-table-creation"></a>Criação automática de tabela
Se estiver a utilizar o Assistente para copiar para copiar dados do SQL Server ou SQL Database do Azure para o Azure SQL Data Warehouse e a tabela que corresponde à tabela de origem não existe no arquivo de destino, fábrica de dados pode criar automaticamente a tabela no armazém de dados, utilizando o esquema de tabela de origem.

Fábrica de dados cria a tabela no arquivo de destino com o mesmo nome de tabela no arquivo de dados de origem. Os tipos de dados para as colunas são escolhidos com base no mapeamento do tipo seguinte. Se for necessário, realiza conversões de tipo para corrigir quaisquer incompatibilidades entre os arquivos de origem e destino. Também utiliza o Round Robin distribuição de tabela.

| Tipo de coluna de base de dados do SQL Server de origem | Tipo de coluna de armazém de dados do SQL Server de destino (limitação de tamanho) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| bits | bits |
| Decimal | Decimal |
| um valor numérico | Decimal |
| Número de vírgula flutuante | Número de vírgula flutuante |
| dinheiro | dinheiro |
| Real | Real |
| Em SmallMoney | Em SmallMoney |
| Binário | Binário |
| varbinary | Varbinary (até 8000) |
| Data | Data |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Hora | Hora |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Texto | Varchar (até 8000) |
| NText | NVarChar (até 4000) |
| Imagem | VarBinary (até 8000) |
| UniqueIdentifier | UniqueIdentifier |
| char | char |
| NChar | NChar |
| VarChar | VarChar (até 8000) |
| NVarChar | NVarChar (até 4000) |
| XML | Varchar (até 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo para o Azure SQL Data Warehouse
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia realiza conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de passo 2:

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Ao mover dados para & do Azure SQL Data Warehouse, são utilizados os seguintes mapeamentos do tipo SQL para o tipo de .NET e vice-versa.

O mapeamento de é igual a [mapeamento do tipo de dados do SQL Server para ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo de motor de base de dados do SQL Server | Tipo de .NET framework |
| --- | --- |
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

Também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados dependente na definição da atividade de cópia. Para obter mais informações, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Exemplos JSON para copiar dados para e do armazém de dados do SQL Server
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Estes mostram como copiar dados de e para o Azure SQL Data Warehouse e armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Exemplo: Copiar dados do armazém de dados SQL do Azure para BLOBs do Azure
O exemplo define as entidades da fábrica de dados seguintes:

1. Um serviço ligado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureSqlDWTable](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [SqlDWSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de séries de tempo (horárias, diários, etc.) de uma tabela na base de dados do armazém de dados SQL do Azure para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado do Azure SQL Data Warehouse:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço ligado do armazenamento de Blobs do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do armazém de dados SQL do Azure:**

O exemplo assume que criou uma tabela "MyTable" Azure SQL Data Warehouse e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo.

A definição "external": "true" informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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
**Conjunto de dados de saída do Blob do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Atividade de cópia de um pipeline com SqlDWSource e BlobSink:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlDWSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **SqlReaderQuery** propriedade seleciona os dados na última hora para copiar.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```
> [!NOTE]
> No exemplo, **sqlReaderQuery** especificado para o SqlDWSource. A atividade de cópia executa esta consulta contra a origem do armazém de dados SQL do Azure para obter os dados.
>
> Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).
>
> Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para criar uma consulta (selecione column1, column2 de mytable) para executar no Azure SQL Data Warehouse. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Exemplo: Copiar dados de Blobs do Azure ao Azure SQL Data Warehouse
O exemplo define as entidades da fábrica de dados seguintes:

1. Um serviço ligado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureSqlDWTable](#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlDWSink](#copy-activity-properties).

As cópias de exemplo séries de tempo blob de dados (hora a hora, diariamente, etc.) do Azure para uma tabela no Azure SQL Data Warehouse da base de dados a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

**Serviço ligado do Azure SQL Data Warehouse:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço ligado do armazenamento de Blobs do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada Blob do Azure:**

Dados são captados um blob de novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome de ficheiro utiliza a parte de hora a hora de início. "external": "true" definição informa o serviço fábrica de dados que esta tabela é externa à fábrica de dados e não é produzida por uma atividade no factory de dados.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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
**Conjunto de dados de saída do armazém de dados SQL do Azure:**

O exemplo copia dados para uma tabela com o nome "MyTable" no Azure SQL Data Warehouse. Crie a tabela no Azure SQL Data Warehouse com o mesmo número de colunas como espera que o ficheiro CSV de Blob que contém. Novas linhas são adicionadas à tabela de cada hora.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Atividade de cópia de um pipeline com BlobSource e SqlDWSink:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlDWSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
Para obter instruções, consulte a [1 TB no Azure SQL Data Warehouse em 15 minutos de carga com o Azure Data Factory](data-factory-load-sql-data-warehouse.md) e [carregar dados com o Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) artigo na documentação do Azure SQL Data Warehouse.

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
