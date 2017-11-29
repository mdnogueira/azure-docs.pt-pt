---
title: Analisar os registos de Web site com bibliotecas de Python no Spark - Azure | Microsoft Docs
description: Este bloco de notas demonstra como analisar dados de registo utilizando uma biblioteca personalizada com o Spark no Azure HDInsight.
services: hdinsight
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 8c61c70f-fe7f-4f0f-a4ab-0cccee5668c9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 9f733e8b0c21b25b212266bb6047416cfb29d0ad
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-spark-cluster-on-hdinsight"></a>Analisar os registos de Web site utilizando uma biblioteca de Python personalizada com um cluster do Spark no HDInsight

Este bloco de notas demonstra como analisar dados de registo utilizando uma biblioteca personalizada com o Spark no HDInsight. A biblioteca personalizada utilizamos é uma biblioteca de Python chamada **iislogparser.py**.

> [!TIP]
> Este tutorial também está disponível como um bloco de notas do Jupyter num cluster do Spark (Linux) que criar no HDInsight. A experiência de bloco de notas permite-lhe executar os fragmentos de Python do bloco de notas. Para efetuar o tutorial do dentro de um bloco de notas, criar um cluster do Spark, inicie um bloco de notas do Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), e, em seguida, execute o bloco de notas **analisar os registos com o Spark utilizando um library.ipynb personalizado** sob o **PySpark**  pasta.
>
>

**Pré-requisitos:**

Tem de ter o seguinte:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Guardar dados não processados como um RDD
Nesta secção, vamos utilizar o [Jupyter](https://jupyter.org) associado um cluster do Apache Spark no HDInsight para executar tarefas que processar os dados de exemplo não processados e guarde-o como uma tabela do Hive do bloco de notas. Os dados de exemplo são um ficheiro. csv (hvac.csv) disponível em todos os clusters por predefinição.

Assim que os seus dados são guardados como uma tabela do Hive, na secção seguinte, irá ligar para a tabela de Hive através de ferramentas de BI como o Power BI e o Tableau.

1. No [portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.   
2. No painel do cluster do Spark, clique em **Dashboard de Clusters** e, em seguida, clique em **Bloco de Notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.

   > [!NOTE]
   > Também pode aceder ao Bloco de Notas do Jupyter para o cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um novo bloco de notas. Clique em **Novo** e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de notas Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Criar um novo bloco de notas Jupyter")
4. É criado e aberto um novo bloco de notas com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Forneça um nome para o bloco de notas](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Forneça um nome para o bloco de notas")
5. Uma vez que criou um bloco de notas com o kernel do PySpark, não é necessário criar quaisquer contextos explicitamente. Os contextos do Spark e do Hive serão criados automaticamente quando executa a primeira célula do código. Pode começar por importar os tipos que são necessários para este cenário. Cole o seguinte fragmento numa célula vazia e, em seguida, prima **SHIFT + ENTER**.

        from pyspark.sql import Row
        from pyspark.sql.types import *


1. Crie um RDD utilizando os dados de registo de exemplo já disponíveis no cluster. Pode aceder os dados na conta do storage predefinida associada ao cluster **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


1. Obter um registo de exemplo definido para verificar que o passo anterior foi concluído com êxito.

        logs.take(5)

    Deverá ver um resultado semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analisar dados de registo utilizando uma biblioteca Python personalizada
1. No resultado acima, as linhas de alguns primeiro incluem as informações de cabeçalho e o esquema descrito no cabeçalho de que corresponde de cada linha restantes. Analisar esses registos pode ser complicado. Por isso, vamos utilizar uma biblioteca Python personalizada (**iislogparser.py**) que faz com que esses registos muito mais fácil de análise. Por predefinição, esta biblioteca é incluída no cluster do Spark no HDInsight em **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    No entanto, esta biblioteca não está no `PYTHONPATH` pelo que não é possível utilizá-la utilizando uma instrução de importação como `import iislogparser`. Para utilizar esta biblioteca, iremos tem de distribui-la para todos os nós de trabalho. Execute o seguinte fragmento.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


1. `iislogparser`Fornece uma função `parse_log_line` que devolve `None` se uma linha de registo de uma linha de cabeçalho e devolve uma instância do `LogLine` classe se encontrar uma linha de registo. Utilize o `LogLine` classe para extrair o RDD apenas as linhas de registo:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
2. Obter alguns extraídas linhas de registo para verificar se o passo foi concluído com êxito.

       logLines.take(2)

   O resultado deve ser semelhante ao seguinte:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
3. O `LogLine` classe, por sua vez, tem alguns métodos úteis, como `is_error()`, que devolve se uma entrada de registo tem um código de erro. Utilize esta opção para calcular o número de erros nas linhas de registo extraídos e, em seguida, inicie sessão todos os erros para um ficheiro diferente.

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   Deve ver um resultado como o seguinte:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
4. Também pode utilizar **Matplotlib** para construir uma visualização dos dados. Por exemplo, se pretender isolar a causa de pedidos que são executadas durante muito tempo, pode querer localizar os ficheiros que demoram mais tempo para servir em média.
   O fragmento abaixo obtém os recursos de 25 superiores que demoraram mais a servir um pedido.

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   Deve ver um resultado como o seguinte:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [(u'/blogposts/mvc4/step13.png', 197.5),
        (u'/blogposts/mvc2/step10.jpg', 179.5),
        (u'/blogposts/extractusercontrol/step5.png', 170.0),
        (u'/blogposts/mvc4/step8.png', 159.0),
        (u'/blogposts/mvcrouting/step22.jpg', 155.0),
        (u'/blogposts/mvcrouting/step3.jpg', 152.0),
        (u'/blogposts/linqsproc1/step16.jpg', 138.75),
        (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
        (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
        (u'/blogposts/nested/step2.jpg', 126.0),
        (u'/blogposts/adminpack/step1.png', 124.0),
        (u'/BlogPosts/datalistpaging/step2.png', 118.0),
        (u'/blogposts/mvc4/step35.png', 117.0),
        (u'/blogposts/mvcrouting/step2.jpg', 116.5),
        (u'/blogposts/aboutme/basketball.jpg', 109.0),
        (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
        (u'/blogposts/mvc4/step12.png', 106.0),
        (u'/blogposts/linq8/step0.jpg', 105.5),
        (u'/blogposts/mvc2/step18.jpg', 104.0),
        (u'/blogposts/mvc2/step11.jpg', 104.0),
        (u'/blogposts/mvcrouting/step1.jpg', 104.0),
        (u'/blogposts/extractusercontrol/step1.png', 103.0),
        (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
        (u'/blogposts/mvcrouting/step21.jpg', 101.0),
        (u'/blogposts/mvc4/step1.png', 98.0)]
5. Também pode apresentar esta informação sob a forma de desenho. Como primeiro passo para criar um desenho, informe-nos primeiro de criar uma tabela temporária **AverageTime**. A tabela agrupa os registos do tempo para ver se ocorreram quaisquer picos de latência invulgares em qualquer altura específica.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
6. Em seguida, pode executar a seguinte consulta SQL para obter todos os registos do **AverageTime** tabela.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   O `%%sql` magic seguido `-o averagetime` assegura que o resultado da consulta é persistente localmente no servidor do Jupyter (normalmente o headnode do cluster). O resultado é persistente como um [Pandas](http://pandas.pydata.org/) dataframe com o nome especificado **averagetime**.

   Deve ver um resultado como o seguinte:

   ![Resultado da consulta SQL](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "resultado de consulta SQL")

   Para obter mais informações sobre o `%%sql` mágica, consulte [parâmetros suportados com o % % sql magic](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
7. Agora, pode utilizar Matplotlib, uma biblioteca utilizada para construir a visualização de dados, para criar um desenho. Porque o desenho tem de ser criado de localmente persistente **averagetime** dataframe, o fragmento de código tem de começar a `%%local` magic. Isto garante que o código é executado localmente no servidor do Jupyter.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   Deve ver um resultado como o seguinte:

   ![Saída de Matplotlib](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib saída")
8. Depois de terminar a execução da aplicação, deve encerrar o bloco de notas para libertar os recursos. Para o fazer, no menu **Ficheiro** do bloco de notas, clique em **Fechar e Parar**. Deste modo, o bloco de notas é encerrado e fechado.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](../hdinsight-apache-spark-eventhub-streaming.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
