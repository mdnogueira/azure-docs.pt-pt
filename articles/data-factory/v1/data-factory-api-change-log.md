---
title: "Fábrica de dados - registo de alterações de API do .NET | Microsoft Docs"
description: "Descreve as alterações, adições de funcionalidade, correções de erros etc... uma versão específica do .NET API do Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 92623b504341572e1b6fae18ba612bcab20162f1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-data-factory---net-api-change-log"></a>O Azure Data Factory - registo de alterações de .NET API
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). 

Este artigo fornece informações sobre as alterações ao SDK do Azure Data Factory uma versão específica. Pode encontrar o pacote NuGet mais recente do Azure Data Factory [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Versão 4.11.0
Funcionalidade adições:

* Foram adicionados os seguintes tipos de serviço ligado:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Foram adicionados os seguintes tipos de origem de cópia:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Versão 4.10.0
* Foram adicionadas as seguintes propriedades opcionais para TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Foram adicionados os seguintes tipos de serviço ligado:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Foram adicionados os seguintes tipos de origem de cópia:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Adicionar [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) propriedade AzureMLBatchExecutionActivity
  * Ativar a transmitir web várias entradas de serviço para uma experimentação do Azure Machine Learning

## <a name="version-491"></a>Versão 4.9.1
### <a name="bug-fix"></a>Correção de erro
* Preterir a autenticação baseada em End WebApi para [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versão 4.9.0
### <a name="feature-additions"></a>Adições de funcionalidade
* Adicionar [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) e [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) propriedades CopyActivity. Consulte [testado cópia](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre a funcionalidade.

### <a name="bug-fix"></a>Correção de erro
* Introduzir uma sobrecarga de [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) método, que demora um [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) instância.
* Marca [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) como opcional na CopySink.

## <a name="version-480"></a>Versão 4.8.0
### <a name="feature-additions"></a>Adições de funcionalidade
* Foram adicionadas as seguintes propriedades opcionais para o tipo de atividade de cópia para ativar a otimização do desempenho de cópia:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versão 4.7.0
### <a name="feature-additions"></a>Adições de funcionalidade
* Adicionar novo tipo de StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) tipo copiar os ficheiros no formato de (ORC) columnar linha otimizada.
* Adicionar [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e propriedades de PolyBaseSettings SqlDWSink.
  * Permite a utilização do PolyBase para copiar dados para o SQL Data Warehouse.

## <a name="version-461"></a>Versão 4.6.1
### <a name="bug-fixes"></a>Correções de erros
* Correções de pedido HTTP para listar windows de atividade.
  * Remove o payload de pedido o nome do grupo de recursos e o nome de factory de dados.

## <a name="version-460"></a>Versão 4.6.0
### <a name="feature-additions"></a>Adições de funcionalidade
* Foram adicionadas as seguintes propriedades para [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Conjuntos de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Foram adicionadas as seguintes propriedades para [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Adicionar novo [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) tipo [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) tipo para definir os conjuntos de dados cujos dados estão no formato JSON.

## <a name="version-450"></a>Versão 4.5.0
### <a name="feature-additions"></a>Adições de funcionalidade
* Adicionar [listar operações para a janela de atividade](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Foram adicionados métodos para obter o windows de atividade com filtros de com base nos tipos de entidade (ou seja, fábricas de dados, os conjuntos de dados, pipelines e atividades).
* Foram adicionados os seguintes tipos de serviço ligado:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Foram adicionados os seguintes tipos de origem de cópia:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versão 4.4.0
### <a name="feature-additions"></a>Adições de funcionalidade
* O seguinte tipo de serviço ligado foi adicionado como origens de dados e sinks para atividades de cópia:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consulte [serviço ligado do Azure Storage SAS](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter exemplos e informações conceptuais.

## <a name="version-430"></a>Versão 4.3.0
### <a name="feature-additions"></a>Adições de funcionalidade
* A seguinte haven de tipos de serviço ligado foi adicionado como origens de dados para atividades de cópia:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Consulte [mover dados do HDFS utilizando o Data Factory](data-factory-hdfs-connector.md) para obter exemplos e informações conceptuais.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Consulte [mover dados de ODBC os arquivos de dados utilizando o Azure Data Factory](data-factory-odbc-connector.md) para obter exemplos e informações conceptuais.

## <a name="version-420"></a>Versão 4.2.0
### <a name="feature-additions"></a>Adições de funcionalidade
* O novo tipo de atividade seguinte foi adicionado: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obter detalhes sobre a atividade, consulte [modelos do Azure ML/atualização utilizando a atividade de recursos de atualização](data-factory-azure-ml-batch-execution-activity.md).
* Uma nova propriedade opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) foi adicionado para o [AzureMLLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) propriedades foram adicionadas para o [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) classe.
* Permitir a configuração de tempos limite de para chamadas de cliente para o serviço fábrica de dados.

## <a name="version-410"></a>Versão 4.1.0
### <a name="feature-additions"></a>Adições de funcionalidade
* Foram adicionados os seguintes tipos de serviço ligado:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Foram adicionados os seguintes tipos de atividade:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Foram adicionados os seguintes tipos de conjunto de dados:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Foram adicionados os seguintes tipos de origem e dependente para a atividade de cópia:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Versão 4.0.1
### <a name="breaking-changes"></a>As alterações de última hora
As seguintes classes de mudança de nome. Os nomes dos novos foram os nomes das classes originais antes da versão 4.0.0.

| Nome no 4.0.0 | Nome no 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Versão 4.0.0
### <a name="breaking-changes"></a>As alterações de última hora
* As seguintes classes/interfaces de mudança de nome.

| Nome antigo | Novo nome |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabela |[Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* O **lista** métodos devolvem resultados paginados agora. Se a resposta contém um vazios **NextLink** propriedade, tem da aplicação de cliente continuar a obter a página seguinte, até que todas as páginas são devolvidas.  Segue-se um exemplo:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **Lista** pipeline API devolve apenas o resumo de um pipeline, em vez de detalhes completos. Por exemplo, as atividades num resumo de pipeline só contenham nome e tipo.

### <a name="feature-additions"></a>Adições de funcionalidade
* O [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) classe suporta duas novas propriedades **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, para suportar idempotent copiar para o Azure SQL Data Warehouse. Consulte o [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) artigo para obter detalhes sobre estas propriedades.
* Agora suportamos a executar o procedimento armazenado em relação a SQL Database do Azure e Azure SQL Data Warehouse origens como parte da atividade de cópia. O [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) as classes têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Consulte o [SQL Database do Azure](data-factory-azure-sql-connector.md#sqlsource) e [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) artigos no Azure.com para obter detalhes sobre estas propriedades.  
