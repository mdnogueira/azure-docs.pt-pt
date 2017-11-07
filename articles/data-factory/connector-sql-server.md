---
title: Copiar dados da SQL Server utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba mais sobre como mover dados para/a partir da base de dados de SQL Server que está no local ou numa VM do Azure utilizando o Azure Data Factory."
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
ms.date: 09/30/2017
ms.author: jingwang
ms.openlocfilehash: 5d414c8c0c9825aae245d59abc23dfc0d4027862
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Copiar dados para e do SQL Server utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-sqlserver-connector.md)
> * [Versão 2 - Pré-visualização](connector-sql-server.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de origem e de uma base de dados do SQL Server. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector do SQL Server no V1](v1/data-factory-sqlserver-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de/para a base de dados do SQL Server para qualquer arquivo de dados suportados sink ou copiar dados a partir de qualquer arquivo de dados de origem suportada para a base de dados do SQL Server. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector do SQL Server suporta:

- SQL Server 2016, 2014, 2012, 2008 R2, 2008 e 2005 de versão
- Copiar dados utilizando **SQL** ou **Windows** autenticação.
- Como origem, a obtenção de dados através de consulta de SQL Server ou o procedimento armazenado.
- Como sink, acrescentar dados para a tabela de destino ou invocar um procedimento armazenado com lógica personalizada durante a cópia.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar dados de cópia da base de dados do SQL Server que não está acessível publicamente, terá de configurar um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes. O tempo de execução de integração fornece um controlador de base de dados incorporado do SQL Server, por conseguinte, não precisa de instalar manualmente a quaisquer controladores ao copiar dados de/para a base de dados do SQL Server.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector de base de dados do SQL Server.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

São suportadas as seguintes propriedades do serviço ligado do SQL Server:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **SqlServer** | Sim |
| connectionString |Especificar as informações de connectionString necessárias para ligar à base de dados do SQL Server utilizando a autenticação SQL ou autenticação do Windows. Marcar este campo como um SecureString. |Sim |
| Nome de utilizador |Especifique o nome de utilizador se estiver a utilizar a autenticação do Windows. Exemplo: **domainname\\username**. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. Marcar este campo como um SecureString. |Não |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração Self-hosted ou Runtime de integração do Azure (se o arquivo de dados acessível publicamente). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo 1: utilizar a autenticação do SQL Server**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo 2: através da autenticação Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
             "userName": "<domain\\username>",
             "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados do SQL Server.

Para copiar dados de/para a base de dados do SQL Server, defina a propriedade de tipo do conjunto de dados para **SqlServerTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **SqlServerTable** | Sim |
| tableName |Nome da tabela ou vista na instância de base de dados do SQL Server que o serviço ligado refere-se. | Sim |

**Exemplo:**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pelo SQL Server origem e dependente.

### <a name="sql-server-as-source"></a>SQL Server como origem

Para copiar dados do SQL Server, defina o tipo de origem na atividade de cópia para **SqlSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **SqlSource** | Sim |
| sqlReaderQuery |Utilize a consulta SQL personalizada para ler os dados. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados a partir da tabela de origem. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Valores permitidos são: pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |

**Pontos a ter em atenção:**

- Se o **sqlReaderQuery** está especificado para o SqlSource, a atividade de cópia executa esta consulta contra a origem de SQL Server para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).
- Se não especificar "sqlReaderQuery" ou "sqlReaderStoredProcedureName", as colunas definidas na secção "estrutura" do conjunto de dados JSON são utilizadas para construir uma consulta (`select column1, column2 from mytable`) para executar o SQL Server. Se a definição do conjunto de dados não tiver a estrutura"", todas as colunas são selecionadas da tabela.
- Quando utiliza **sqlReaderStoredProcedureName**, terá de especificar um fictício **tableName** propriedade no conjunto de dados JSON.

**Exemplo: utilizar a consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
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
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
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

### <a name="sql-server-as-sink"></a>SQL Server como sink

Para copiar dados para o SQL Server, defina o tipo de sink na atividade de cópia para **SqlSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **SqlSink** | Sim |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize.<br/>Valores permitidos são: número inteiro (número de linhas). |Não (predefinição: 10000) |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite.<br/>Valores permitidos são: timespan. Exemplo: "00: 30:00" (30 minutos). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado dados upserts (inserções/atualizações) para a tabela de destino. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado.<br/>Valores permitidos são: pares nome/valor. Nomes e maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e a maiúsculas e minúsculas dos parâmetros de procedimento armazenado. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia faz com que os dados a ser movidos disponível numa tabela temporária com este tipo de tabela. Código do procedimento armazenado, em seguida, pode intercalar os dados que está a ser copiados com dados existentes. |Não |
| preCopyScript |Especifique uma consulta de SQL Server ser executado antes de escrever dados no servidor SQL em cada execução de atividade de cópia. Pode utilizar esta propriedade para limpar os dados previamente carregados. |Não |

> [!TIP]
> Quando copiar dados para o SQL Server, a atividade de cópia acrescenta dados para a tabela do sink por predefinição. Para executar um UPSERT ou lógica de negócio adicionais, utilize o procedimento armazenado no SqlSink. Saiba mais detalhes de [invocar o procedimento armazenado para Sink do SQL Server](#invoking-stored-procedure-for-sql-sink).

**Exemplo 1: acrescentar dados**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exemplo 2: a invocar um procedimento armazenado durante a cópia para upsert**

Saiba mais detalhes de [invocar o procedimento armazenado para Sink do SQL Server](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade na base de dados de destino

Esta secção fornece um exemplo que copia dados a partir de uma tabela de origem com nenhuma coluna de identidade para uma tabela de destino com uma coluna de identidade.

**Tabela de origem:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Tabela de destino:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Tenha em atenção que a tabela de destino tem uma coluna de identidade.

**Definição de JSON do conjunto de dados de origem**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Definição de JSON do conjunto de dados de destino**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Tenha em atenção que, como a tabela de origem e destino têm esquemas diferentes (o destino tem uma coluna adicional com a identidade). Neste cenário, tem de especificar **estrutura** propriedade na definição de conjunto de dados de destino, que não inclui a coluna de identidade.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocar um procedimento armazenado do sink do SQL Server

Quando copiar dados na base de dados do SQL Server, um utilizador especificado pode ser configurado e invocado com parâmetros adicionais procedimento armazenado.

Um procedimento armazenado pode ser utilizado quando mecanismos incorporados de cópia não servem o objetivo. É normalmente utilizado quando upsert (insert + update) ou processamento adicional (intercalação colunas, procurar valores adicionais, inserir na várias tabelas, etc.) tiver de ser efetuada antes da final inserção de dados de origem na tabela de destino.

O exemplo seguinte mostra como utilizar um procedimento armazenado para efetuar um upsert numa tabela na base de dados do SQL Server. Partindo do princípio de dados de entrada e a tabela do sink "Marketing" tem três colunas: ProfileID, estado e categoria. Efetuar upsert com base na coluna "ProfileID" e aplicam-se apenas para uma categoria específica.

**Conjunto de dados de saída**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Defina a secção de SqlSink na atividade de cópia da seguinte forma.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

A base de dados, defina o procedimento armazenado com o mesmo nome como SqlWriterStoredProcedureName. Processa a entrada de dados de origem especificada e intercalação na tabela de saída. Tenha em atenção que o nome do parâmetro do procedimento armazenado deve ser igual a "tableName" definida no conjunto de dados.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

A base de dados, defina o tipo de tabela com o mesmo nome como sqlWriterTableType. Tenha em atenção que o esquema do tipo de tabela deve ser igual ao esquema devolvido pelos seus dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

A funcionalidade de procedimento armazenado tira partido da [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-sql-server"></a>Tipo de dados de mapeamento para o SQL server

Quando copiar dados de/para SQL Server, os seguintes mapeamentos são utilizados de tipos de dados do SQL Server para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do SQL Server | Tipo de dados intermédio de fábrica de dados |
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

## <a name="troubleshooting-connection-issues"></a>Resolução de problemas de ligação

1. Configure o servidor de SQL para aceitar ligações remotas. Iniciar **SQL Server Management Studio**, faça duplo clique **servidor**e clique em **propriedades**. Selecione **ligações** na lista e verificação **permitir ligações remotas ao servidor**.

    ![Ativar ligações remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Consulte [configurar a opção de configuração do servidor de acesso remoto](https://msdn.microsoft.com/library/ms191464.aspx) para obter passos detalhados.

2. Iniciar **Gestor de configuração do SQL Server**. Expanda **configuração de rede do SQL Server** para a instância que pretende e selecione **protocolos para MSSQLSERVER**. Deverá ver protocolos no painel direito. Ative TCP/IP clicando **TCP/IP** e clicando em **ativar**.

    ![Ative TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Consulte [ativar ou desativar um protocolo de rede do servidor](https://msdn.microsoft.com/library/ms191294.aspx) para obter detalhes e alternativas formas de ativar o protocolo TCP/IP.

3. Na janela do mesma, faça duplo clique em **TCP/IP** iniciar **propriedades de TCP/IP** janela.
4. Mudar para o **endereços IP** separador. Desloque para baixo para ver **IPAll** secção. Tome nota o * * a porta TCP * * (predefinição é **1433**).
5. Criar um **regra da Firewall do Windows** no computador para permitir tráfego de entrada através desta porta.  
6. **Verificar ligação**: para ligar ao SQL Server com o nome completamente qualificado, utilize o SQL Server Management Studio de um computador diferente. Por exemplo: `"<machine>.<domain>.corp.<company>.com,1433"`.


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).