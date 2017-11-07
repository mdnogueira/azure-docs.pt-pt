---
title: Copiar dados de Cassandra utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados de Cassandra aos arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory."
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
ms.openlocfilehash: eba08c38a5502368beda7ca7f84559ecca011133
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Copiar dados de Cassandra utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-onprem-cassandra-connector.md)
> * [Versão 2 - Pré-visualização](connector-cassandra.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados Cassandra. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.


> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector Cassandra no V1](v1/data-factory-onprem-cassandra-connector.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados Cassandra para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Cassandra suporta:

- Cassandra **versões 2**.
- Copiar dados utilizando **básico** ou **anónimo** autenticação.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de uma base de dados de Cassandra que não está acessível publicamente, terá de configurar um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes. O tempo de execução de integração fornece um controlador de Cassandra incorporado, por conseguinte, não precisa de instalar manualmente a quaisquer controladores ao copiar dados de/para Cassandra.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir o conector Cassandra entidades do Data Factory específicas.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço de Cassandra ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida: **Cassandra** |Sim |
| anfitrião |Uma ou mais endereços IP ou nomes de anfitrião dos servidores de Cassandra.<br/>Especifica uma lista separada por vírgulas dos endereços IP ou nomes de anfitrião para ligar a todos os servidores em simultâneo. |Sim |
| porta |A porta TCP que o servidor de Cassandra utiliza para escutar ligações de cliente. |Não (a predefinição é 9042) |
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados Cassandra.<br/>Valores permitidos são: **básico**, e **anónimo**. |Sim |
| o nome de utilizador |Especifique o nome de utilizador da conta de utilizador. |Sim, se authenticationType está definido para básico. |
| palavra-passe |Especifique a palavra-passe da conta de utilizador. Marcar este campo como SecureString. |Sim, se authenticationType está definido para básico. |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração Self-hosted ou Runtime de integração do Azure (se o arquivo de dados acessível publicamente). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
            "username": "<username>",
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por Cassandra conjunto de dados.

Para copiar dados de Cassandra, defina a propriedade de tipo do conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **CassandraTable** | Sim |
| keyspace |Nome do esquema na base de dados Cassandra ou keyspace. |Não (se for especificada "consulta" para "CassandraSource") |
| tableName |Nome da tabela na base de dados Cassandra. |Não (se for especificada "consulta" para "CassandraSource") |

**Exemplo:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia


Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por origem Cassandra.

### <a name="cassandra-as-source"></a>Cassandra como origem

Para copiar dados de Cassandra, defina o tipo de origem na atividade de cópia para **CassandraSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **CassandraSource** | Sim |
| consulta |Utilize a consulta personalizada para ler os dados. |Consulta de SQL 92 ou consulta CQL. Consulte [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Quando utilizar a consulta SQL, especifique **keyspace name.table nome** para representar a tabela que pretende consultar. |Não (se estiverem especificados "tableName" e "keyspace" no conjunto de dados). |
| consistencyLevel |O nível de consistência Especifica quantos réplicas tem de responder a um pedido de leitura antes da devolução de dados para a aplicação de cliente. Cassandra verifica o número especificado de réplicas de dados satisfazer o pedido de leitura. Consulte [consistência dos dados de configuração](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obter mais detalhes.<br/><br/>Valores permitidos são: **um**, **dois**, **três**, **QUÓRUM**, **todos os**, **LOCAL_ QUÓRUM**, **EACH_QUORUM**, e **LOCAL_ONE**. |Não (predefinição é `ONE`) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Tipo de dados de mapeamento para Cassandra

Quando copiar dados de Cassandra, os seguintes mapeamentos são utilizados Cassandra tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados de Cassandra | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
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

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções utilizando a tabela virtual

O Azure Data Factory utiliza um controlador ODBC incorporado para ligar a e copiar os dados da sua base de dados Cassandra. Para tipos de coleção, incluindo o mapa, conjunto e lista, o controlador renormalizes os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contém quaisquer colunas de coleção, o controlador gera as tabelas seguintes virtuais:

* A **tabela base**, que contém os mesmos dados que a tabela real, exceto as colunas de coleção. A tabela base utiliza o mesmo nome como a tabela real que representa.
* A **tabela virtual** para cada coluna de coleção, que expande os dados aninhados. As tabelas virtuais que representam as coleções são denominadas utilizando o nome da tabela real, o separador "*vt*" e o nome da coluna.

Consulte os dados na tabela real, ativar o controlador para aceder aos dados denormalized tabelas virtuais. Consulte a secção de exemplo para obter mais detalhes. Pode aceder ao conteúdo de coleções Cassandra consultando e associar as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, "Seguinte ExampleTable" é uma tabela de base de dados de Cassandra que contém a coluna de chave primária de um número inteiro com o nome "pk_int", uma coluna de texto com o nome de valor, uma coluna de lista, uma coluna de mapa e um conjunto de colunas (com o nome "StringSet").

| pk_int | Valor | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de exemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"value 3 do exemplo" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O controlador irá gerar várias tabelas virtuais para representar esta tabela única. As colunas de chave externas nas tabelas virtuais referenciam as colunas de chave primárias na tabela real e indicam que linha de tabela real corresponde à opção da linha de tabela virtual.

A primeira tabela virtual é a tabela base com o nome "ExampleTable" é apresentada na seguinte tabela: 

| pk_int | Valor |
| --- | --- |
| 1 |"valor de exemplo 1" |
| 3 |"value 3 do exemplo" |

A tabela base contém os mesmos dados que a tabela de base de dados original, exceto as coleções, que são omitido desta tabela e expandido em outras tabelas virtuais.

As tabelas seguintes mostram as tabelas de virtuais renormalize os dados a partir das lista, mapa e StringSet colunas. As colunas com nomes que terminem com "_index" ou "c_have" indicam a posição de dados dentro da lista original ou do mapa. As colunas com nomes que terminem com "_value" contêm os dados expandidos da coleção.

**Tabela "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabela "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |B |
| 3 |S1 |T |

**Tabela "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |I |

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).