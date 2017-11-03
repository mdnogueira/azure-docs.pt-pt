---
title: Mover dados de arquivos de dados ODBC | Microsoft Docs
description: Saiba mais sobre como mover dados de arquivos de dados ODBC utilizando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cbacf8b73f1eea520000f9406044b072fe36235f
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Mover dados de ODBC os arquivos de dados utilizando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](data-factory-odbc-connector.md)
> * [Versão 2 - Pré-visualização](../connector-odbc.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [conector ODBC na V2](../connector-odbc.md).


Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de um arquivo de dados ODBC no local. Baseia-se no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma descrição geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados ODBC para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como sinks pela atividade de cópia, consulte o [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. Fábrica de dados atualmente suporta apenas mover dados a partir de um arquivo de dados ODBC ao outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um arquivo de dados ODBC. 

## <a name="enabling-connectivity"></a>Ativar a conectividade
O serviço de fábrica de dados suporta a ligar a origens ODBC no local utilizando o Data Management Gateway. Consulte [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para saber mais sobre o Data Management Gateway e instruções passo a passo sobre como configurar o gateway. Utilize o gateway para ligar a um arquivo de dados ODBC, mesmo esteja alojado numa VM do IaaS do Azure.

Pode instalar o gateway no mesmo computador no local ou VM do Azure como o arquivo de dados ODBC. No entanto, recomendamos que instale o gateway de um máquina/Azure separado IaaS VM para evitar a contenção de recursos e para um melhor desempenho. Quando instalar o gateway num computador separado, a máquina deve ser capaz de aceder a máquina com o arquivo de dados ODBC.

Para além do Data Management Gateway, também tem de instalar o controlador ODBC do arquivo de dados no computador gateway.

> [!NOTE]
> Consulte [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para dicas sobre/gateway de ligação de resolução de problemas relacionados com problemas.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia move os dados de um arquivo de dados ODBC utilizando ferramentas diferentes/APIs.

A forma mais fácil de criar um pipeline que consiste em utilizar o **Assistente para copiar**. Consulte [Tutorial: criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para instruções rápidas sobre como criar um pipeline com o Assistente de cópia de dados.

Também pode utilizar as ferramentas seguintes para criar um pipeline: **portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Consulte [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se utilizar as ferramentas ou APIs, execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados do sink: 

1. Criar **serviços ligados** associar dados de entrada e de saída armazena à fábrica de dados.
2. Criar **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia executa um conjunto de dados como entrada e um conjunto de dados como resultado. 

Quando utilizar o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e o pipeline) são criadas automaticamente para si. Ao utilizar ferramentas/APIs (exceto .NET API), é possível definir estas entidades do Data Factory, utilizando o formato JSON.  Para um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados ODBC, consulte [exemplo JSON: arquivo de dados de cópia de dados de ODBC para Blob do Azure](#json-example-copy-data-from-odbc-data-store-to-azure-blob) secção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON que são utilizados para definir entidades do Data Factory específicas para o arquivo de dados ODBC:

## <a name="linked-service-properties"></a>Propriedades de serviço ligado
A tabela seguinte fornece uma descrição para os elementos JSON específicos ODBC serviço ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida: **OnPremisesOdbc** |Sim |
| connectionString |A parte de uma credencial de acesso não da cadeia de ligação e uma credencial encriptada opcional. Veja exemplos nas seguintes secções: <br/><br/>Pode especificar a cadeia de ligação com o padrão como `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, ou utilizar o sistema DSN (nome da origem de dados), configurado no computador gateway com `"DSN=<name of the DSN>;"` (necessário ainda especificar a parte da credencial no serviço ligado em conformidade). |Sim |
| credencial |A parte de credencial de acesso da cadeia de ligação especificada no formato do valor da propriedade do controlador específico. Exemplo: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar ao arquivo de dados ODBC. Os valores possíveis são: básico e anónimos. |Sim |
| o nome de utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço fábrica de dados deve utilizar para ligar ao arquivo de dados ODBC. |Sim |

### <a name="using-basic-authentication"></a>Utilizar a autenticação básica

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Utilizar a autenticação básica com credenciais encriptado
Pode encriptar as credenciais utilizando o [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) cmdlet (1.0 versão do Azure PowerShell) ou [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 ou anterior a versão do Azure PowerShell).  

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Autenticação anónima

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```


## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para uma lista completa das secções & Propriedades disponíveis para definir os conjuntos de dados, consulte o [criar conjuntos de dados](data-factory-create-datasets.md) artigo. As secções, tais como a estrutura, a disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, Azure blob, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados ODBC) tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no arquivo de dados ODBC. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para uma lista completa das secções & Propriedades disponíveis para definir as atividades, consulte o [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição e de saída, tabelas e as políticas estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis no **typeProperties** secção da atividade por outro lado variar com cada tipo de atividade. Para a atividade de cópia, podem variam consoante os tipos de origens e sinks.

Na atividade de cópia, quando a origem é do tipo **RelationalSource** (que inclui ODBC), na secção typeProperties, estão disponíveis as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler os dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable. |Sim |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>Exemplo JSON: arquivo de dados de cópia de dados de ODBC para Blob do Azure
Neste exemplo fornece definições de JSON que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar dados a partir de uma origem ODBC para um armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) utilizando a atividade de cópia no Azure Data Factory.

O exemplo tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [OnPremisesOdbc](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Uma entrada [dataset](data-factory-create-datasets.md) do tipo [RelationalTable](#dataset-properties).
4. Uma saída [dataset](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [pipeline](data-factory-create-pipelines.md) com atividade de cópia que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados a partir um resultado da consulta de um arquivo de dados ODBC para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas secções seguintes exemplos.

Como primeiro passo, configure o data management gateway. As instruções são no [mover dados entre localizações no local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo.

**Serviço ligado de ODBC** este exemplo utiliza a autenticação básica. Consulte [serviço ligado de ODBC](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Serviço ligado do Armazenamento do Azure**

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

**Conjunto de dados de entrada de ODBC**

O exemplo assume que criou uma tabela "MyTable" numa base de dados ODBC e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo.

A definição "external": "true" informa o serviço fábrica de dados que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade no factory de dados.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
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

**Conjunto de dados de saída de Blobs do Azure**

Dados são escritos num blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e em partes de horas a hora de início.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Atividade de cópia de um pipeline com a origem ODBC (RelationalSource) e o sink de Blob (BlobSink)**

O pipeline contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e de saída e está agendada para execução a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta de SQL Server especificada para o **consulta** propriedade seleciona os dados na última hora para copiar.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
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
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Mapeamento de tipos para ODBC
Tal como mencionado no [atividades de movimentos de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia realiza conversões de tipo automática dos tipos de origem para sink tipos com a seguinte abordagem de dois passos:

1. Converter de tipos de origens de nativo para o tipo .NET
2. Converter o tipo de sink nativo do tipo .NET

Quando mover dados de arquivos de dados ODBC, tipos de dados ODBC estão mapeados para tipos de .NET, tal como mencionado no [mapeamentos de tipos de dados de ODBC](https://msdn.microsoft.com/library/cc668763.aspx) tópico.

## <a name="map-source-to-sink-columns"></a>Origem de mapa para sink colunas
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem em colunas no conjunto de dados do sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repetíveis leitura a partir de origens relacionais
Quando armazena a cópia de dados de dados relacionais, manter a repetibilidade em mente para evitar resultados inesperados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor é voltar a executar qualquer forma, tem de certificar-se de que os mesmos dados é a leitura não independentemente um setor é executado o número de vezes. Consulte [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="ge-historian-store"></a>Arquivo de GE Historian
Criar um serviço ligado de ODBC para ligar um [GE Proficy Historian (agora GE Historian)](http://www.geautomation.com/products/proficy-historian) do arquivo de dados para um Azure data factory conforme mostrado no exemplo seguinte:

```json
{
    "name": "HistorianLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "connectionString": "DSN=<name of the GE Historian store>;",
            "gatewayName": "<gateway name>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": "<password>"
        }
    }
}
```

Instalar o Data Management Gateway numa máquina no local e registar o gateway com o portal. O gateway instalado no seu computador no local utiliza o controlador ODBC para GE Historian para ligar ao arquivo de dados de GE Historian. Por conseguinte, instale o controlador se não estiver já instalado no computador gateway. Consulte [ativar conectividade](#enabling-connectivity) secção para obter detalhes.

Antes de utilizar o arquivo de GE Historian numa solução de fábrica de dados, verifique se o gateway pode ligar ao arquivo de dados com as instruções na secção seguinte.

Leia o artigo a partir do início para uma descrição geral detalhada da utilização de dados de ODBC armazena como arquivos de dados de origem de uma operação de cópia.  

## <a name="troubleshoot-connectivity-issues"></a>Resolver problemas de conectividade
Para resolver problemas de ligação, utilize o **diagnóstico** separador de **Gestor de configuração do Data Management Gateway**.

1. Iniciar **Gestor de configuração do Data Management Gateway**. Pode optar por executar "C:\Program Files\Microsoft Data gestão Gateway\1.0\Shared\ConfigManager.exe" diretamente (ou) pesquisa para **Gateway** encontrar uma ligação para **Microsoft Data Management Gateway** aplicação conforme mostrado na imagem seguinte.

    ![Gateway de pesquisa](./media/data-factory-odbc-connector/search-gateway.png)
2. Mudar para o **diagnóstico** separador.

    ![Diagnóstico do gateway](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Selecione o **tipo** de dados armazenar (serviço ligado).
4. Especifique **autenticação** e introduza **credenciais** (ou) introduza **cadeia de ligação** que é utilizada para ligar ao arquivo de dados.
5. Clique em **Testar ligação** para testar a ligação ao arquivo de dados.

## <a name="performance-and-tuning"></a>Desempenho e a otimização
Consulte [desempenho de atividade de cópia & otimização guia](data-factory-copy-activity-performance.md) para saber mais sobre fatores determinantes esse desempenho impacto de movimento de dados (atividade de cópia) no Azure Data Factory e várias formas para otimizar o mesmo.
