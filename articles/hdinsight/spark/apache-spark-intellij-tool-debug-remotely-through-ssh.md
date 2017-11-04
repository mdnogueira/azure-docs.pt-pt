---
title: "Toolkit do Azure para o IntelliJ: aplicações de depuração Spark remotamente através do SSH | Microsoft Docs"
description: "Clusters orientações passo a passo sobre como utilizar as ferramentas do HDInsight na Azure Toolkit para o IntelliJ para depurar aplicações remotamente no HDInsight através de SSH"
keywords: "depurar remotamente intellij, de depuração remota intellij, ssh, intellij, hdinsight, depurar intellij, depuração"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 08/24/2017
ms.author: jejiang
ms.openlocfilehash: cebbe2a0e28d49c93d0ebf12cc04b3d201dcec97
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depurar aplicações do Spark num cluster do HDInsight com o Toolkit do Azure para o IntelliJ através de SSH

Este artigo fornece orientações passo a passo sobre como utilizar as ferramentas do HDInsight na Azure Toolkit para o IntelliJ para depurar aplicações remotamente num cluster do HDInsight. Para depurar o projeto, também pode ver o [aplicações de depuração Spark do HDInsight com o Toolkit do Azure para o IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) vídeo.

**Pré-requisitos**

* **As ferramentas do HDInsight no Toolkit do Azure para o IntelliJ**. Esta ferramenta faz parte do Toolkit do Azure para o IntelliJ. Para obter mais informações, consulte [instalar o Toolkit do Azure para o IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).
* **Toolkit do Azure para o IntelliJ**. Utilize este toolkit para criar Spark aplicações para um cluster do HDInsight. Para obter mais informações, siga as instruções em [Toolkit do Azure de utilização para o IntelliJ criar Spark aplicações para um cluster do HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **SSH do HDInsight serviço com o nome de utilizador e palavra-passe gestão**. Para obter mais informações, consulte [ligar para o HDInsight (Hadoop), utilizando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [utilizar o SSH túnel para aceder à Ambari web IU, JobHistory, NameNode, Oozie e outras UIs web](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Criar uma aplicação do Spark Scala e configurá-la para depuração remota

1. Iniciar o IntelliJ IDEA e, em seguida, criar um projeto. No **novo projeto** diálogo caixa, efetue o seguinte:

   a. Selecione **HDInsight**. 

   b. Selecione um modelo Java ou Scala com base na sua preferência. Selecione entre as seguintes opções:

      - **O Spark no HDInsight (Scala)**

      - **O Spark no HDInsight (Java)**

      - **O Spark no HDInsight Cluster execução exemplo (Scala)**

      Este exemplo utiliza um **Spark no HDInsight Cluster executar exemplo (Scala)** modelo.

   c. No **ferramenta de compilação** lista, selecione um dos seguintes, de acordo com a sua necessidade:

      - **Maven**, para o suporte de Assistente de criação do projeto Scala

      -  **SBT**, para gerir as dependências e a criação do projeto Scala 

      ![Criar um projeto de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Selecione **seguinte**.     
 
3. As próximas **novo projeto** janela, efetue o seguinte procedimento:

   ![Selecione o Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Introduza um nome de projeto e a localização do projeto.

   b. No **projeto SDK** na lista pendente, selecione **1.8 do Java** para **Spark 2** cluster ou selecione **Java 1.7** para **Spark 1. x**  cluster.

   c. No **Spark versão** na lista pendente, o Assistente de criação de projeto Scala integra-se a versão correta para o SDK do Spark e Scala SDK. Se a versão de cluster do spark é anterior ao 2.0, selecione **Spark 1. x**. Caso contrário, selecione **Spark 2. x.** Este exemplo utiliza **Spark 2.0.2 (Scala 2.11.8)**.

   d. Selecione **Concluir**.

4. Selecione **src** > **principal** > **scala** para abrir o código no projeto. Este exemplo utiliza o **SparkCore_wasbloTest** script.

5. Para o acesso a **editar configurações** menu, selecione o ícone no canto superior direito. Este menu, pode criar ou editar as configurações para depuração remota.

   ![Editar configurações](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

6. No **configurações de execução/depuração** diálogo caixa, selecione o sinal de adição (**+**). Em seguida, selecione o **submeter a tarefa do Spark** opção.

   ![Adicionar uma nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
7. Introduza as informações de **nome**, **cluster do Spark**, e **nome da classe principal**. Em seguida, selecione **configuração avançada**. 

   ![Execute as configurações de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

8. No **Spark submissão avançada configuração** caixa de diálogo, selecione **depuração remota ativar Spark**. Introduza o nome de utilizador do SSH e, em seguida, introduza uma palavra-passe ou utilizar um ficheiro de chave privada. Para guardar a configuração, selecione **OK**.

   ![Ativar depuração remota do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

9. Agora, a configuração é guardada com o nome que forneceu. Para ver os detalhes da configuração, selecione o nome de configuração. Para efetuar alterações, selecione **editar configurações**. 

10. Depois de concluir as definições de configurações, pode executar o projeto de clusters remotos ou efetuar a depuração remota.

## <a name="learn-how-to-perform-remote-debugging"></a>Saiba como efetuar a depuração remota
### <a name="scenario-1-perform-remote-run"></a>Cenário 1: Executar execução remota

Nesta secção, vamos mostrar-lhe como controladores e executores de depuração.

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. Configurar pontos de quebra e, em seguida, selecione o **depurar** ícone.

   ![Selecione o ícone de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. Quando a execução do programa atinge o ponto de quebra, verá um **controlador** separador e dois **Executor** nos separadores o **depurador** painel. Selecione o **retomar programa** ícone para continuar a executar o código, que, em seguida, atinge o próximo ponto de interrupção e centra-se nos correspondente **Executor** separador. Pode rever os registos de execução do correspondente **consola** separador.

   ![Separador de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>Cenário 2: Executar depuração remota e da correção de erros
Nesta secção, vamos mostrar-lhe como atualizar dinamicamente o valor da variável utilizando a capacidade de depuração IntelliJ para uma correção simple. No exemplo de código seguinte, é emitida uma exceção porque o ficheiro de destino já existe.
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>Para efetuar a depuração remota e da correção de erros
1. Configurar dois de quebra de pontos e, em seguida, selecione o **depurar** ícone para iniciar o processo de depuração remoto.

2. O código para o primeiro ponto de quebra e as informações de variável e o parâmetro são apresentadas no **variáveis** painel. 

3. Selecione o **retomar programa** ícone para continuar. O código interrompe o segundo ponto. A exceção é detetada como esperado.

  ![Erro de Throw](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. Selecione o **retomar programa** ícone novamente. O **HDInsight Spark submissão** janela apresenta um erro de "Falha de execução da tarefa".

  ![Submissão de erro](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. Para atualizar dinamicamente o valor da variável utilizando o IntelliJ capacidade de depuração, selecione **depurar** novamente. O **variáveis** painel aparece novamente. 

6. Faça duplo clique de destino no **depurar** separador e, em seguida, selecione **definir valor**. Em seguida, introduza um novo valor para a variável. Em seguida, selecione **Enter** para guardar o valor. 

  ![Defina o valor](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. Selecione o **retomar programa** ícone para continuar a executar o programa. Nesta altura, sem exceção é detetada. Pode ver que o projeto é executada com êxito sem quaisquer exceções.

  ![Depurar sem exceção](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Passos seguintes
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Criar Scala projeto (vídeo): [criar aplicações do Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Toolkit do Azure de utilização para o IntelliJ depurar aplicações do Spark remotamente num cluster do HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Spark com BI: efetuar uma análise de dados interativa ao utilizar o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilize o Toolkit do Azure para o IntelliJ para criar Spark aplicações para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através da VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize as ferramentas do HDInsight para o IntelliJ com Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar as ferramentas do HDInsight na Azure Toolkit para Eclipse para criar Spark aplicações](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
