# Descrição geral
## [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)
## [Comparar o Azure Data Lake Store com o Armazenamento do Azure](data-lake-store-comparison-with-blob-storage.md)
## [Azure Data Lake Store para o processamento de macrodados](data-lake-store-data-scenarios.md)
## [Abrir aplicações de origem que funcionem com o Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)

# Introdução
## [Utilizar o Portal](data-lake-store-get-started-portal.md)
## [Com o PowerShell](data-lake-store-get-started-powershell.md)
## [Utilizar a CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)


# Procedimento
## Carregar e mover dados
### [Utilizar o Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md)
### [Utilizar o AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [Utilizar o DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [Utilizar o Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [Carregar dados a partir de origens offline](data-lake-store-offline-bulk-data-upload.md)
### [Migrar o Azure Data Lake Store entre regiões](data-lake-store-migration-cross-region.md)

## Proteger dados
### [Descrição geral da segurança](data-lake-store-security-overview.md)
### [Controlo de acesso no Data Lake Store](data-lake-store-access-control.md)
### [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)
### [Encriptação](data-lake-store-encryption.md)

## Autenticar com o Data Lake Store
### [Opções de autenticação](data-lakes-store-authentication-using-azure-active-directory.md)
### [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
#### [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
#### [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
#### [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
### [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
#### [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)

## Trabalhar com o Data Lake Store
### Operações de gestão de conta
#### [Com o .NET SDK](data-lake-store-get-started-net-sdk.md)
#### [Utilizar a API REST](data-lake-store-get-started-rest-api.md)
#### [Utilizar o Python](data-lake-store-get-started-python.md)
### Operações de sistema de ficheiros
#### [Com o .NET SDK](data-lake-store-data-operations-net-sdk.md)
#### [Utilizar o .Java SDK](data-lake-store-get-started-java-sdk.md)
#### [Utilizar a API REST](data-lake-store-data-operations-rest-api.md)
#### [Utilizar o Python](data-lake-store-data-operations-python.md)

## Desempenho
### [Guia de sintonização de desempenho para o Azure Data Lake Store](data-lake-store-performance-tuning-guidance.md)
### [Guia de sintonização de desempenho para o PowerShell com o Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
### [Guia de sintonização de desempenho para o Spark no HDInsight e Azure Data Lake Store](data-lake-store-performance-tuning-spark.md)
### [Guia de sintonização de desempenho para o Hive no HDInsight e Azure Data Lake Store](data-lake-store-performance-tuning-hive.md)
### [Guia de sintonização de desempenho para o MapReduce no HDInsight e Azure Data Lake Store](data-lake-store-performance-tuning-mapreduce.md)
### [Guia de sintonização de desempenho para o Storm no HDInsight e Azure Data Lake Store](data-lake-store-performance-tuning-storm.md)

## Integrar nos Serviços do Azure
### Com o HDInsight
#### [Com o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Utilizar o Azure PowerShell (armazenamento predefinido)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Utilizar o Azure PowerShell (armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Utilizar o modelo do Azure](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Acesso a partir de VMs na VNET do Azure](data-lake-store-connectivity-from-vnets.md)
### [Utilizar com o Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Utilizar com os Hubs de Eventos do Azure](data-lake-store-archive-eventhub-capture.md)
### [Utilizar com o Data Factory](../data-factory/connector-azure-data-lake-store.md)
### [Utilizar com o Stream Analytics](data-lake-store-stream-analytics.md)
### [Utilizar com o Power BI](data-lake-store-power-bi.md)
### [Utilizar com o Catálogo de Dados](data-lake-store-with-data-catalog.md)
### [Utilizar com o PolyBase no SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [Utilizar com os SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [Mais opções de integração do Azure](data-lake-store-integrate-with-other-services.md)

## Gerir
### [Aceder aos registos de diagnósticos](data-lake-store-diagnostic-logs.md)
### [Plano para elevada disponibilidade](data-lake-store-disaster-recovery-guidance.md)

# Referência
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (Gestão de Conta)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
## [Python (Gestão do Sistema de Ficheiros)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [CLI do Azure](https://docs.microsoft.com/cli/azure/dls)

# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/)
## [Blogue do Data Lake Store](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Enviar comentários sobre o UserVoice](https://feedback.azure.com/forums/327234-data-lake)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Preços](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Fórum do Stack Overflow](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
