---
title: Mover dados de Cassandra utilizando o Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados de uma base de dados no local Cassandra utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 06ee23d7e6a0f046e9656a40fca105ff6270a481
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Mover dados de uma base de dados no local Cassandra utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-onprem-cassandra-connector.md)
> * [Versão 2 - Pré-visualização](../connector-cassandra.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector Cassandra na V2](../connector-cassandra.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de uma base de dados de Cassandra no local. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados de Cassandra no local para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Fábrica de dados atualmente suporta apenas mover dados a partir de um arquivo de dados Cassandra para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um arquivo de dados Cassandra. 

## <a name="supported-versions"></a>Versões suportadas
O conector de Cassandra suporta as seguintes versões do Cassandra: 2. x.

## <a name="prerequisites"></a>Pré-requisitos
Para o serviço do Azure Data Factory conseguir ligar à base de dados no local Cassandra, tem de instalar um Gateway de gestão de dados no mesmo computador que aloja a base de dados ou num computador separado para evitar a competir pela recursos com a base de dados. O Data Management Gateway é um componente que liga as origens de dados no local a serviços em nuvem de forma segura e gerida. Consulte [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter detalhes sobre o Data Management Gateway. Consulte [mover os dados no local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como configurar o gateway de um pipeline de dados mover os dados.

Tem de utilizar o gateway para ligar a uma base de dados Cassandra, mesmo se a base de dados está alojado na nuvem, por exemplo, numa VM do IaaS do Azure. Y pode tem o gateway na mesma VM que aloja a base de dados ou numa VM separada desde que o gateway pode ligar à base de dados.  

Quando a instalação do gateway, instala automaticamente um controlador ODBC do Microsoft Cassandra utilizado para ligar à base de dados Cassandra. Por conseguinte, não precisa de instalar manualmente os controladores no computador gateway ao copiar dados da base de dados Cassandra. 

> [!NOTE]
> Consulte [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para dicas sobre/gateway de ligação de resolução de problemas relacionados com problemas.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de um arquivo de dados no local Cassandra utilizando ferramentas diferentes/APIs. 

- A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados. 
- Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink:

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados de Cassandra no local, consulte [exemplo JSON: copiar dados de Cassandra para Blob do Azure](#json-example-copy-data-from-cassandra-to-azure-blob) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para um arquivo de dados Cassandra:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicos do serviço de Cassandra ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **OnPremisesCassandra** |Sim |
| anfitrião |Uma ou mais endereços IP ou nomes de anfitrião dos servidores de Cassandra.<br/><br/>Especifica uma lista separada por vírgulas dos endereços IP ou nomes de anfitrião para ligar a todos os servidores em simultâneo. |Sim |
| porta |A porta TCP que o servidor de Cassandra utiliza para escutar ligações de cliente. |Valor predefinido não,: 9042 |
| authenticationType |Basic ou Anonymous |Sim |
| o nome de utilizador |Especifique o nome de utilizador da conta de utilizador. |Sim, se authenticationType está definido para básico. |
| palavra-passe |Especifique a palavra-passe da conta de utilizador. |Sim, se authenticationType está definido para básico. |
| gatewayName |O nome do gateway que é utilizado para ligar à base de dados de Cassandra no local. |Sim |
| encryptedCredential |Credencial encriptada pelo gateway. |Não |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **CassandraTable** tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| keyspace |Nome do esquema na base de dados Cassandra ou keyspace. |Sim (se **consulta** para **CassandraSource** não está definido). |
| tableName |Nome da tabela na base de dados Cassandra. |Sim (se **consulta** para **CassandraSource** não está definido). |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

Enquanto, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Quando a origem é do tipo **CassandraSource**, na secção typeProperties, estão disponíveis as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Consulta de SQL 92 ou consulta CQL. Consulte [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Quando utilizar a consulta SQL, especifique **keyspace name.table nome** para representar a tabela que pretende consultar. |Não (se for tableName e keyspace no conjunto de dados estão definidos). |
| consistencyLevel |O nível de consistência Especifica quantos réplicas tem de responder a um pedido de leitura antes da devolução de dados para a aplicação de cliente. Cassandra verifica o número especificado de réplicas de dados satisfazer o pedido de leitura. |UM, DOIS, TRÊS, QUÓRUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Consulte [consistência dos dados de configuração](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obter mais detalhes. |Não. Valor predefinido é um. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Exemplo JSON: copiar dados de Cassandra para Blob do Azure
Neste exemplo fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar dados de uma base de dados de Cassandra no local para um armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

> [!IMPORTANT]
> Este exemplo fornece fragmentos JSON. Não inclui instruções passo a passo para criação da fábrica de dados. Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo.

O exemplo tem as seguintes entidades de fábrica de dados:

* Um serviço ligado do tipo [OnPremisesCassandra](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Uma entrada [dataset](data-factory-create-datasets.md) do tipo [CassandraTable](#dataset-properties).
* Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [CassandraSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra serviço ligado:**

Este exemplo utiliza o **Cassandra** serviço ligado. Consulte [Cassandra serviço ligado](#linked-service-properties) secção para as propriedades suportadas por este serviço ligado.  

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Serviço ligado do Storage do Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Conjunto de dados entrado de Cassandra:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
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

Definição **externo** para **verdadeiro** informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

**Conjunto de dados de saída do Blob do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Atividade de cópia num pipeline com a origem de Cassandra e o sink de Blob:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **CassandraSource** e **sink** tipo está definido como **BlobSink**.

Consulte [propriedades do tipo RelationalSource](#copy-activity-properties) para a lista de propriedades suportadas pelo RelationalSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

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

### <a name="type-mapping-for-cassandra"></a>Mapeamento de tipos para Cassandra
| Tipo de Cassandra | .NET com base em tipo |
| --- | --- |
| ASCII |Cadeia |
| BIGINT |Int64 |
| BLOB |Byte] |
| VALOR BOOLEANO |Valor booleano |
| DECIMAL |Decimal |
| VALOR DE DUPLO |duplo |
| NÚMERO DE VÍRGULA FLUTUANTE |Único |
| INET |Cadeia |
| INT |Int32 |
| TEXTO |Cadeia |
| TIMESTAMP |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Cadeia |
| VARINT |Decimal |

> [!NOTE]
> Coleção de tipos (mapa, conjunto, lista, etc.), consulte [trabalhar com os tipos de coleção Cassandra utilizando a tabela virtual](#work-with-collections-using-virtual-table) secção.
>
> Não são suportados tipos definidos pelo utilizador.
>
> O comprimento de comprimentos de coluna de binário e a coluna de cadeia não pode ser superior a 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções utilizando a tabela virtual
O Azure Data Factory utiliza um controlador ODBC incorporado para ligar a e copiar os dados da sua base de dados Cassandra. Para tipos de coleção, incluindo o mapa, conjunto e lista, o controlador renormalizes os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contém quaisquer colunas de coleção, o controlador gera as tabelas seguintes virtuais:

* A **tabela base**, que contém os mesmos dados que a tabela real, exceto as colunas de coleção. A tabela base utiliza o mesmo nome como a tabela real que representa.
* A **tabela virtual** para cada coluna de coleção, que expande os dados aninhados. As tabelas virtuais que representam as coleções são denominadas utilizando o nome da tabela real, o separador "*vt*" e o nome da coluna.

Consulte os dados na tabela real, ativar o controlador para aceder aos dados denormalized tabelas virtuais. Consulte a secção de exemplo para obter mais detalhes. Pode aceder ao conteúdo de coleções Cassandra consultando e associar as tabelas virtuais.

Pode utilizar o [Assistente para copiar](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivo ver a lista de tabelas na base de dados de Cassandra, incluindo as tabelas virtuais e pré-visualizar os dados no interior. Também pode construir uma consulta no Assistente de cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "Seguinte ExampleTable" é uma tabela de base de dados de Cassandra que contém a coluna de chave primária de um número inteiro com o nome "pk_int", uma coluna de texto com o nome de valor, uma coluna de lista, uma coluna de mapa e um conjunto de colunas (com o nome "StringSet").

| pk_int | Valor | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de exemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"value 3 do exemplo" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O controlador irá gerar várias tabelas virtuais para representar esta tabela única. As colunas de chave externas nas tabelas virtuais referenciam as colunas de chave primárias na tabela real e indicam que linha de tabela real corresponde à opção da linha de tabela virtual.

A primeira tabela virtual é a tabela base com o nome "ExampleTable" é apresentada na seguinte tabela. A tabela base contém os mesmos dados que a tabela de base de dados original, exceto as coleções, que são omitido desta tabela e expandido em outras tabelas virtuais.

| pk_int | Valor |
| --- | --- |
| 1 |"valor de exemplo 1" |
| 3 |"value 3 do exemplo" |

As tabelas seguintes mostram as tabelas de virtuais renormalize os dados a partir das lista, mapa e StringSet colunas. As colunas com nomes que terminem com "_index" ou "c_have" indicam a posição de dados dentro da lista original ou do mapa. As colunas com nomes que terminem com "_value" contêm os dados expandidos da coleção.

#### <a name="table-exampletablevtlist"></a>Tabela "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>Tabela "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |B |
| 3 |S1 |T |

#### <a name="table-exampletablevtstringset"></a>Tabela "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |I |

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem em colunas no conjunto de dados do sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repetíveis leitura a partir de origens relacionais
Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
