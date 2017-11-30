---
title: "Toolkit do Azure do Eclipse: Scala criar aplicações do HDInsight Spark | Microsoft Docs"
description: "Utilize as ferramentas do HDInsight na Azure Toolkit para Eclipse para desenvolver aplicações do Spark escritas no Scala e submetê-las para um cluster do HDInsight Spark, diretamente a partir do Eclipse IDE."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: nitinme
ms.openlocfilehash: ede1a974b32227edf44464ed56ae85a1ea7ee97b
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Utilize o Toolkit de Azure do Eclipse para criar Spark aplicações para um cluster do HDInsight

Utilize as ferramentas do HDInsight na Azure Toolkit para Eclipse para desenvolver aplicações do Spark escritas no Scala e submetê-las para um cluster do Azure HDInsight Spark, diretamente a partir do Eclipse IDE. Pode utilizar as ferramentas do HDInsight Plug-in de algumas formas diferentes:

* Para desenvolver e submeter a uma aplicação num cluster do Spark do HDInsight Scala Spark
* Para aceder aos recursos de cluster do Azure HDInsight Spark
* Para desenvolver e executar localmente uma aplicação do Scala Spark

> [!IMPORTANT]
> Pode utilizar esta ferramenta para criar e submeter aplicações apenas para um cluster do HDInsight Spark no Linux.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit versão 8, que é utilizado para o tempo de execução do Eclipse IDE. Poderá transferi-lo do [site Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Eclipse IDE. Este artigo utiliza o Eclipse Neon. Pode instalá-lo do [Web site do Eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalar as ferramentas do HDInsight na Azure Toolkit para Eclipse e Scala Plug-in
### <a name="install-hdinsight-toolsazure-toolkit-for"></a>Instalar para a do toolkit do HDInsight Toolsazure
Ferramentas do HDInsight para o Eclipse está disponível como parte do Toolkit de Azure do Eclipse. Para obter instruções de instalação, consulte [instalar o Toolkit de Azure do Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).
### <a name="install-the-scala-plug-in"></a>Instalar o plug-in de Scala
Quando abrir o Eclipse, o HDInsight ferramenta Deteta automaticamente se tiver instalado o Scala Plug-in. Selecione **OK** para continuar e, em seguida, siga as instruções para instalar o plug-in do Eclipse Marketplace.

![Instalação automática de Scala Plug-in](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure
1. Inicie o IDE Eclipse e abra o Explorador do Azure. No **janela** menu, selecione **Mostrar vista**e, em seguida, selecione **outros**. Na caixa de diálogo que se abre, expanda **Azure**, selecione **Explorador do Azure**e, em seguida, selecione **OK**.

   ![Mostrar a caixa de diálogo de vista](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Clique com botão direito do **Azure** nó e, em seguida, selecione **sessão**.
3. No **Azure sessão** caixa de diálogo caixa, escolha o método de autenticação, selecione **sessão**e introduza as suas credenciais do Azure.
   
   ![Azure sessão caixa](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Depois de a sessão iniciada, o **selecionar subscrições** caixa de diálogo apresenta uma lista de todas as subscrições Azure associadas com as credenciais. Clique em **selecione** para fechar a caixa de diálogo.

   ![Selecione a caixa de diálogo de subscrições](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. No **Explorador do Azure** separador, expanda **HDInsight** para ver a clusters do HDInsight Spark na sua subscrição.
   
   ![Clusters do HDInsight Spark no Explorador do Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Pode expandir ainda mais um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados com o cluster.
   
   ![Um nome de cluster para ver os recursos de expansão](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)



## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configurar um projeto do Spark Scala para um cluster do Spark do HDInsight

1. Na área de trabalho Eclipse IDE, selecione **ficheiro**, selecione **novo**e, em seguida, selecione **projeto**. 
2. No Assistente de novo projeto, expanda **HDInsight**, selecione **Spark no HDInsight (Scala)**e, em seguida, selecione **seguinte**.

   ![Selecionar o Spark no HDInsight (Scala) projeto](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. O Assistente de criação de projeto Scala Deteta automaticamente se tiver instalado o Scala Plug-in. Selecione **OK** para continuar a transferir o Scala Plug-in e, em seguida, siga as instruções para reiniciar o Eclipse.

   ![verificação de scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. No **novo projeto do HDInsight Scala** , forneça os valores seguintes e, em seguida, selecione **seguinte**:
   * Introduza um nome para o projeto.
   * No **JRE** área, certifique-se de que **utilizar um ambiente de execução JRE** está definido como **JavaSE 1.7** ou posterior.
   * No **Spark biblioteca** área, pode escolher **utilizar Maven para configurar o SDK do Spark** opção.  A nossa ferramenta integra-se a versão correta para o SDK do Spark e Scala SDK. Também pode optar por **adicionar manualmente o Spark SDK** opção, transfira e adicionar SDK Spark por manualmente.

   ![Caixa de diálogo novo projeto do HDInsight Scala](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. Na caixa de diálogo seguinte, selecione **concluir**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Criar uma aplicação de Scala para um cluster do Spark do HDInsight

1. No Eclipse IDE, a partir do Explorador do pacote, expanda o projeto que criou anteriormente, clique com botão direito **src**, aponte para **novo**e, em seguida, selecione **outros**.
2. No **selecionar um assistente** diálogo caixa, expanda **Scala assistentes**, selecione **Scala objeto**e, em seguida, selecione **seguinte**.
   
   ![Selecione uma caixa de diálogo Assistente](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. No **criar o novo ficheiro** caixa de diálogo, introduza um nome para o objeto e, em seguida, selecione **concluir**.
   
   ![Criar caixa de diálogo novo ficheiro](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Cole o seguinte código no editor de texto:
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
5. Execute a aplicação num cluster do Spark do HDInsight:
   
   a. A partir do Explorador do pacote, faça duplo clique no nome do projeto e, em seguida, selecione **submeter aplicações do Spark no HDInsight**.        
   b. No **Spark submissão** , forneça os valores seguintes e, em seguida, selecione **submeter**:
      
      * Para **nome do Cluster**, selecione o cluster do HDInsight Spark no qual pretende executar a sua aplicação.
      * Selecione um artefacto do projeto Eclipse ou selecione um disco rígido. O valor predefinido depende do item, clique com botão direito do Explorador do pacote.
      * No **nome da classe principal** na lista pendente, o Assistente de submissão apresenta todos os nomes de objeto do seu projeto. Selecione ou introduza uma que pretende executar. Se tiver selecionado um artefacto a partir de uma unidade de disco rígida, tem de introduzir manualmente o nome da classe principal. 
      * Porque o código de aplicação neste exemplo não requerem argumentos da linha de comandos ou de referência v7 ou ficheiros, pode deixar as restantes caixas de texto vazia.
        
      ![Caixa de diálogo de submissão de Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. O **Spark submissão** separador deve começar que apresenta o progresso. Pode parar a aplicação, selecionando o botão vermelho no **Spark submissão** janela. Também pode ver os registos para esta aplicação específica executar selecionando o ícone de globo (em falta por que a caixa de blue na imagem).
      
   ![Janela de submissão de Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Aceder e gerir clusters do HDInsight Spark utilizando as ferramentas do HDInsight na Azure Toolkit para Eclipse
Pode desempenhar várias operações, utilizando as ferramentas do HDInsight, incluindo a aceder ao resultado da tarefa.

### <a name="access-the-job-view"></a>Aceda à vista de tarefa
1. No Explorador do Azure, expanda **HDInsight**, expanda o nome de cluster do Spark e, em seguida, selecione **tarefas**. 

   ![Nó de vista de tarefa](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

2. Selecione o **tarefas** nós. Se a versão do Java é inferior ao **1.8**, as ferramentas do HDInsight automaticamente o lembrete de instalar o **I (fx) clipse** Plug-in. Selecione **OK** para continuar e, em seguida, siga o Assistente para instalar a partir do Marketplace Eclipse e reiniciar o Eclipse. 

   ![Instalar clipse I (fx)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. Abrir a vista de tarefa do **tarefas** nós. No painel da direita, a **vista de tarefas do Spark** separador apresenta todas as aplicações que foram executadas no cluster. Selecione o nome da aplicação para o qual pretende ver mais detalhes.

   ![Detalhes da aplicação](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Em seguida, pode efetuar qualquer uma destas ações:

   * Paire o rato sobre o gráfico da tarefa. Apresenta informações básicas sobre a tarefa em execução. Selecione o gráfico da tarefa e pode ver as fases e informações de que cada tarefa gera.

     ![Detalhes da fase de tarefa](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Selecione o **registo** separador para ver frequentemente utilizados registos, incluindo **controlador Stderr**, **controlador Stdout**, e **informações de diretório**.

     ![Detalhes de registo](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Abra o histórico de Spark IU e a IU do YARN (ao nível da aplicação), selecionando as hiperligações na parte superior da janela.

### <a name="access-the-storage-container-for-the-cluster"></a>Aceder ao contentor de armazenamento para o cluster
1. No Explorador do Azure, expanda o **HDInsight** nó de raiz para ver uma lista de clusters do HDInsight Spark que estão disponíveis.
2. Expanda o nome do cluster para ver a conta de armazenamento e o contentor de armazenamento predefinido para o cluster.
   
   ![Contentor de armazenamento de conta e predefinições do armazenamento](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Selecione o nome do contentor de armazenamento associado com o cluster. No painel da direita, faça duplo clique o **HVACOut** pasta. Abra um do **parte -** ficheiros para ver o resultado da aplicação.

### <a name="access-the-spark-history-server"></a>Aceder ao servidor de histórico do Spark
1. No Explorador do Azure, o nome de cluster do Spark com o botão direito e, em seguida, selecione **abra IU do histórico de Spark**. Quando lhe for pedido, introduza as credenciais de administrador para o cluster. Especificar estes ao aprovisionamento de cluster.
2. No dashboard de servidor do histórico do Spark, utilize o nome da aplicação para procurar a aplicação que acabou concluir em execução. No código anterior, defina o nome da aplicação utilizando `val conf = new SparkConf().setAppName("MyClusterApp")`. Por isso, o nome da sua aplicação Spark foi **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari
1. No Explorador do Azure, o nome de cluster do Spark com o botão direito e, em seguida, selecione **abra Portal de gestão de Cluster (Ambari)**. 
2. Quando lhe for pedido, introduza as credenciais de administrador para o cluster. Especificar estes ao aprovisionamento de cluster.

### <a name="manage-azure-subscriptions"></a>Gerir as subscrições do Azure
Por predefinição, a ferramenta de HDInsight no Azure Toolkit para Eclipse lista os clusters do Spark de todas as subscrições do Azure. Se necessário, pode especificar as subscrições para a qual pretende aceder ao cluster. 

1. No Explorador do Azure, clique com botão direito do **Azure** nó raiz e, em seguida, selecione **gerir subscrições**. 
2. Na caixa de diálogo, desmarque as caixas de verificação para a subscrição que não pretende aceder e, em seguida, selecione **fechar**. Também pode selecionar **terminar sessão** se pretende terminar a sua subscrição do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar uma aplicação do Spark Scala localmente
Pode utilizar as ferramentas do HDInsight na Azure Toolkit para Eclipse para executar aplicações do Spark Scala localmente na sua estação de trabalho. Normalmente, estas aplicações não precisam de acesso a recursos de cluster como um contentor de armazenamento, e podem executar e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito
Enquanto estiver a executar a aplicação do Spark Scala local num computador Windows, poderá obter uma exceção, conforme explicado no [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Esta exceção ocorre porque **WinUtils.exe** está em falta no Windows. 

Para resolver este erro, terá de [transferir o ficheiro executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) numa localização como **C:\WinUtils\bin**e, em seguida, adicione a variável de ambiente **HADOOP_HOME** e defina o valor de a variável **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar uma aplicação local do Spark Scala
1. Abra o Eclipse e criar um projeto. No **novo projeto** , selecione as seguintes opções e, em seguida, selecione **seguinte**.
   
   * No painel esquerdo, selecione **HDInsight**.
   * No painel direito, selecione **Spark no HDInsight Local executar exemplo (Scala)**.

   ![Caixa de diálogo Novo Projeto](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. Para fornecer os detalhes do projeto, siga os passos 3 a 6 da secção anterior [configurar um projeto do Spark Scala para um cluster do HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

3. O modelo adiciona um código de exemplo (**LogQuery**) sob o **src** pasta que pode executar localmente no seu computador.
   
   ![Localização do LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
4. Clique com botão direito do **LogQuery** aplicação, aponte para **Run**e, em seguida, selecione **1 Scala aplicação**. Como esta opção é apresentada de saída a **consola** separador:
   
   ![Aplicação de Spark local resultado da execução](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Problemas conhecidos
Para submeter uma aplicação no Azure Data Lake Store, selecione **interativo** modo durante o processo de início de sessão do Azure. Se selecionar **automatizada** modo, poderá obter um erro.

![O início de sessão interativo](./media/apache-spark-eclipse-tool-plugin/interactive-authentication.png)

Pode escolher um cluster do Azure Data Lake ao submeter a sua aplicação com qualquer método de início de sessão.

Atualmente, a visualização saídas de Spark diretamente não é suportada.

## <a name="feedback"></a>Comentários
Se tiver quaisquer comentários ou se tiver quaisquer outros problemas ao utilizar esta ferramenta, envie-num endereço de e-mail hdivstool@microsoft.com.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](../hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilize o Toolkit do Azure para o IntelliJ para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através da VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através de SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilize as ferramentas do HDInsight para o IntelliJ com Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestão de recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

