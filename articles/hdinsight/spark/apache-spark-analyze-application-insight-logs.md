---
title: Analisar os registos de Application Insight com o Spark - Azure HDInsight | Microsoft Docs
description: Saiba como exportar os registos do Application Insight para armazenamento de BLOBs e, em seguida, analise os registos com o Spark no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: larryfr
ms.openlocfilehash: d7e4e064a451831c7cf0753064522d0a0b4f5d63
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>Analisar os registos de telemetria do Application Insights com o Spark no HDInsight

Saiba como utilizar o Spark no HDInsight para analisar dados de telemetria do Application Insight.

[Visual Studio Application Insights](../../application-insights/app-insights-overview.md) é um serviço de análise que monitoriza as suas aplicações web. Dados de telemetria gerados pelo Application Insights podem ser exportados para o Storage do Azure. Depois dos dados no armazenamento do Azure, o HDInsight pode ser utilizado para analisá-lo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação que está configurada para utilizar o Application Insights.

* Familiaridade com a criação de um cluster do HDInsight baseado em Linux. Para obter mais informações, consulte [criar Spark no HDInsight](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]
  > Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um web browser.

Os seguintes recursos foram utilizados no desenvolver e testar este documento:

* Dados de telemetria do Application Insights foi gerados utilizando um [aplicação web de Node.js configurada para utilizar o Application Insights](../../application-insights/app-insights-nodejs.md).

* Foi utilizado um Spark baseado em Linux no clusters do HDInsight versão 3.5 para analisar os dados.

## <a name="architecture-and-planning"></a>Planeamento e arquitetura

O diagrama seguinte ilustra a arquitetura de serviço deste exemplo:

![diagrama que mostra os dados que fluem do Application Insights para o blob storage, em seguida, o que está a ser processados pelo Spark no HDInsight](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Storage do Azure

Application Insights podem ser configurados para continuamente exportar informações de telemetria para blobs. HDInsight, em seguida, pode ler os dados armazenados em blobs. No entanto, existem alguns requisitos que tem de seguir:

* **Localização**: se a conta de armazenamento e o HDInsight em diferentes localizações, pode aumentar o latência. Também aumenta custo, como os encargos são aplicados ao mover entre regiões de dados de saída.

    > [!WARNING]
    > Não é suportada a utilização de uma conta de armazenamento numa localização diferente ao HDInsight.

* **Tipo de blob**: HDInsight suporta apenas blobs de blocos. Aplicação Insights predefinições para a utilização de blobs de blocos, por isso, deverão funcionar por predefinição com o HDInsight.

Para obter informações sobre como adicionar armazenamento a um cluster existente, consulte o [adicionar mais contas do storage](../hdinsight-hadoop-add-storage.md) documento.

### <a name="data-schema"></a>Esquema de dados

Application Insights fornece [exportar o modelo de dados](../../application-insights/app-insights-export-data-model.md) informações para o formato de dados de telemetria exportadas para o blobs. Os passos neste documento utilizam Spark SQL para trabalhar com os dados. O Spark SQL pode gerar automaticamente um esquema para a estrutura de dados JSON registada pelo Application Insights.

## <a name="export-telemetry-data"></a>Exportar dados de telemetria

Siga os passos no [configurar a exportação contínua](../../application-insights/app-insights-export-telemetry.md) para configurar o Application Insights para exportar as informações de telemetria para um blob de armazenamento do Azure.

## <a name="configure-hdinsight-to-access-the-data"></a>Configurar o HDInsight para aceder aos dados

Se estiver a criar um cluster do HDInsight, adicione a conta de armazenamento durante a criação do cluster.

Para adicionar a conta de armazenamento do Azure para um cluster existente, utilize as informações de [adicionar mais contas de armazenamento](../hdinsight-hadoop-add-storage.md) documento.

## <a name="analyze-the-data-pyspark"></a>Analisar os dados: PySpark

1. Do [portal do Azure](https://portal.azure.com), selecione o Spark no HDInsight cluster. Do **ligações rápidas** secção, selecione **Dashboards de clusters**e, em seguida, selecione **bloco de notas do Jupyter** da secção Dashboard__ de Cluster.

    ![Os dashboards de clusters](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. No canto superior direito da página Jupyter, selecione **novo**e, em seguida, **PySpark**. É aberto um novo separador do browser que contém um bloco de notas do Jupyter com base no Python.

3. No campo do primeiro (chamado um **célula**) na página, introduza o seguinte texto:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Este código configura Spark para o acesso de modo recursivo a estrutura de diretório para os dados de entrada. Telemetria do Application Insights é registada para uma estrutura de diretórios semelhante para o `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Utilize **SHIFT + ENTER** para executar o código. No lado esquerdo da célula, um '\*' aparece entre Retos para indicar que está a ser executado o código nesta célula. Depois de terminar, o '\*' alterações a um número e o resultado semelhante para o seguinte texto é apresentado abaixo célula:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. É criada uma nova célula abaixo primeiro. Introduza o seguinte texto na célula novo. Substitua `CONTAINER` e `STORAGEACCOUNT` com o nome de conta de armazenamento do Azure e o nome de contentor do blob que contém os dados do Application Insights.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Utilize **SHIFT + ENTER** para executar esta célula. Ver um resultado semelhante ao seguinte texto:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    O caminho de wasb devolvido é a localização dos dados de telemetria do Application Insights. Alterar o `hdfs dfs -ls` linha na célula para utilizar o caminho de wasb devolvido e, em seguida, utilize **SHIFT + ENTER** executar novamente a célula. Neste momento, os resultados deverá apresentar os diretórios que contêm dados de telemetria.

   > [!NOTE]
   > Para o resto dos passos nesta secção, o `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` diretório foi utilizado. A estrutura de diretório pode ser diferente.

6. A célula seguinte, introduza o seguinte código: substituir `WASB_PATH` com o caminho do passo anterior.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Este código cria um dataframe a partir de ficheiros JSON exportados pelo processo de exportação contínua. Utilize **SHIFT + ENTER** para executar esta célula.
7. A célula seguinte, introduza e execute o seguinte para ver o esquema que Spark criado para os ficheiros JSON:

   ```python
   jsonData.printSchema()
   ```

    O esquema para cada tipo de telemetria é diferente. O exemplo seguinte é o esquema que é gerado para pedidos web (dados armazenados no `Requests` subdiretório):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)
8. Utilize o seguinte para registar o dataframe como uma tabela temporária e executar uma consulta de dados:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Esta consulta devolve as informações de cidade dos principais 20 registos onde context.location.city não seja nulo.

   > [!NOTE]
   > A estrutura de contexto está presente em toda a telemetria registada pelo Application Insights. O elemento de cidade não pode ser preenchido nos seus registos. Utilize o esquema para identificar outros elementos que pode consultar que poderão conter dados para os seus registos.

    Esta consulta devolve informações semelhantes para o seguinte texto:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Analisar os dados: Scala

1. Do [portal do Azure](https://portal.azure.com), selecione o Spark no HDInsight cluster. Do **ligações rápidas** secção, selecione **Dashboards de clusters**e, em seguida, selecione **bloco de notas do Jupyter** da secção Dashboard__ de Cluster.

    ![Os dashboards de clusters](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. No canto superior direito da página Jupyter, selecione **novo**e, em seguida, **Scala**. É apresentado um novo separador do browser que contém um bloco de notas do Jupyter com base no Scala.
3. No campo do primeiro (chamado um **célula**) na página, introduza o seguinte texto:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Este código configura Spark para o acesso de modo recursivo a estrutura de diretório para os dados de entrada. Telemetria do Application Insights é registada para uma estrutura de diretório semelhante à `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Utilize **SHIFT + ENTER** para executar o código. No lado esquerdo da célula, um '\*' aparece entre Retos para indicar que está a ser executado o código nesta célula. Depois de terminar, o '\*' alterações a um número e o resultado semelhante para o seguinte texto é apresentado abaixo célula:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. É criada uma nova célula abaixo primeiro. Introduza o seguinte texto na célula novo. Substitua `CONTAINER` e `STORAGEACCOUNT` com o nome de conta de armazenamento do Azure e o nome de contentor do blob que contém o Application Insights registos.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Utilize **SHIFT + ENTER** para executar esta célula. Ver um resultado semelhante ao seguinte texto:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    O caminho de wasb devolvido é a localização dos dados de telemetria do Application Insights. Alterar o `hdfs dfs -ls` linha na célula para utilizar o caminho de wasb devolvido e, em seguida, utilize **SHIFT + ENTER** executar novamente a célula. Neste momento, os resultados deverá apresentar os diretórios que contêm dados de telemetria.

   > [!NOTE]
   > Para o resto dos passos nesta secção, o `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` diretório foi utilizado. Este diretório pode não existir, a menos que os dados de telemetria para uma aplicação web.

6. A célula seguinte, introduza o seguinte código: substituir `WASB\_PATH` com o caminho do passo anterior.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Este código cria um dataframe a partir de ficheiros JSON exportados pelo processo de exportação contínua. Utilize **SHIFT + ENTER** para executar esta célula.

7. A célula seguinte, introduza e execute o seguinte para ver o esquema que Spark criado para os ficheiros JSON:

   ```scala
   jsonData.printSchema
   ```

    O esquema para cada tipo de telemetria é diferente. O exemplo seguinte é o esquema que é gerado para pedidos web (dados armazenados no `Requests` subdiretório):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Utilize o seguinte para registar o dataframe como uma tabela temporária e executar uma consulta de dados:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Esta consulta devolve as informações de cidade dos principais 20 registos onde context.location.city não seja nulo.

   > [!NOTE]
   > A estrutura de contexto está presente em toda a telemetria registada pelo Application Insights. O elemento de cidade não pode ser preenchido nos seus registos. Utilize o esquema para identificar outros elementos que pode consultar que poderão conter dados para os seus registos.
   >
   >

    Esta consulta devolve informações semelhantes para o seguinte texto:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Passos seguintes

Para obter mais exemplos de utilização do Spark para trabalhar com dados e serviços no Azure, consulte os seguintes documentos:

* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em fluxo do Spark: Utilizar o Spark no HDInsight para compilar aplicações de transmissão em fluxo](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

Para obter informações sobre como criar e executar Spark aplicações, consulte os seguintes documentos:

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)
