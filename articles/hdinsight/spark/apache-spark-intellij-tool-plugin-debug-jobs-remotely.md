---
title: "Toolkit do Azure para o IntelliJ: depurar aplicações remotamente no HDInsight Spark | Microsoft Docs"
description: "Saiba como utilizar as ferramentas do HDInsight na Azure Toolkit para o IntelliJ remotamente depurar aplicações do Spark que são executadas em clusters do HDInsight através de VPN."
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: maxluk
ms.openlocfilehash: 9300973e4d3311c487179b41c4a298b1bda9ad28
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente no HDInsight através de VPN

Recomendamos a depuração de aplicações do spark remotamente através de SSH. Para obter instruções, consulte [remotamente depurar aplicações do Spark num cluster do HDInsight com o Toolkit do Azure para o IntelliJ através de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Este artigo fornece orientações passo a passo sobre como utilizar as ferramentas do HDInsight na Azure Toolkit para o IntelliJ para submeter uma tarefa de Spark num cluster do Spark do HDInsight e, em seguida, depurá-lo remotamente a partir do seu computador de secretária. Para concluir estas tarefas, tem de efetuar os seguintes passos de alto nível:

1. Crie um site para site ou ponto a site Azure virtual network. Os passos neste documento partem do princípio de que utiliza uma rede de site para site.
2. Crie um cluster do Spark no HDInsight que faz parte da rede virtual site a site.
3. Verificar a conectividade entre o nó principal do cluster e de ambiente de trabalho.
4. Criar uma aplicação de Scala no IntelliJ IDEA e, em seguida, configurá-la para depuração remota.
5. Executar a aplicação e Depure.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Para obter mais informações, consulte [obtenha uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um cluster do Apache Spark no HDInsight**. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Kit de desenvolvimento Java Oracle**. Pode instalá-lo do [site Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* **O IntelliJ IDEA**. Este artigo utiliza a versão 2017.1. Pode instalá-lo do [JetBrains site](https://www.jetbrains.com/idea/download/).
* **As ferramentas do HDInsight no Toolkit do Azure para o IntelliJ**. As ferramentas do HDInsight para o IntelliJ estão disponíveis como parte do Toolkit do Azure para o IntelliJ. Para obter instruções sobre como instalar o Toolkit do Azure, consulte [instalar o Toolkit do Azure para o IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Iniciar sessão na sua subscrição do Azure de IntelliJ IDEA**. Siga as instruções em [Toolkit do Azure de utilização para o IntelliJ criar Spark aplicações para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md).
* **Solução de exceção**. Ao executar a aplicação do Spark Scala para depuração remota num computador Windows, poderá obter uma exceção. Esta exceção é explicada no [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) e ocorre devido a um ficheiro WinUtils.exe em falta no Windows. Para contornar este erro, terá [transferir o ficheiro executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) numa localização como **C:\WinUtils\bin**. Adicionar um **HADOOP_HOME** variável de ambiente e, em seguida, defina o valor da variável para **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Passo 1: Criar uma Azure virtual network
Siga as instruções das ligações seguintes para criar uma Azure virtual network e, em seguida, verifique a conectividade entre o computador de secretária e a rede virtual:

* [Criar uma VNet com uma ligação de VPN de site a site utilizando o portal do Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Criar uma VNet com uma ligação de VPN de site para site com o PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configurar uma ligação ponto a site a uma rede virtual com o PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Passo 2: Criar um cluster do Spark do HDInsight
É recomendável criar um cluster do Apache Spark no Azure HDInsight que faz parte da rede virtual do Azure que criou. Utilize as informações disponíveis no [baseado em Linux criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Como parte da configuração opcional, selecione a rede virtual do Azure que criou no passo anterior.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Passo 3: Verificar a conectividade entre o nó principal do cluster e de ambiente de trabalho
1. Obter o endereço IP do nó principal. Abra a IU do Ambari para o cluster. A partir do painel do cluster, selecione **Dashboard**.

    ![Selecione o Dashboard no Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. Na IU do Ambari, selecione **anfitriões**.

    ![Selecione os anfitriões em Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. É apresentada uma lista de nós principais, nós de trabalho e nós de zookeeper. Os nós principais têm um **hn*** prefixo. Selecione o primeiro nó principal.

    ![Localizar o nó principal no Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. Do **resumo** painel na parte inferior da página que abre, copiar o **endereço IP** do nó principal e o **Hostname**.

    ![Localizar o endereço IP no Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. Adicione o endereço IP e o nome de anfitrião do nó principal para o **anfitriões** ficheiro no computador onde pretende executar e depurar remotamente a tarefa de Spark. Isto permite-lhe comunicar com o nó principal utilizando o endereço IP, bem como o nome do anfitrião.

   a. Abra um ficheiro do bloco de notas com permissões elevadas. Do **ficheiro** menu, selecione **abra**e, em seguida, localizar a localização do ficheiro de anfitriões. Num computador Windows, a localização é **C:\Windows\System32\Drivers\etc\hosts**.

   b. Adicione as seguintes informações para o **anfitriões** ficheiro:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. No computador ligado à rede virtual do Azure que é utilizada pelo cluster do HDInsight, certifique-se de que consegue enviar pings para os nós principais, utilizando o endereço IP, bem como o nome do anfitrião.
7. Utilizar o SSH para ligar ao nó principal do cluster, seguindo as instruções em [ligar a um cluster do HDInsight utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). O principal do nó do cluster, executar um ping o endereço IP do computador de secretária. Teste a conectividade com ambos os endereços IP atribuídos ao computador:

    - Uma para a ligação de rede
    - Uma para a Azure virtual network

8. Repita os passos para outro nó principal.

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Passo 4: Criar uma aplicação do Spark Scala utilizando as ferramentas do HDInsight do Toolkit do Azure para o IntelliJ e configurá-la para depuração remota
1. Abra o IntelliJ IDEA e criar um novo projeto. No **novo projeto** diálogo caixa, efetue o seguinte:

    ![Selecione o novo modelo de projeto na IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.

    b. Selecione **seguinte**.
2. As próximas **novo projeto** , efetue o seguinte procedimento e, em seguida, selecione **concluir**:

    - Introduza um nome de projeto e localização.

    - No **projeto SDK** na lista pendente, selecione **1.8 do Java** para o cluster do Spark 2. x ou selecione **Java 1.7** para o cluster do Spark 1. x.

    - No **versão Spark** na lista pendente, o Assistente de criação de projeto Scala integra-se a versão correta para o SDK do Spark e o SDK Scala. Se a versão de cluster do Spark é anterior ao 2.0, selecione **Spark 1. x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Selecione a versão do SDK e Spark do projeto](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
3. O projeto de Spark cria automaticamente um artefacto para si. Para ver o artefacto, efetue o seguinte:

    a. Do **ficheiro** menu, selecione **estrutura do projeto**.

    b. No **estrutura do projeto** caixa de diálogo, selecione **artefactos** para ver o artefacto predefinido que é criado. Também pode criar os seus artefactos selecionando o sinal de adição (**+**).

   ![Criar JAR](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


4. Adicione bibliotecas ao seu projeto. Para adicionar uma biblioteca, efetue o seguinte:

    a. Faça duplo clique no nome do projeto na árvore de projeto e, em seguida, selecione **abra as definições do módulo**. 

    b. No **estrutura do projeto** caixa de diálogo, selecione **bibliotecas**, selecione o (**+**) do símbolo e, em seguida, selecione **do Maven** .

    ![Adicionar uma biblioteca](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. No **transferir biblioteca do repositório Maven** diálogo caixa, procure e adicione as bibliotecas seguintes:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. Cópia `yarn-site.xml` e `core-site.xml` do cluster head nó e adicioná-los para o projeto. Utilize os seguintes comandos para copiar os ficheiros. Pode utilizar [Cygwin](https://cygwin.com/install.html) para executar o seguinte `scp` comandos para copiar os ficheiros do cluster de nós principais:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Porque já foi adicionado o endereço IP de nó principal do cluster e os nomes de anfitrião para o ficheiro de anfitriões no ambiente de trabalho, podemos utilizar o `scp` comandos da seguinte forma:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Para adicionar estes ficheiros ao seu projeto, copiá-los sob a **/src** pasta na sua árvore de projeto, por exemplo `<your project directory>\src`.
6. Atualização do `core-site.xml` ficheiro para as seguintes alterações:

   a. Substitua a chave encriptada. O `core-site.xml` ficheiro inclui a chave encriptada para a conta de armazenamento associada com o cluster. No `core-site.xml` ficheiro que adicionou ao projeto, substitua a chave encriptada com a chave de armazenamento real associada à conta de armazenamento predefinido. Para obter mais informações, consulte [gerir as chaves de acesso de armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. Remover as seguintes entradas de `core-site.xml`:

           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>

           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>

           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   c. Guarde o ficheiro.
7. Adicione a classe principal para a sua aplicação. Do **Explorador de projeto**, faça duplo clique **src**, aponte para **novo**e, em seguida, selecione **Scala classe**.

    ![Seleccione a classe principal](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
8. No **criar uma nova classe de Scala** diálogo caixa, forneça um nome, selecione **objeto** no **tipo** caixa e, em seguida, selecione **OK**.

    ![Criar nova classe de Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
9. No `MyClusterAppMain.scala` ficheiro, cole o seguinte código. Este código cria o Spark contexto e abre um `executeJob` método o `SparkSample` objeto.

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasb:///HVACOut")
          }
        }

10. Repita os passos 8 e 9 para adicionar um novo objeto de Scala chamado `*SparkSample`. Adicione o seguinte código para esta classe. Este código lê os dados de HVAC.csv (disponível em todos os clusters do HDInsight Spark). Obtém as linhas que têm apenas um dígito na coluna seventh no ficheiro CSV e, em seguida, escreve a saída para **/HVACOut** sob o contentor de armazenamento predefinido para o cluster.

        import org.apache.spark.SparkContext

        object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)

           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)

           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }
        }
11. Repita a classe dos passos 8 e 9 para adicionar uma nova denominada `RemoteClusterDebugging`. Esta classe implementa a arquitetura de teste do Spark que é utilizada para depurar as aplicações. Adicione o seguinte código para o `RemoteClusterDebugging` classe:

        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }

     Existem alguns pontos importantes a ter em atenção:

      * Para `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, certifique-se de que a assemblagem de Spark JAR disponível no armazenamento de cluster no caminho especificado.
      * Para `setJars`, especifique a localização onde o artefacto JAR é criado. Normalmente, é `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
12. No`*RemoteClusterDebugging` classe, clique com botão direito do `test` palavra-chave e, em seguida, selecione **criar configuração de RemoteClusterDebugging**.

    ![Criar uma configuração remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

13. No **criar configuração de RemoteClusterDebugging** , forneça um nome para a configuração e, em seguida, selecione **testar tipo** como o **nome para o teste**. Deixe todos os outros valores como as definições predefinidas. Selecione **aplicar**e, em seguida, selecione **OK**.

    ![Adicione os detalhes de configuração](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
14. Deverá ver uma **remoto execute** configuração na lista pendente na barra de menus.

    ![A lista pendente execução remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Passo 5: Executar a aplicação no modo de depuração
1. No projeto IntelliJ IDEA, abra `SparkSample.scala` e criar um ponto de interrupção junto a `val rdd1`. No **criar ponto de interrupção para** menu de pop-up, selecione **linha na função executeJob**.

    ![Adicionar um ponto de interrupção](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. Para executar a aplicação, selecione o **depurar executar** junto ao **remoto execute** configuração na lista pendente.

    ![Selecione o botão de depuração executar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. Quando a execução do programa atinge o ponto de interrupção, verá um **depurador** separador no painel inferior.

    ![Consulte o separador de depurador](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. Para adicionar uma veja, selecione o (**+**) ícone.

    ![Selecione o + ícone](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Neste exemplo, a aplicação quebrou antes da variável `rdd1` foi criado. Ao utilizar este veja, é possível ver as linhas primeiro cinco na variável `rdd`. Selecione **introduza**.

    ![Execute o programa no modo de depuração](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    O que vê na imagem anterior é no tempo de execução, poderá consultar terabytes de dados e de depuração como avança de ser a aplicação. Por exemplo, no resultado do mostrado na imagem anterior, pode ver que a primeira linha do resultado é um cabeçalho. Com base neste resultado, pode modificar o código da aplicação para ignorar a linha de cabeçalho, se necessário.
5. Agora, pode selecionar o **retomar programa** ícone para continuar com a sua aplicação em execução.

    ![Selecione o programa de retomar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. Se a aplicação for concluído com êxito, deverá ver um resultado como o seguinte:

    ![Saída da consola](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

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
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilize as ferramentas do HDInsight para o IntelliJ com Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar as ferramentas do HDInsight na Azure Toolkit para Eclipse para criar Spark aplicações](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utilize blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter num cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas que são executados num cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
