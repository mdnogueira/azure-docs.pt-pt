---
title: Mover dados de MongoDB a utilizar o Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados da base de dados de MongoDB utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 10ca7d9a-7715-4446-bf59-2d2876584550
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 569e5a3bf8227caf003a9ea9ff897b29d7b0cf19
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Mover dados de MongoDB utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-on-premises-mongodb-connector.md)
> * [Versão 2 - Pré-visualização](../connector-mongodb.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector do MongoDB no V2](../connector-mongodb.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de uma base de dados de MongoDB no local. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados de MongoDB no local para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Fábrica de dados atualmente suporta apenas mover dados a partir de um arquivo de dados de MongoDB a outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um arquivo de dados de MongoDB. 

## <a name="prerequisites"></a>Pré-requisitos
Para o serviço do Azure Data Factory conseguir ligar à base de dados MongoDB no local, tem de instalar os componentes seguintes:

- Versões suportadas do MongoDB são: 2.4, 2.6, 3.0 e 3.2.
- O Data Management Gateway no mesmo computador que aloja a base de dados ou num computador separado para evitar a competir pela recursos com a base de dados. O Data Management Gateway é um software que liga as origens de dados no local a serviços em nuvem de forma segura e gerida. Consulte [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter detalhes sobre o Data Management Gateway. Consulte [mover os dados no local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como configurar o gateway de um pipeline de dados mover os dados.

    Quando a instalação do gateway, instala automaticamente um controlador Microsoft MongoDB ODBC utilizado para ligar ao MongoDB.

    > [!NOTE]
    > Terá de utilizar o gateway para ligar a MongoDB, mesmo esteja alojado numa VMs IaaS do Azure. Se estiver a tentar ligar a uma instância do MongoDB alojada na nuvem, também pode instalar a instância do gateway na VM do IaaS.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de um arquivo de dados de MongoDB no local utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink: 

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados de MongoDB no local, consulte [exemplo JSON: copiar dados de MongoDB para Blob do Azure](#json-example-copy-data-from-mongodb-to-azure-blob) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir o MongoDB origem entidades do Data Factory específicas:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicos **OnPremisesMongoDB** serviço ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **OnPremisesMongoDb** |Sim |
| servidor |Nome anfitrião ou endereço IP do servidor MongoDB. |Sim |
| porta |Porta TCP que o servidor do MongoDB utiliza para escutar ligações de cliente. |Opcional, valor predefinido: 27017 |
| authenticationType |Basic ou Anonymous. |Sim |
| o nome de utilizador |Conta de utilizador para aceder a MongoDB. |Sim (se for utilizada a autenticação básica). |
| palavra-passe |Palavra-passe para o utilizador. |Sim (se for utilizada a autenticação básica). |
| authSource |Nome da base de dados MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Opcional (se for utilizada a autenticação básica). predefinição: utiliza a conta de administrador e a base de dados especificada utilizando a propriedade databaseName. |
| DatabaseName |Nome da base de dados MongoDB que pretende aceder. |Sim |
| gatewayName |Nome do gateway que acede ao arquivo de dados. |Sim |
| encryptedCredential |Credencial encriptada pelo gateway. |Opcional |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **MongoDbCollection** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| CollectionName |Nome da coleção na base de dados de MongoDB. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e política estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis no **typeProperties** secção da atividade por outro lado variar com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Quando a origem é do tipo **MongoDbSource** na secção typeProperties, estão disponíveis as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta de SQL 92. Por exemplo: selecionar * de MyTable. |Não (se **collectionName** de **dataset** especificado) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>Exemplo JSON: copiar dados de MongoDB para Blob do Azure
Neste exemplo fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar dados de um MongoDB no local para um armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

O exemplo tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [OnPremisesMongoDb](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [MongoDbCollection](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [MongoDbSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta na base de dados de MongoDB para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

Como primeiro passo, o gateway de gestão de dados de acordo com as instruções de configuração a [Data Management Gateway](data-factory-data-management-gateway.md) artigo.

**Serviço ligado do MongoDB:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",  
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
           "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
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

**Conjunto de dados de entrada MongoDB:** definição "external": "true" informa o serviço fábrica de dados que a tabela é externa à fábrica de dados e não é produzida por uma atividade no factory de dados.

```json
{
     "name":  "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"    
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Conjunto de dados de saída do Blob do Azure:**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Atividade de cópia num pipeline com a origem do MongoDB e o sink de Blob:**

O pipeline contém uma atividade de cópia está configurado para utilizar a entrada acima e conjuntos de dados de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **MongoDbSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **consulta** propriedade seleciona os dados na última hora para copiar.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados
Serviço do Azure Data Factory infere esquema de uma coleção de MongoDB através da utilização de 100 documentos mais recentes na coleção. Se estes 100 documentos não contêm esquema completa, algumas colunas podem ser ignoradas durante a operação de cópia.

## <a name="type-mapping-for-mongodb"></a>Mapeamento de tipos para MongoDB
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia realiza conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de passo 2:

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Ao mover dados para o MongoDB os seguintes mapeamentos são utilizados dos tipos de MongoDB para tipos de .NET.

| Tipo do MongoDB | Tipo de .NET framework |
| --- | --- |
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
> Para saber mais sobre o suporte para as matrizes de tabelas virtuais a utilizar, consulte [suporte para tipos complexos utilizando tabelas virtuais](#support-for-complex-types-using-virtual-tables) secção abaixo.

Atualmente, não são suportados os seguintes tipos de dados de MongoDB: DBPointer, JavaScript, Máx. por minuto da chave, expressão Regular, símbolo, Timestamp, Undefined

## <a name="support-for-complex-types-using-virtual-tables"></a>Suporte para tipos complexos utilizando tabelas virtuais
O Azure Data Factory utiliza um controlador ODBC incorporado para ligar a e copiar os dados da sua base de dados de MongoDB. Para os tipos complexos, tais como os objetos ou matrizes com diferentes tipos entre os documentos, o controlador normaliza novamente dados em tabelas virtuais correspondentes. Especificamente, se uma tabela que contém este tipo de colunas, o controlador gera as tabelas seguintes virtuais:

* A **tabela base**, que contém os mesmos dados que a tabela real, exceto as colunas de tipo complexo. A tabela base utiliza o mesmo nome como a tabela real que representa.
* A **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais são denominadas utilizando o nome de tabela real, existe um separador de "_" e o nome da matriz ou objeto.

Consulte os dados na tabela real, ativar o controlador para aceder aos dados denormalized tabelas virtuais. Consulte a secção de exemplo abaixo os detalhes. Pode aceder ao conteúdo de matrizes de MongoDB consultando e associar as tabelas virtuais.

Pode utilizar o [Assistente para copiar](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivo ver a lista de tabelas na base de dados de MongoDB, incluindo as tabelas virtuais e pré-visualizar os dados no interior. Também pode construir uma consulta no Assistente de cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "ExampleTable" abaixo é uma tabela de MongoDB que tem uma coluna com uma matriz de objetos em cada célula – faturas e uma coluna com uma matriz de tipos escalares – classificações.

| ID | Nome do cliente | Faturas | Nível de serviço | Classificações |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123" item: "toaster", o preço: Desconto "456": "0,2"}, {invoice_id: "124" item: "oven", o preço: Desconto "1235": "0,2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id: item "135": "fridge", o preço: Desconto "12543": "0,0"}] |Gold |[1,2] |

O controlador irá gerar várias tabelas virtuais para representar esta tabela única. A primeira tabela virtual é a tabela base "ExampleTable", abaixo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foi omitidos e são expandidos nas tabelas virtuais.

| ID | Nome do cliente | Nível de serviço |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

As tabelas seguintes mostram as tabelas virtuais que representam matrizes originais no exemplo. Estas tabelas contenham o seguinte:

* Uma referência para a coluna de chave primária original correspondente para a linha da matriz original (através da coluna de ID)
* Uma indicação da posição de dados dentro da matriz original
* Os dados expandidos para cada elemento na matriz

Tabela "ExampleTable_Invoices":

| ID | ExampleTable_Invoices_dim1_idx | invoice_id | item | preço | Desconto |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |Toaster |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

Tabela "ExampleTable_Ratings":

| ID | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem em colunas no conjunto de dados do sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repetíveis leitura a partir de origens relacionais
Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.

## <a name="next-steps"></a>Passos Seguintes
Consulte [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo para criar um pipeline de dados que move os dados de um arquivo de dados no local para um arquivo de dados do Azure.
