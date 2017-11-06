
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

O Azure Data Factory é um serviço de integração de dados. Permite-lhe criar fluxos de trabalho condicionados por dados na cloud. Um fluxo de trabalho é implementado como um ou mais *pipelines*. Os pipelines orquestram e automatizam o movimento de dados e a transformação de dados. Os pipelines podem realizar a seguinte sequência com os seus dados:

1. Ingestão de dados a partir de arquivos de dados diferentes.
2. Transforme ou processe os dados com serviços de computação, como os seguintes:
    - Hadoop do Azure HDInsight
    - Spark
    - Azure Data Lake Analytics
    - Azure Machine Learning
3. Publica os dados de saída em arquivos de dados.
    - O destino de publicação poderia ser um Azure SQL Data Warehouse para consumo por aplicações de business intelligence (BI). 

Pode agendar pipelines com o Data Factory.

