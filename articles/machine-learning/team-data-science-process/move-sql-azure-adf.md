---
title: Mover dados de um servidor de SQL no local para o SQL Azure com o Azure Data Factory | Microsoft Docs
description: "Configure um pipeline do ADF que composes duas atividades de migração de dados em conjunto mover dados numa base diária entre bases de dados no local e na nuvem."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 36837c83-2015-48be-b850-c4346aa5ae8a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: bbf969927e96053df055ac6e347bb8fb746054c8
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Mover dados de um servidor SQL no local para o SQL Azure com o Azure Data Factory
Este tópico mostra como mover dados de uma base de dados do servidor de SQL no local para uma base de dados do SQL do Azure através do Blob Storage do Azure com a fábrica de dados do Azure (ADF).

Para uma tabela que resume várias opções para mover dados para uma base de dados do SQL do Azure, consulte [mover dados para uma base de dados do SQL do Azure para o Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Introdução: O que é ADF e quando deve ser utilizada para migrar os dados?
O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerido que orquestra e automatiza o movimento e a transformação de dados. O conceito de chave no modelo de ADF é o pipeline. Um pipeline é um agrupamento lógico das atividades, cada um dos quais define as ações a efetuar nos dados contidos nos conjuntos de dados. Serviços ligados são utilizados para definir as informações necessárias para a fábrica de dados ligar aos recursos de dados.

Com ADF, os serviços de processamento de dados existentes podem ser compostos para pipelines de dados que são altamente disponíveis e geridos na nuvem. Estes pipelines de dados podem ser agendadas para inserir, preparar, transformar, analisar e publicar dados e ADF gere e orquestra os dados complexos e dependências de processamento. Soluções podem ser rapidamente criadas e implementadas na nuvem, ligar um número crescente de no local e na nuvem origens de dados.

Considere a utilização ADF:

* Quando dados tem de ser migrada continuamente no cenário híbrido que acede ao ambos no local e aos recursos na nuvem
* Quando os dados são transacionados ou tem de ser modificados ou ter lógica de negócio adicionada ao mesmo quando a ser migrado.

ADF permite o agendamento e a monitorização de tarefas utilizando scripts simples do JSON que gerem o movimento de dados numa base periódica. ADF também tem outras funcionalidades, tais como o suporte para operações complexas. Para obter mais informações sobre ADF, consulte a documentação em [fábrica de dados do Azure (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>O cenário
Configuramos um pipeline do ADF que composes duas atividades de migração de dados. Em conjunto, mover dados numa base diária entre uma base de dados do SQL Server no local e uma base de dados do SQL do Azure na nuvem. Duas atividades são:

* copiar dados de uma base de dados do SQL Server no local para uma conta do Blob Storage do Azure
* copia dados da conta do Blob Storage do Azure para uma base de dados do SQL do Azure.

> [!NOTE]
> Os passos apresentados aqui foram adaptada o tutorial mais detalhadas fornecidos pela equipa do ADF: [mover dados entre origens no local e nuvem com o Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md) referências para as secções relevantes desse tópico são fornecidas quando for adequado.
>
>

## <a name="prereqs"></a>Pré-requisitos
Este tutorial parte do princípio de que tem:

* Um **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um **conta do storage do Azure**. Utilize uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Depois de ter criado a conta de armazenamento, tem de obter a chave de conta utilizada para aceder ao armazenamento. Consulte [gerir as chaves de acesso de armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Acesso a um **base de dados SQL do Azure**. Se tem de configurar uma SQL Database do Azure, o tópico [introdução à base de dados do Microsoft Azure SQL ](../../sql-database/sql-database-get-started.md) fornece informações sobre como aprovisionar uma nova instância de uma base de dados do SQL do Azure.
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Este procedimento utiliza a [portal do Azure](https://portal.azure.com/).
>
>

## <a name="upload-data"></a>Carregar os dados para o SQL Server no local
Utilizamos o [NYC Taxi dataset](http://chriswhong.com/open-data/foil_nyc_taxi/) para demonstrar o processo de migração. O conjunto de dados NYC Taxi está disponível, conforme indicado nesse post, no armazenamento de Blobs do Azure [NYC Taxi dados](http://www.andresmh.com/nyctaxitrips/). Os dados têm dois ficheiros, o trip_data.csv, que contém detalhes viagem, e o ficheiro de trip_far.csv, que contém detalhes de fare paga para cada viagem. Um exemplo e uma descrição destes ficheiros são fornecidos na [NYC Taxi viagens Dataset Descrição](sql-walkthrough.md#dataset).

Pode adaptar o procedimento indicado aqui a um conjunto de dados da sua própria ou siga os passos, conforme descrito utilizando o conjunto de dados NYC Taxi. Para carregar o conjunto de dados NYC Taxi para a base de dados do SQL Server no local, siga o procedimento descrito no [em massa importar dados na base de dados do SQL Server](sql-walkthrough.md#dbload). Estas instruções se destinam a um SQL Server uma Máquina Virtual no Azure, mas o procedimento para carregar para o SQL Server no local é o mesmo.

## <a name="create-adf"></a>Criar um Azure Data Factory
As instruções para criar uma nova fábrica de dados do Azure e um grupo de recursos a [portal do Azure](https://portal.azure.com/) são fornecidos [criar um Azure Data Factory](../../data-factory/v1/data-factory-build-your-first-pipeline-using-editor.md#create-data-factory). Nome a nova ocorrência do ADF *adfdsp* e atribua o nome do grupo de recursos criado *adfdsprg*.

## <a name="install-and-configure-up-the-data-management-gateway"></a>Instalar e configurar o Data Management Gateway
Para ativar os seus pipelines de um Azure data factory para trabalhar com um servidor de SQL no local, terá de adicioná-lo como um serviço ligado a uma fábrica de dados. Para criar um serviço ligado para um servidor de SQL no local, tem de:

* Transfira e instale o Microsoft Data Management Gateway no computador local.
* Configure o serviço ligado para a origem de dados no local utilizar o gateway.

O Data Management Gateway serializa e deserializes os dados de origem e dependente no computador onde está alojado.

Para instruções de configuração e detalhes sobre o Data Management Gateway, consulte [mover dados entre origens no local e nuvem com o Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md)

## <a name="adflinkedservices"></a>Criar serviços ligados para ligar aos recursos de dados
Um serviço ligado define as informações necessárias para ligar a um recurso de dados do Azure Data Factory. Temos três recursos neste cenário para o qual são necessários serviços ligados:

1. SQL Server no local
2. Armazenamento de Blobs do Azure
3. Base de dados SQL do Azure

O procedimento passo a passo para criar serviços ligados é fornecido no [criar serviços ligados](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-linked-services).


## <a name="adf-tables"></a>Definir e criar tabelas para especificar como os conjuntos de dados de acesso
Crie tabelas que especifiquem a estrutura, a localização e a disponibilidade dos conjuntos de dados com os seguintes procedimentos de acesso baseado em script. Ficheiros JSON são utilizados para definir as tabelas. Para obter mais informações sobre a estrutura destes ficheiros, consulte [conjuntos de dados](../../data-factory/v1/data-factory-create-datasets.md).

> [!NOTE]
> Deve executar o `Add-AzureAccount` cmdlet antes de executar o [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet para confirmar se a subscrição à direita do Azure está selecionada para a execução do comando. Para obter documentação deste cmdlet, consulte [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

As definições baseadas em JSON nas tabelas utilizem os nomes seguintes:

* o **nome da tabela** no local no SQL server se *nyctaxi_data*
* o **nome do contentor** no armazenamento de Blobs do Azure é a conta *containername*  

São necessários três definições de tabela para este pipeline ADF:

1. [Tabela do SQL Server no local](#adf-table-onprem-sql)
2. [Tabela de BLOBs](#adf-table-blob-store)
3. [SQL Server tabela do Azure](#adf-table-azure-sql)

> [!NOTE]
> Estes procedimentos utilizam o Azure PowerShell para definir e criar as atividades ADF. Mas estas tarefas também podem ser realizadas através do portal do Azure. Para obter mais informações, consulte [criar conjuntos de dados](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-datasets).
>
>

### <a name="adf-table-onprem-sql"></a>Tabela do SQL Server no local
A definição de tabela para o SQL Server no local é especificada no seguinte ficheiro JSON:

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

Os nomes das colunas não estavam incluídas aqui. Pode selecionar subplano nos nomes das colunas, incluindo-las aqui (para obter detalhes, verifique o [documentação ADF](../../data-factory/v1/data-factory-data-movement-activities.md) tópico.

Copiar a definição de JSON da tabela num ficheiro denominada *onpremtabledef.json* do ficheiro e guarde-a numa localização conhecida (aqui pressupõe-se que *C:\temp\onpremtabledef.json*). Crie a tabela no ADF com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Tabela de BLOBs
A definição da tabela para a localização de blob de saída é a seguir (mapeia os dados de transmissões no local ao blob do Azure):

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

Copiar a definição de JSON da tabela num ficheiro denominada *bloboutputtabledef.json* do ficheiro e guarde-a numa localização conhecida (aqui pressupõe-se que *C:\temp\bloboutputtabledef.json*). Crie a tabela no ADF com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

### <a name="adf-table-azure-sq"></a>SQL Server tabela do Azure
Definição da tabela para o SQL Azure de saída é a seguir (este esquema mapeia dos dados provenientes das blob):

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

Copiar a definição de JSON da tabela num ficheiro denominada *AzureSqlTable.json* do ficheiro e guarde-a numa localização conhecida (aqui pressupõe-se que *C:\temp\AzureSqlTable.json*). Crie a tabela no ADF com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


## <a name="adf-pipeline"></a>Definir e criar o pipeline
Especifique as atividades que pertencem ao pipeline e criar o pipeline com os seguintes procedimentos de acesso baseado em script. Um ficheiro JSON é utilizado para definir as propriedades do pipeline.

* O script parte do princípio de que o **nome pipeline** é *AMLDSProcessPipeline*.
* Note também que definimos a periodicidade do pipeline para ser executado numa base diária e utilizar o tempo de execução predefinido para a tarefa (UTC-12: 00).

> [!NOTE]
> Os procedimentos seguintes utilizam o Azure PowerShell para definir e criar o pipeline ADF. Mas também pode ser realizada esta tarefa no portal do Azure. Para obter mais informações, consulte [criar pipeline](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-pipeline).
>
>

Utilizar as definições de tabela fornecidas anteriormente, a definição de pipeline para o ADF é especificada da seguinte forma:

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premises SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",                
                            }            
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

Copiar esta definição JSON do pipeline de para um ficheiro chamada *pipelinedef.json* do ficheiro e guarde-a numa localização conhecida (aqui pressupõe-se que *C:\temp\pipelinedef.json*). Crie o pipeline ADF com o seguinte cmdlet do PowerShell do Azure:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Iniciar o Pipeline
O pipeline agora pode ser executado utilizando o seguinte comando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

O *startdate* e *enddate* os valores dos parâmetros têm de ser substituído com as datas reais entre os quais pretende que o pipeline para executar.

Assim que executa o pipeline, deve conseguir ver os dados apareçam no contentor selecionado para o blob, um ficheiro por dia.

Tenha em atenção que não podemos ter aproveitadas a funcionalidade fornecida pelo ADF dados de pipe de forma incremental. Para obter mais informações sobre como efetuar esta e outras funcionalidades fornecidas pelo ADF, consulte o [documentação ADF](https://azure.microsoft.com/services/data-factory/).
