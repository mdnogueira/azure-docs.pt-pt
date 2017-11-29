---
title: "Depurar tarefas do Apache Spark em execução no Azure HDInsight | Microsoft Docs"
description: "Utilize a IU do YARN, a IU do Spark e o servidor de histórico de Spark para controlar e depurar tarefas em execução num cluster do Spark no Azure HDInsight"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 59af05a7-2bd9-44b0-b55f-2438d294198b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jgao
ms.openlocfilehash: 1eaa5982703c31485c7b73eae780a62a0c5d672a
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Depurar tarefas do Apache Spark em execução no Azure HDInsight

Neste artigo, irá aprender a controlar e depurar tarefas de Spark em execução nos clusters do HDInsight utilizando a IU do YARN, Spark IU e o servidor de histórico de Spark. Para este artigo, vamos iniciar uma tarefa de Spark através de um bloco de notas disponível com o cluster do Spark, **Machine learning: Análise Preditiva nos dados de inspeção de prato utilizando MLLib**. Pode utilizar os passos abaixo para controlar uma aplicação que é submetido com quaisquer outra abordagem, bem como, por exemplo, **submeter o spark**.

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Deve ter iniciado a executar o bloco de notas,  **[Machine learning: Análise Preditiva nos dados de inspeção de prato utilizando MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Para obter instruções sobre como executar este bloco de notas, siga a ligação.  

## <a name="track-an-application-in-the-yarn-ui"></a>Controlar uma aplicação na IU do YARN
1. Inicie a IU do YARN. No painel do cluster, clique em **Cluster Dashboard**e, em seguida, clique em **YARN**.
   
    ![Iniciar a IU do YARN](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Em alternativa, pode também iniciar a IU do YARN da IU do Ambari. Para iniciar a IU do Ambari, no painel do cluster, clique em **Cluster Dashboard**e, em seguida, clique em **Dashboard de Cluster do HDInsight**. Na IU do Ambari, clique em **YARN**, clique em **ligações rápidas**, clique o Gestor de recursos do Active Directory e, em seguida, clique em **ResourceManager IU**.    
   > 
   > 
2. Porque iniciou a tarefa de Spark utilizando blocos de notas do Jupyter, a aplicação com o nome **remotesparkmagics** (este é o nome de todas as aplicações que são iniciada a partir de blocos de notas). Clique no ID da aplicação contra o nome da aplicação para obter mais informações sobre a tarefa. Isto inicia a vista da aplicação.
   
    ![Localizar o ID de aplicação do Spark](./media/apache-spark-job-debugging/find-application-id.png)
   
    Para essas aplicações que são iniciadas a partir de blocos de notas do Jupyter, o estado é sempre **executar** até sair o bloco de notas.
3. Da vista de aplicação, pode desagregar further to descobrir os contentores associada com a aplicação e os registos de (stdout/stderr). Também pode iniciar a IU do Spark, clicando em ligação correspondente para o **URL de controlo**, conforme mostrado abaixo. 
   
    ![Transferir os registos do contentor](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Controlar uma aplicação na IU do Spark
Na IU do Spark, pode desagregar para as tarefas do Spark que são geradas pela aplicação iniciada anteriormente.

1. Para iniciar a IU do Spark, da vista de aplicação, clique na ligação contra o **URL de controlo**, conforme mostrado na captura de ecrã acima. Pode ver todas as tarefas do Spark que são iniciadas pela aplicação em execução no bloco de notas do Jupyter.
   
    ![Ver tarefas do Spark](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Clique em de **executores** separador para ver informações de processamento e armazenamento para cada executor. Também pode obter a pilha de chamadas ao clicar no **Thread informação do estado** ligação.
   
    ![Ver o executor do Spark](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Clique em de **fases** separador para ver as fases associadas à aplicação.
   
    ![Fases de Spark de vista](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Cada fase pode ter várias tarefas para o qual pode ver estatísticas de execução, como mostrado abaixo.
   
    ![Fases de Spark de vista](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Na página de detalhes da fase, pode iniciar a visualização de DAG. Expanda o **DAG visualização** ligação na parte superior da página, conforme mostrado abaixo.
   
    ![Ver a visualização de fases DAG do Spark](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG ou direto Aclyic gráfico representa as fases diferentes na aplicação. Cada caixa blue no gráfico representa uma operação de Spark invocada a partir da aplicação.
5. Na página de detalhes da fase, pode também iniciar a vista de linha cronológica de aplicação. Expanda o **linha cronológica de evento** ligação na parte superior da página, conforme mostrado abaixo.
   
    ![Ver a linha cronológica de eventos do Spark fases](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Esta ação apresenta os eventos de Spark sob a forma de uma linha cronológica. A vista de linha cronológica está disponível em três níveis, tarefas, dentro de uma tarefa e dentro de uma fase. A imagem acima captura a vista de linha cronológica para uma determinada fase.
   
   > [!TIP]
   > Se selecionar a **ativar o zoom** caixa de verificação pode se deslocar para a esquerda e direita em toda a linha cronológica vista.
   > 
   > 
6. Outros separadores na IU do Spark fornecem informações úteis sobre, bem como a instância de Spark.
   
   * Separador de armazenamento - se a sua aplicação cria um RDDs, pode encontrar informações sobre os no separador de armazenamento.
   * Separador ambiente - neste separador fornece uma grande quantidade de informações úteis sobre a instância de Spark, tais como o 
     * Versão scala
     * Diretório de registo de eventos associado ao cluster
     * Número de núcleos de executor para a aplicação
     * Etc.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Localizar informações sobre as tarefas concluídas utilizando o servidor de histórico do Spark
Depois de uma tarefa é concluída, as informações sobre a tarefa são continuadas no servidor de histórico de Spark.

1. Para iniciar o servidor de histórico do Spark, do painel do cluster, clique em **Cluster Dashboard**e, em seguida, clique em **Spark histórico servidor**.
   
    ![Iniciar o servidor de histórico do Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Em alternativa, pode também iniciar a IU do servidor de histórico do Spark da IU do Ambari. Para iniciar a IU do Ambari, no painel do cluster, clique em **Cluster Dashboard**e, em seguida, clique em **Dashboard de Cluster do HDInsight**. Na IU do Ambari, clique em **Spark**, clique em **ligações rápidas**e, em seguida, clique em **IU do servidor de histórico de Spark**.
   > 
   > 
2. Irá ver todas as aplicações concluídas listadas. Clique num ID de aplicação para desagregar para uma aplicação para obter mais informações.
   
    ![Iniciar o servidor de histórico do Spark](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Consultar também
*  [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)

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


