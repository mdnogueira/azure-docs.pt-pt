---
title: Gerir os recursos de cluster do Apache Spark no Azure HDInsight | Microsoft Docs
description: Saiba como utilizar Gerir os recursos de clusters do Spark no Azure HDInsight para um melhor desempenho.
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: jgao
ms.openlocfilehash: 3c98150239134c686ac8edebd3c477bec8be7dd8
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gerir os recursos de cluster do Apache Spark no Azure HDInsight 

Este artigo irá aprender a aceder às interfaces, como Ambari IU, IU do YARN, e o servidor de histórico do Spark associadas com o cluster do Spark. Pode também saber mais sobre como otimizar a configuração de cluster para um desempenho ideal.

**Pré-requisitos:**

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-launch-the-ambari-web-ui"></a>Como iniciar a IU da Web do Ambari?
1. No [portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard). Também pode navegar até ao cluster em **Procurar Tudo** > **Clusters do HDInsight**.
2. Para o cluster do Spark, clique em **Dashboard**. Quando lhe for pedido, introduza as credenciais de administrador para o cluster do Spark.

    ![Inicie o Ambari](./media/apache-spark-resource-manager/hdinsight-launch-cluster-dashboard.png "inicie o Gestor de recursos")
3. Isto deve iniciar a IU da Web do Ambari, conforme mostrado na captura de ecrã.

    ![IU da Web do Ambari](./media/apache-spark-resource-manager/ambari-web-ui.png "IU da Web do Ambari")   

## <a name="how-do-i-launch-the-spark-history-server"></a>Como iniciar o servidor de histórico do Spark?
1. No [portal do Azure](https://portal.azure.com/), no startboard, clique no mosaico relativo ao cluster do Spark (se o tiver afixado ao startboard).
2. No painel do cluster, em **ligações rápidas**, clique em **Cluster Dashboard**. No **Cluster Dashboard** painel, clique em **Spark histórico servidor**.

    ![Servidor do histórico de spark](./media/apache-spark-resource-manager/launch-history-server.png "Spark histórico servidor")

    Quando lhe for pedido, introduza as credenciais de administrador para o cluster do Spark.

## <a name="how-do-i-launch-the-yarn-ui"></a>Como iniciar a IU do Yarn?
Pode utilizar a IU do YARN para monitorizar as aplicações que estão atualmente em execução no cluster do Spark.

1. No painel do cluster, clique em **Cluster Dashboard**e, em seguida, clique em **YARN**.

    ![Iniciar a IU do YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Em alternativa, pode também iniciar a IU do YARN da IU do Ambari. Para iniciar a IU do Ambari, no painel do cluster, clique em **Cluster Dashboard**e, em seguida, clique em **Dashboard de Cluster do HDInsight**. Na IU do Ambari, clique em **YARN**, clique em **ligações rápidas**, clique em Gestor de recursos do Active Directory e, em seguida, clique em **da IU do Gestor de recursos**.
   >
   >

## <a name="what-is-the-optimum-cluster-configuration-to-run-spark-applications"></a>O que é a configuração do cluster ideal para executar Spark aplicações?
Os três parâmetros de chaves que podem ser utilizados para a configuração de Spark consoante os requisitos da aplicação são `spark.executor.instances`, `spark.executor.cores`, e `spark.executor.memory`. Um Executor é um processo iniciado para uma aplicação de Spark. Este é executado no nó de trabalho e é responsável para realizar as tarefas para a aplicação. O número predefinido de executor e os tamanhos de executor de cada cluster é calculado com base no número de nós de trabalho e o tamanho de nó de trabalho. Esta informação é armazenada no `spark-defaults.conf` sobre os nós principais do cluster.

Os parâmetros de três configuração podem ser configurados ao nível do cluster (para todas as aplicações executadas no cluster) ou podem ser especificados para cada aplicação individuais bem.

### <a name="change-the-parameters-using-ambari-ui"></a>Altere os parâmetros através da IU do Ambari
1. De clique a IU do Ambari **Spark**, clique em **Contigs**e, em seguida, expanda **personalizada spark-predefinições**.

    ![Parâmetros de conjunto com o Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Os valores predefinidos são boas ter 4 Spark as aplicações são executadas em simultâneo no cluster. Pode alterar estes valores de interface de utilizador, conforme mostrado abaixo.

    ![Parâmetros de conjunto com o Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Clique em **guardar** para guardar as alterações de configuração. Na parte superior da página, lhe for pedido para reiniciar os serviços afetados. Clique em **reiniciar**.

    ![Reinicie os serviços](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Altere os parâmetros para uma aplicação em execução no bloco de notas do Jupyter
Para aplicações em execução no bloco de notas do Jupyter, pode utilizar o `%%configure` magic que efetue as alterações de configuração. Idealmente, tem de se essas alterações no início da aplicação, antes de executar a sua primeira célula do código. Efetuar este procedimento assegura que a configuração é aplicada à sessão Livy, quando é criada. Se pretender alterar a configuração de uma fase posterior da aplicação, tem de utilizar o `-f` parâmetro. No entanto, por se o fizer, perderá todas as progresso na aplicação.

O fragmento abaixo mostra como alterar a configuração para uma aplicação em execução no Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Parâmetros de configuração tem de ser transmitidos como uma cadeia JSON e tem de estar na linha seguinte após a magia, conforme apresentado na coluna de exemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Altere os parâmetros para uma aplicação enviados através de spark-submeter
Os seguintes comandos é um exemplo de como pode alterar os parâmetros de configuração para uma aplicação de batch que é submetido utilizando `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Altere os parâmetros para uma aplicação submetido utilizando cURL
Os seguintes comandos é um exemplo de como pode alterar os parâmetros de configuração para uma aplicação de batch que é submetido utilizando utilizando cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="how-do-i-change-these-parameters-on-a-spark-thrift-server"></a>Como posso alterar estes parâmetros num servidor Thrift de Spark?
Servidor Thrift de Spark fornece acesso JDBC/ODBC para um cluster do Spark e é utilizado para consultas de Spark SQL do serviço. Ferramentas como o Power BI, Tableau etc. Utilize o protocolo ODBC para comunicar com o servidor de Thrift de Spark para executar consultas do Spark SQL como uma aplicação de Spark. Quando é criado um cluster do Spark, duas instâncias do servidor Thrift de Spark são iniciadas, um em cada nó principal. Cada servidor Thrift de Spark é visível como uma aplicação de Spark na IU do YARN.

Servidor Thrift de Spark utiliza alocação do Spark executor dinâmica e, por conseguinte, o `spark.executor.instances` não é utilizado. Em vez disso, utiliza servidor Thrift de Spark `spark.dynamicAllocation.minExecutors` e `spark.dynamicAllocation.maxExecutors` para especificar a contagem de executor. Os parâmetros de configuração `spark.executor.cores` e `spark.executor.memory` é utilizado para modificar o tamanho de executor. Pode alterar estes parâmetros, conforme mostrado nos passos seguintes.

* Expanda o **avançadas sparkconf de thrift de spark** categoria para atualizar os parâmetros `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, e `spark.executor.memory`.

    ![Configurar o servidor thrift de Spark](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Expanda o **personalizada spark-thrift-sparkconf** categoria para atualizar o parâmetro `spark.executor.cores`.

    ![Configurar o servidor thrift de Spark](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="how-do-i-change-the-driver-memory-of-the-spark-thrift-server"></a>Como posso alterar a memória do controlador do servidor Thrift de Spark?
Memória do servidor Thrift de Spark controlador é configurado para 25% do tamanho da RAM de nó principal, fornecido que o tamanho da RAM total do nó principal é superior ao 14GB. Pode utilizar a IU do Ambari para alterar a configuração de memória de controladores, como mostrado abaixo.

* De clique a IU do Ambari **Spark**, clique em **folhas**, expanda **avançadas spark env**e, em seguida, forneça o valor para **spark_thrift_cmd_opts**.

    ![Configurar o servidor thrift de Spark RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="i-do-not-use-bi-with-spark-cluster-how-do-i-take-the-resources-back"></a>Não utilizo BI com um cluster do Spark. Como posso colocar os recursos novamente?
Uma vez que utilizamos alocação dinâmica Spark, os únicos recursos que são consumidos pelo servidor thrift são os recursos para modelos de estrutura mestres duas aplicações. Para recuperar estes recursos tem de parar os serviços de servidor Thrift em execução no cluster.

1. Na IU do Ambari, no painel esquerdo, clique em **Spark**.
2. Na página seguinte, clique em **servidores de Thrift de Spark**.

    ![Reinicie o servidor thrift](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Deverá ver os duas headnodes no qual está a executar o servidor Thrift de Spark. Clique do headnodes.

    ![Reinicie o servidor thrift](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. A página seguinte apresenta uma lista de todos os serviços em execução nesse headnode. Na lista, clique no botão de lista pendente junto do servidor Thrift de Spark e, em seguida, clique em **parar**.

    ![Reinicie o servidor thrift](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Repita estes passos em de outra headnode bem.

## <a name="my-jupyter-notebooks-are-not-running-as-expected-how-can-i-restart-the-service"></a>A minha blocos de notas do Jupyter não estão em execução conforme esperado. Como pode a reiniciar o serviço?
Inicie a IU de Web do Ambari, conforme mostrado acima. No painel de navegação esquerdo, clique em **Jupyter**, clique em **serviço ações**e, em seguida, clique em **todas reinicie**. Isto irá iniciar o serviço de Jupyter em todos os headnodes.

    ![Restart Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Restart Jupyter")

## <a name="how-do-i-know-if-i-am-running-out-of-resources"></a>Como saber se estiver a ficar sem recursos?
Inicie a IU do Yarn, conforme mostrado acima. Na tabela de métricas de Cluster por cima do ecrã, verifique os valores de **memória utilizada** e **memória Total** colunas. Se os valores de 2 são muito fechar, poderão não existir recursos suficientes para iniciar a aplicação seguinte. O mesmo se aplica para o **VCores utilizado** e **VCores Total** colunas. Além disso, na vista de principal, se existir uma aplicação stayed no **ACEITES** estado e não vão transitar para **executar** nem **falha** Estado, isto também pode ser uma indicação de que Não é ao obter recursos suficientes para iniciar.

    ![Resource Limit](./media/apache-spark-resource-manager/resource-limit.png "Resource Limit")

## <a name="how-do-i-kill-a-running-application-to-free-up-resource"></a>Como posso eliminar uma aplicação em execução para libertar recursos?
1. Na IU do Yarn, a partir do painel esquerdo, clique em **executar**. Na lista de aplicações em execução, determinar a aplicação a ser cancelado e clique em de **ID**.

    ![Kill App1](./media/apache-spark-resource-manager/kill-app1.png "Kill App1")

2. Clique em **Kill aplicação** no canto superior direito, em seguida, clique em **OK**.

    ![Kill App2](./media/apache-spark-resource-manager/kill-app2.png "Kill App2")

## <a name="see-also"></a>Consultar também
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para os analistas de dados

* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados telemétricos do Application Insight com o Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Utilizar Caffe no Azure HDInsight Spark para aprender profunda distribuída](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Para programadores do Spark

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
