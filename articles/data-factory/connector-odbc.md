---
title: "Copiar dados a partir de origens ODBC, através do Azure Data Factory | Microsoft Docs"
description: "Saiba como copiar dados a partir de origens de OData aos arquivos de dados suportados sink utilizando uma atividade de cópia num pipeline do Azure Data Factory."
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
ms.date: 10/19/2017
ms.author: jingwang
ms.openlocfilehash: 37957c75fbe18734761ed8b33a27fe566018f4d2
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copiar dados de origem e de arquivos de dados ODBC utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-odbc-connector.md)
> * [Versão 2 - Pré-visualização](connector-odbc.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de origem e de um arquivo de dados ODBC. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector ODBC no V1](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de origem ODBC para qualquer arquivo de dados suportados sink ou copiar a partir de qualquer arquivo de dados de origem suportada para sink ODBC. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector ODBC suporta a cópia de dados de/para **qualquer arquivos de dados compatível com ODBC** utilizando **básico** ou **anónimo** autenticação.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector ODBC, tem de:

- Defina um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes.
- Instale o controlador ODBC do arquivo de dados na máquina de tempo de execução de integração.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector ODBC.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado de ODBC:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Odbc** | Sim |
| connectionString | A cadeia de ligação, excluindo a parte da credencial. Pode especificar a cadeia de ligação com o padrão como `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, ou utilize o DSN (nome da origem de dados) configurou na máquina com o tempo de execução de integração do sistema `"DSN=<name of the DSN on IR machine>;"` (necessário ainda especificar a parte da credencial no serviço ligado em conformidade).| Sim |
| authenticationType | Tipo de autenticação utilizado para ligar ao arquivo de dados ODBC.<br/>Valores permitidos são: **básico** e **anónimo**. | Sim |
| Nome de utilizador | Especifique o nome de utilizador se estiver a utilizar autenticação básica. | Não |
| palavra-passe | Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. Marcar este campo como um SecureString. | Não |
| credencial | A parte de credencial de acesso da cadeia de ligação especificada no formato do valor da propriedade do controlador específico. Exemplo: `"RefreshToken=<secret refresh token>;"`. Marcar este campo como um SecureString. | Não |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Um tempo de execução de integração Self-hosted é necessário, tal como mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo 1: utilizar a autenticação básica**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

**Exemplo 2: utilizar a autenticação anónima**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados ODBC.

Para copiar dados de/para arquivo de dados compatível com ODBC, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **RelationalTable** | Sim |
| tableName | Nome da tabela no arquivo de dados ODBC. | Não para a origem (se for especificado "consulta" na origem de atividade);<br/>Sim para sink |

**Exemplo**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pela origem ODBC.

### <a name="odbc-as-source"></a>ODBC como origem

Para copiar dados de arquivo de dados compatível com ODBC, defina o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>ODBC como sink

Para copiar dados para o arquivo de dados compatível com ODBC, defina o tipo de sink na atividade de cópia para **OdbcSink**. As seguintes propriedades são suportadas na atividade de cópia **sink** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de sink de atividade de cópia tem de ser definida: **OdbcSink** | Sim |
| writeBatchTimeout |De tempo de espera para a operação de inserção de lote seja concluída antes de atingir o tempo limite.<br/>Valores permitidos são: timespan. Exemplo: "00: 30:00" (30 minutos). |Não |
| WriteBatchSize |Insere dados para a tabela SQL quando o tamanho da memória intermédia atinge writeBatchSize.<br/>Valores permitidos são: número inteiro (número de linhas). |Não (a predefinição é 0 - detetada automaticamente) |
| preCopyScript |Especifique uma consulta de SQL Server executar antes de escrever dados no arquivo de dados em cada execução de atividade de cópia. Pode utilizar esta propriedade para limpar os dados previamente carregados. |Não |

> [!NOTE]
> Para "writeBatchSize", se não estiver definido (detecção automática), a atividade de cópia primeiro Deteta se o controlador suporta operações de lote e configurá-lo para 10000 se existir ou definido como 1, se este não. Se definir explicitamente o valor diferente de 0, a atividade de cópia honra o valor e falha no tempo de execução se o controlador não suporta operações de lote.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="ibm-informix-source"></a>Origem do IBM Informix

Pode copiar dados da base de dados IBM Informix através do conector ODBC genérico.

Defina um tempo de execução de integração Self-hosted num computador com acesso ao seu arquivo de dados. O tempo de execução de integração utiliza o controlador ODBC para Informix para ligar ao arquivo de dados. Por conseguinte, instale o controlador se não estiver já instalado no mesmo computador. Por exemplo, pode utilizar o controlador "IBM INFORMIX o controlador ODBC (64-bit)". Consulte [pré-requisitos](#prerequisites) secção para obter detalhes.

Antes de utilizar a origem de Informix numa solução de fábrica de dados, verifique se o tempo de execução de integração pode ligar para o arquivo de dados utilizando as instruções em [resolver problemas de conectividade](#troubleshoot-connectivity-issues) secção.

Crie um serviço ligado de ODBC para ligar um arquivo de dados IBM Informix a um Azure data factory, conforme mostrado no exemplo seguinte:

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Leia o artigo a partir do início para uma descrição geral detalhada da utilização de dados de ODBC armazena como arquivos de dados de origem/dependente de uma operação de cópia.

## <a name="microsoft-access-source"></a>Origem do Microsoft Access

Pode copiar dados da base de dados do Microsoft Access através do conector ODBC genérico.

Defina um tempo de execução de integração Self-hosted num computador com acesso ao seu arquivo de dados. O tempo de execução de integração utiliza o controlador ODBC do Microsoft Access para ligar ao arquivo de dados. Por conseguinte, instale o controlador se não estiver já instalado no mesmo computador. Consulte [pré-requisitos](#prerequisites) secção para obter detalhes.

Antes de utilizar a origem do Microsoft Access numa solução de fábrica de dados, verifique se o tempo de execução de integração pode ligar para o arquivo de dados utilizando as instruções em [resolver problemas de conectividade](#troubleshoot-connectivity-issues) secção.

Crie um serviço ligado de ODBC para associar uma base de dados do Microsoft Access a um Azure data factory, conforme mostrado no exemplo seguinte:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Leia o artigo a partir do início para uma descrição geral detalhada da utilização de dados de ODBC armazena como arquivos de dados de origem/dependente de uma operação de cópia.

## <a name="ge-historian-source"></a>Origem de GE Historian

Pode copiar dados de Historian GE através do conector ODBC genérico.

Defina um tempo de execução de integração Self-hosted num computador com acesso ao seu arquivo de dados. O tempo de execução de integração utiliza o controlador ODBC para GE Historian para ligar ao arquivo de dados. Por conseguinte, instale o controlador se não estiver já instalado no mesmo computador. Consulte [pré-requisitos](#prerequisites) secção para obter detalhes.

Antes de utilizar a origem de GE Historian numa solução de fábrica de dados, verifique se o tempo de execução de integração pode ligar para o arquivo de dados utilizando as instruções em [resolver problemas de conectividade](#troubleshoot-connectivity-issues) secção.

Crie um serviço ligado de ODBC para associar uma base de dados do Microsoft Access a um Azure data factory, conforme mostrado no exemplo seguinte:

```json
{
    "name": "HistorianLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Leia o artigo a partir do início para uma descrição geral detalhada da utilização de dados de ODBC armazena como arquivos de dados de origem/dependente de uma operação de cópia.

## <a name="sap-hana-sink"></a>Sink de SAP HANA

>[!NOTE]
>Para copiar dados de arquivo de dados SAP HANA, consulte nativo [conector de SAP HANA](connector-sap-hana.md). Para copiar dados para SAP HANA, siga esta instrução para utilizar o conector ODBC. Deste modo, não pode ser reutilizada nota que são os serviços ligados para o conector de SAP HANA e conector ODBC com tipo diferente.
>

Pode copiar dados para a base de dados SAP HANA através do conector ODBC genérico.

Defina um tempo de execução de integração Self-hosted num computador com acesso ao seu arquivo de dados. O tempo de execução de integração utiliza o controlador ODBC para SAP HANA para ligar ao arquivo de dados. Por conseguinte, instale o controlador se não estiver já instalado no mesmo computador. Consulte [pré-requisitos](#prerequisites) secção para obter detalhes.

Antes de utilizar o sink de SAP HANA numa solução de fábrica de dados, verifique se o tempo de execução de integração pode ligar para o arquivo de dados utilizando as instruções em [resolver problemas de conectividade](#troubleshoot-connectivity-issues) secção.

Crie um serviço ligado de ODBC para ligar um arquivo de dados SAP HANA a um Azure data factory, conforme mostrado no exemplo seguinte:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

Leia o artigo a partir do início para uma descrição geral detalhada da utilização de dados de ODBC armazena como arquivos de dados de origem/dependente de uma operação de cópia.

## <a name="troubleshoot-connectivity-issues"></a>Resolver problemas de conectividade

Para resolver problemas de ligação, utilize o **diagnóstico** separador de **integração Runtime Configuration Manager**.

1. Iniciar **Gestor de configuração de tempo de execução de integração**.
2. Mudar para o **diagnóstico** separador.
3. Na secção "Ligação de teste", selecione o **tipo** de dados armazenar (serviço ligado).
4. Especifique o **cadeia de ligação** que é utilizada para ligar ao arquivo de dados, escolha o **autenticação** e introduza **nome de utilizador**, **palavra-passe**, e/ou **credenciais**.
5. Clique em **Testar ligação** para testar a ligação ao arquivo de dados.

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).