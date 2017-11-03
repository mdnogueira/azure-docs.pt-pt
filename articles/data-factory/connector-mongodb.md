---
title: Copiar dados de MongoDB utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados da base de dados do Mongo para os arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory."
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
ms.openlocfilehash: 6f11d4184b5edbcb80411f146ae01ecfe62b6e91
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copiar dados de MongoDB utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-on-premises-mongodb-connector.md)
> * [Versão 2 - Pré-visualização](connector-mongodb.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados de MongoDB. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [conector do MongoDB no V1](v1/data-factory-on-premises-mongodb-connector.md).


## <a name="supported-scenarios"></a>Cenários suportados

Pode copiar dados da base de dados de MongoDB para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector MongoDB suporta:

- MongoDB **versões 2.4, 2.6, 3.0 e 3.2**.
- Copiar dados utilizando **básico** ou **anónimo** autenticação.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de uma base de dados MongoDB, que não está acessível publicamente, terá de configurar um tempo de execução de integração Self-hosted. Consulte [Self-hosted integração Runtime](create-self-hosted-integration-runtime.md) artigo para obter detalhes. O tempo de execução de integração fornece um controlador de MongoDB incorporado, por conseguinte, não precisa de instalar manualmente a quaisquer controladores ao copiar dados de/para MongoDB.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir entidades do Data Factory específicas para o conector do MongoDB.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do MongoDB:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida: **MongoDb** |Sim |
| servidor |Nome anfitrião ou endereço IP do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor do MongoDB utiliza para escutar ligações de cliente. |Não (a predefinição é 27017) |
| DatabaseName |Nome da base de dados MongoDB que pretende aceder. |Sim |
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados MongoDB.<br/>Valores permitidos são: **básico**, e **anónimo**. |Sim |
| o nome de utilizador |Conta de utilizador para aceder a MongoDB. |Sim (se for utilizada a autenticação básica). |
| palavra-passe |Palavra-passe para o utilizador. Marcar este campo como SecureString. |Sim (se for utilizada a autenticação básica). |
| authSource |Nome da base de dados MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Não. Para a autenticação básica, a predefinição é utilizar a conta de administrador e a base de dados especificada utilizando a propriedade databaseName. |
| connectVia | O [integração Runtime](concepts-integration-runtime.md) para ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração Self-hosted ou Runtime de integração do Azure (se o arquivo de dados acessível publicamente). Se não for especificado, utiliza a predefinição de Runtime de integração do Azure. |Não |

**Exemplo:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
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

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o artigo de conjuntos de dados. Esta secção fornece uma lista de propriedades suportadas por conjunto de dados de MongoDB.

Para copiar dados de MongoDB, defina a propriedade de tipo do conjunto de dados para **MongoDbCollection**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **MongoDbCollection** | Sim |
| CollectionName |Nome da coleção na base de dados de MongoDB. |Sim |

**Exemplo:**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }

```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportadas por origem do MongoDB.

### <a name="mongodb-as-source"></a>MongoDB como origem

Para copiar dados de MongoDB, defina o tipo de origem na atividade de cópia para **MongoDbSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo da origem de atividade de cópia tem de ser definida: **MongoDbSource** | Sim |
| consulta |Utilize a consulta de SQL 92 personalizada para ler os dados. Por exemplo: selecionar * de MyTable. |Não (se for especificado "collectionName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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

> [!TIP]
> Especifique quando a consulta SQL, preste atenção, no formato DateTime. Por exemplo: `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados

Serviço do Azure Data Factory infere esquema de uma coleção de MongoDB utilizando o **mais recentes 100 documentos** na coleção. Se estes 100 documentos não contêm esquema completa, algumas colunas podem ser ignoradas durante a operação de cópia.

## <a name="data-type-mapping-for-mongodb"></a>Tipo de dados de mapeamento para MongoDB

Quando copiar dados de MongoDB, os seguintes mapeamentos são utilizados MongoDB tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados de MongoDB | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| Binário |Byte] |
| Valor booleano |Valor booleano |
| Data |DateTime |
| NumberDouble |duplo |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Cadeia |
| Cadeia |Cadeia |
| UUID |GUID |
| Objeto |Renormalized para aplanar colunas com "_" como separador aninhada |

> [!NOTE]
> Para saber mais sobre o suporte para as matrizes de tabelas virtuais a utilizar, consulte [suporte para tipos complexos utilizando tabelas virtuais](#support-for-complex-types-using-virtual-tables) secção.
>
> Atualmente, não são suportados os seguintes tipos de dados de MongoDB: DBPointer, JavaScript, Máx. por minuto da chave, expressão Regular, símbolo, Timestamp, Undefined.

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos utilizando tabelas virtuais

O Azure Data Factory utiliza um controlador ODBC incorporado para ligar a e copiar os dados da sua base de dados de MongoDB. Para os tipos complexos, tais como os objetos ou matrizes com diferentes tipos entre os documentos, o controlador normaliza novamente dados em tabelas virtuais correspondentes. Especificamente, se uma tabela que contém este tipo de colunas, o controlador gera as tabelas seguintes virtuais:

* A **tabela base**, que contém os mesmos dados que a tabela real, exceto as colunas de tipo complexo. A tabela base utiliza o mesmo nome como a tabela real que representa.
* A **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais são denominadas utilizando o nome de tabela real, existe um separador de "_" e o nome da matriz ou objeto.

Consulte os dados na tabela real, ativar o controlador para aceder aos dados denormalized tabelas virtuais. Pode aceder ao conteúdo de matrizes de MongoDB consultando e associar as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, aqui ExampleTable é uma tabela de MongoDB que tenha uma coluna com uma matriz de objetos em cada célula – faturas e uma coluna com uma matriz de tipos escalares – classificações.

| ID | Nome do cliente | Faturas | Nível de serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123" item: "toaster", o preço: Desconto "456": "0,2"}, {invoice_id: "124" item: "oven", o preço: Desconto "1235": "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: item "135": "fridge", o preço: Desconto "12543": "0,0"}] |Gold |[1,2] |

O controlador irá gerar várias tabelas virtuais para representar esta tabela única. A primeira tabela virtual é a tabela base "ExampleTable" mostrado no exemplo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foi omitidos e são expandidos nas tabelas virtuais.

| ID | Nome do cliente | Nível de serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas seguintes mostram as tabelas virtuais que representam matrizes originais no exemplo. Estas tabelas contenham o seguinte:

* Uma referência para a coluna de chave primária original correspondente para a linha da matriz original (através da coluna de ID)
* Uma indicação da posição de dados dentro da matriz original
* Os dados expandidos para cada elemento na matriz

**Tabela "ExampleTable_Invoices":**

| ID | ExampleTable_Invoices_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

**Tabela "ExampleTable_Ratings":**

| ID | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).