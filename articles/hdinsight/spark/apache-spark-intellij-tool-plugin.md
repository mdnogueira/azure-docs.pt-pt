---
title: "Toolkit do Azure para o IntelliJ: criar Spark aplicações para um cluster do HDInsight | Microsoft Docs"
description: "Utilize o Toolkit do Azure para o IntelliJ para desenvolver aplicações do Spark escritas no Scala e submetê-las para um cluster do HDInsight Spark."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: nitinme
ms.openlocfilehash: 82683349f3e562be5ac89ade4143588283abd71c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Utilize o Toolkit do Azure para o IntelliJ para criar Spark aplicações para um cluster do HDInsight

Utilize o Toolkit do Azure para o plug-in IntelliJ para desenvolver aplicações do Spark escritas no Scala e depois submetê-las para um cluster do HDInsight Spark diretamente a partir do ambiente de desenvolvimento integrado (IDE) de IntelliJ. Pode utilizar o plug-in de algumas formas:

* Desenvolver e submeter uma aplicação do Scala Spark num cluster do HDInsight Spark.
* Aceder aos recursos de cluster do Azure HDInsight Spark.
* Desenvolver e executar uma aplicação do Scala Spark localmente.

Para criar o projeto, ver o [criar aplicações do Spark com o Toolkit do Azure para o IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) vídeo.

> [!IMPORTANT]
> Pode utilizar este plug-in para criar e submeter aplicações apenas para um cluster do HDInsight Spark no Linux.
> 

## <a name="prerequisites"></a>Pré-requisitos

- Um cluster do Apache Spark no HDInsight Linux. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Kit de desenvolvimento do Java Oracle. Pode instalá-lo do [site Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. Este artigo utiliza a versão 2017.1. Pode instalá-lo do [JetBrains site](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Instalar o Toolkit do Azure para o IntelliJ
Para obter instruções de instalação, consulte [instalar o Toolkit do Azure para o IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure

1. Inicie o IntelliJ IDE e abra o Explorador do Azure. No **vista** menu, selecione **ferramenta Windows**e, em seguida, selecione **Explorador do Azure**.
       
   ![A ligação Explorador do Azure](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Clique com botão direito do **Azure** nó e, em seguida, selecione **sessão**.

3. No **Azure sessão** caixa de diálogo, selecione **sessão**e, em seguida, introduza as suas credenciais do Azure.

    ![A caixa de diálogo do Azure de sessão](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Depois de a sessão iniciada, o **selecionar subscrições** caixa de diálogo apresenta uma lista de todas as subscrições do Azure que estão associadas com as credenciais. Selecione o **selecione** botão.

    ![A caixa de diálogo Selecionar subscrições](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. No **Explorador do Azure** separador, expanda **HDInsight** para ver os clusters do HDInsight Spark que estão na sua subscrição.
   
    ![Clusters do HDInsight Spark no Explorador do Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Para ver os recursos (por exemplo, contas de armazenamento) que estão associados ao cluster, pode expandir ainda mais um nó de nome do cluster.
   
    ![Um nó expandido do nome do cluster](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar uma aplicação do Spark Scala num cluster do Spark do HDInsight

1. Iniciar o IntelliJ IDEA e, em seguida, criar um projeto. No **novo projeto** diálogo caixa, efetue o seguinte: 

   a. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.

   b. No **ferramenta de compilação** lista, selecione um dos seguintes, de acordo com a sua necessidade:

      * **Maven**, para o suporte de Assistente de criação do projeto Scala
      * **SBT**, para gerir as dependências e a criação do projeto Scala

    ![A caixa de diálogo novo projeto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. Selecione **seguinte**.

3. O Assistente de criação do projeto de Scala Deteta automaticamente se instalou o Scala Plug-in. Selecione **instalar**.

   ![Verificação de plug-in scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. Para transferir o Scala Plug-in, selecione **OK**. Siga as instruções para reiniciar o IntelliJ. 

   ![A caixa de diálogo de instalação de plug-in Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. No **novo projeto** janela, efetue o seguinte procedimento:  

    ![Selecionar o Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Introduza um nome de projeto e localização.

   b. No **projeto SDK** na lista pendente, selecione **1.8 do Java** para o cluster do Spark 2. x ou selecione **Java 1.7** para o cluster do Spark 1. x.

   c. No **versão Spark** na lista pendente, Assistente de criação do projeto Scala integra-se a versão correta para o SDK do Spark e Scala SDK. Se a versão de cluster do Spark é anterior ao 2.0, selecione **Spark 1. x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza **Spark 2.0.2 (Scala 2.11.8)**.

6. Selecione **Concluir**.

7. O projeto de Spark cria automaticamente um artefacto para si. Para ver o artefacto, efetue o seguinte:

   a. No **ficheiro** menu, selecione **estrutura do projeto**.

   b. No **estrutura do projeto** caixa de diálogo, selecione **artefactos** para ver o artefacto predefinido que é criado. Também pode criar os seus artefactos selecionando o sinal de adição (**+**).

      ![Informações de artefacto na caixa de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. Adicione o código fonte da aplicação da seguinte forma:

   a. No Explorador de projeto, clique com botão direito **src**, aponte para **novo**e, em seguida, selecione **Scala classe**.
      
      ![Comandos para criar uma classe de Scala a partir do Explorador de projeto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. No **criar uma nova classe de Scala** diálogo caixa, forneça um nome, selecione **objeto** no **tipo** caixa e, em seguida, selecione **OK**.
      
      ![Criar caixa de diálogo nova classe de Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. No **MyClusterApp.scala** ficheiro, cole o seguinte código. O código lê os dados de HVAC.csv (disponível em todos os clusters do HDInsight Spark), obtém as linhas que têm apenas um dígito na coluna seventh no ficheiro CSV e escreve a saída para **/HVACOut** sob o contentor de armazenamento predefinido para o cluster.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

9. Execute a aplicação num cluster do HDInsight Spark efetuando o seguinte procedimento:

   a. No Explorador de projeto, faça duplo clique no nome do projeto e, em seguida, selecione **submeter aplicações do Spark no HDInsight**.
      
      ![A aplicação de Spark submeter para comandos do HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Lhe for pedido para introduzir as suas credenciais de subscrição do Azure. No **Spark submissão** , forneça os valores seguintes e, em seguida, selecione **submeter**.
      
      * Para **Spark clusters (apenas Linux)**, selecione o cluster do HDInsight Spark no qual pretende executar a sua aplicação.

      * Selecione um artefacto do projeto IntelliJ ou selecione um disco rígido.

      * No **nome da classe principal** caixa, selecione o botão de reticências (**...** ), selecione a classe principal no seu código fonte da aplicação e, em seguida, selecione **OK**.

        ![A caixa de diálogo Selecionar classe de principal](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Porque o código de aplicação neste exemplo não necessitam de argumentos da linha de comandos ou de referência v7 ou ficheiros, pode deixar as restantes caixas vazio. Depois de fornecer todas as informações, a caixa de diálogo deve assemelhar-se a imagem seguinte.
        
        ![A caixa de diálogo de submissão de Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. O **Spark submissão** separador na parte inferior da janela deve começar que apresenta o progresso. Também pode parar a aplicação, selecionando o botão vermelho no **Spark submissão** janela.
      
      ![A janela de submissão de Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Para saber como resultado da tarefa de acesso, consulte o "acesso e gerir clusters do HDInsight Spark através do Toolkit do Azure para o IntelliJ" secção neste artigo.

## <a name="run-or-debug-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar ou depurar uma aplicação do Spark Scala num cluster do Spark do HDInsight
Recomendamos também outra maneira de submeter a aplicação de Spark para o cluster. Pode fazê-lo ao definir os parâmetros **configurações de execução/depuração** IDE. Para obter mais informações, consulte [remotamente depurar aplicações do Spark num cluster do HDInsight com o Toolkit do Azure para o IntelliJ através de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Aceder e gerir clusters do HDInsight Spark através do Toolkit do Azure para o IntelliJ
Pode desempenhar várias operações, utilizando o Toolkit do Azure para o IntelliJ.

### <a name="access-the-job-view"></a>Aceda à vista de tarefa
1. No Explorador do Azure, expanda **HDInsight**, expanda o nome de cluster do Spark e, em seguida, selecione **tarefas**.  

    ![Nó de vista de tarefa](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. No painel da direita, a **vista de tarefas do Spark** separador apresenta todas as aplicações que foram executadas no cluster. Selecione o nome da aplicação para o qual pretende ver mais detalhes.

    ![Detalhes da aplicação](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Para apresentar informações básicas de tarefas em execução, coloque o cursor sobre o gráfico da tarefa. Para ver as informações que cada tarefa gera e gráfico de fases, selecione um nó de gráfico da tarefa.

    ![Detalhes da fase de tarefa](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Para ver frequentemente utilizados registos, tal como *controlador Stderr*, *controlador Stdout*, e *informações de diretório*, selecione o **registo** separador.

    ![Detalhes de registo](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Também pode ver o histórico do Spark IU e a IU do YARN (ao nível da aplicação) ao selecionar uma ligação na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Aceder ao servidor de histórico do Spark
1. No Explorador do Azure, expanda **HDInsight**, faça duplo clique o nome de cluster do Spark e, em seguida, selecione **abra IU do histórico de Spark**. 

2. Quando lhe for pedido, introduza as credenciais de administrador do cluster, o que especificou quando configurou o cluster.

3. No dashboard de servidor do histórico do Spark, pode utilizar o nome da aplicação para procurar a aplicação que acabou concluir em execução. No código anterior, defina o nome da aplicação utilizando `val conf = new SparkConf().setAppName("MyClusterApp")`. Por conseguinte, é o nome da sua aplicação Spark **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari
1. No Explorador do Azure, expanda **HDInsight**, faça duplo clique o nome de cluster do Spark e, em seguida, selecione **abra Portal de gestão de Cluster (Ambari)**. 

2. Quando lhe for pedido, introduza as credenciais de administrador para o cluster. Estas credenciais que especificou durante o processo de configuração de cluster.

### <a name="manage-azure-subscriptions"></a>Gerir as subscrições do Azure
Por predefinição, o Toolkit do Azure para o IntelliJ lista os clusters do Spark de todas as subscrições do Azure. Se necessário, pode especificar as subscrições que pretende aceder. 

1. No Explorador do Azure, clique com botão direito do **Azure** nó raiz e, em seguida, selecione **gerir subscrições**. 

2. Na caixa de diálogo, desmarque as caixas de verificação junto as subscrições que não pretende aceder e, em seguida, selecione **fechar**. Também pode selecionar **terminar sessão** se pretende terminar a sua subscrição do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar uma aplicação do Spark Scala localmente
Pode utilizar o Toolkit do Azure para o IntelliJ para executar aplicações do Spark Scala localmente na sua estação de trabalho. As aplicações, normalmente, não precisam de acesso a recursos de cluster, como contentores de armazenamento, e podem executar e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito
Enquanto estiver a executar a aplicação do Spark Scala local num computador Windows, poderá obter uma exceção, conforme explicado no [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils.exe está em falta no Windows. 

Para resolver este erro, [transferir o ficheiro executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) numa localização como **C:\WinUtils\bin**. Em seguida, adicione a variável de ambiente **HADOOP_HOME**e defina o valor da variável para **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar uma aplicação local do Spark Scala
1. Inicie o IntelliJ IDEA e criar um projeto. 

2. No **novo projeto** diálogo caixa, efetue o seguinte:
   
    a. Selecione **HDInsight** > **Spark no HDInsight Local execução exemplo (Scala)**.

    b. No **ferramenta de compilação** lista, selecione um dos seguintes, de acordo com a sua necessidade:

      * **Maven**, para o suporte de Assistente de criação do projeto Scala
      * **SBT**, para gerir as dependências e a criação do projeto Scala

    ![A caixa de diálogo novo projeto](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

3. Selecione **seguinte**.
 
4. Na janela do seguinte, efetue o seguinte:
   
    a. Introduza um nome de projeto e localização.

    b. No **projeto SDK** pendente lista, selecione uma versão do Java que é posterior à versão 1.7.

    c. No **Spark versão** pendente lista, selecione a versão do Scala que pretende utilizar: Scala 2.11.x para Spark 2.0 ou Scala 2.10.x para 1.6 de Spark.

    ![A caixa de diálogo novo projeto](./media/apache-spark-intellij-tool-plugin/Create-local-project.PNG)

5. Selecione **Concluir**.

6. O modelo adiciona um código de exemplo (**LogQuery**) sob o **src** pasta que pode executar localmente no seu computador.
   
    ![Localização do LogQuery](./media/apache-spark-intellij-tool-plugin/local-app.png)

7. Clique com botão direito do **LogQuery** aplicação e, em seguida, selecione **executar 'LogQuery'**. No **executar** separador na parte inferior, pode ver um resultado semelhante ao seguinte:
   
   ![Aplicação de Spark local resultado da execução](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter aplicações IntelliJ IDEA existentes para utilizar o Toolkit do Azure para o IntelliJ
Pode converter o Scala existente do Spark aplicações que criou no IntelliJ IDEA para ser compatível com o Toolkit do Azure para o IntelliJ. Pode utilizar o plug-in para submeter aplicações para um cluster do HDInsight Spark.

1. Para uma aplicação de Spark Scala existente que foi criada através da IntelliJ IDEA, abra o ficheiro .iml associado.

2. Na raiz nível é um **módulo** elemento semelhante ao seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edite o elemento para adicionar `UniqueKey="HDInsightTool"` para que o **módulo** elemento assemelha ao seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Guarde as alterações. A aplicação deve ser compatível com o Toolkit do Azure para o IntelliJ. Pode testar clicando com o nome do projeto no Explorador de projeto. O menu de pop-up tem agora a opção **submeter aplicações do Spark no HDInsight**.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Erro na execução local: *utilize um tamanho maior de área dinâmica para dados*
No Spark 1.6, se estiver a utilizar um SDK de Java de 32 bits durante a execução local, podem ocorrer os seguintes erros:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Estes erros acontecer porque o tamanho da área dinâmica para dados não é suficientemente grande para Spark executar. O Spark requer, pelo menos, 471 MB. (Para obter mais informações, consulte [SPARK 12081](https://issues.apache.org/jira/browse/SPARK-12081).) É uma solução simple utilizar um SDK de Java de 64 bits. Também pode alterar as definições de JVM IntelliJ adicionando as seguintes opções:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![A adição de opções para a caixa de "Opções de VM" IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>FAQ
Para submeter uma aplicação no Azure Data Lake Store, escolha **interativo** modo durante o processo de início de sessão do Azure. Se selecionar **automatizada** modo, pode obter um erro.

![interative de início de sessão](./media/apache-spark-intellij-tool-plugin/interative-signin.png)

Agora, iremos resolve. Pode escolher um Cluster do Azure Data Lake ao submeter a sua aplicação com qualquer método de início de sessão.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
Atualmente, a visualização saídas de Spark diretamente não é suportada.

Se tiver quaisquer comentários ou uma sugestão ou se tiver quaisquer problemas ao utilizar este plug-in, e-mail-nos hdivstool@microsoft.com.

## <a name="seealso"></a>Passos seguintes
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Criar Scala projeto (vídeo): [criar aplicações do Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Toolkit do Azure de utilização para o IntelliJ depurar aplicações do Spark remotamente num Cluster do HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Spark com BI: efetuar uma análise de dados interativa ao utilizar o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através da VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilize as ferramentas do HDInsight para o IntelliJ com Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar as ferramentas do HDInsight na Azure Toolkit para Eclipse para criar Spark aplicações](apache-spark-eclipse-tool-plugin.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestão de recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

