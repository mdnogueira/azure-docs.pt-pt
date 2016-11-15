O Azure Data Factory suporta as seguintes atividades de transformação, que podem ser adicionadas a pipelines individualmente ou encadeadas com outra atividade.

| Atividade de transformação de dados | Ambiente de computação |
|:--- |:--- |
| [Hive](../articles/data-factory/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) |VM do Azure |
| [Procedimento Armazenado](../articles/data-factory/data-factory-stored-proc-activity.md) |SQL Azure, Azure SQL Data Warehouse ou SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Azure Batch |

> [!NOTE]
> Pode utilizar a atividade MapReduce para executar programas do Spark no seu cluster do HDInsight Spark. Veja [Invoke Spark programs from Azure Data Factory (Invocar programas do Spark a partir do Azure Data Factory)](../articles/data-factory/data-factory-spark.md) para obter detalhes
> Pode criar uma atividade personalizada para executar scripts R no seu cluster do HDInsight com R instalado. Veja [Run R Script using Azure Data Factory (Executar Script R com o Azure Data Factory)](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).
> 
> 



<!--HONumber=Nov16_HO2-->


