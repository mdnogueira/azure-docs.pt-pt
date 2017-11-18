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
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: 6f9259ae5e8f382c6714d468004624c2cbcbbc33
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depurar aplicações do Spark localmente ou remotamente num cluster do HDInsight com o Toolkit do Azure para o IntelliJ através de SSH

Este artigo fornece orientações passo a passo sobre como utilizar as ferramentas do HDInsight na Azure Toolkit para o IntelliJ para depurar aplicações remotamente num cluster do HDInsight. Para depurar o projeto, também pode ver o [aplicações de depuração Spark do HDInsight com o Toolkit do Azure para o IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) vídeo.

**Pré-requisitos**
* **As ferramentas do HDInsight no Toolkit do Azure para o IntelliJ**. Esta ferramenta faz parte do Toolkit do Azure para o IntelliJ. Para obter mais informações, consulte [instalar o Toolkit do Azure para o IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). E **Toolkit do Azure para o IntelliJ**. Utilize este toolkit para criar Spark aplicações para um cluster do HDInsight. Para obter mais informações, siga as instruções em [Toolkit do Azure de utilização para o IntelliJ criar Spark aplicações para um cluster do HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **SSH do HDInsight serviço com o nome de utilizador e palavra-passe gestão**. Para obter mais informações, consulte [ligar para o HDInsight (Hadoop), utilizando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [utilizar o SSH túnel para aceder à Ambari web IU, JobHistory, NameNode, Oozie e outras UIs web](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Saiba como efetuar local, executar e depuração
### <a name="scenario-1-create-a-spark-scala-application"></a>Cenário 1: Criar uma aplicação do Spark Scala 

1. Iniciar o IntelliJ IDEA e, em seguida, criar um projeto. No **novo projeto** diálogo caixa, efetue o seguinte:

   a. Selecione **HDInsight**. 

   b. Selecione um modelo Java ou Scala com base na sua preferência. Selecione entre as seguintes opções:

      - **O Spark no HDInsight (Scala)**

      - **O Spark no HDInsight (Java)**

      - **O Spark no HDInsight exemplo (Scala)**

      Este exemplo utiliza um **Spark no HDInsight exemplo (Scala)** modelo.

   c. No **ferramenta de compilação** lista, selecione um dos seguintes, de acordo com a sua necessidade:

      - **Maven**, para o suporte de Assistente de criação do projeto Scala

      -  **SBT**, para gerir as dependências e a criação do projeto Scala 

      ![Criar um projeto de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Selecione **seguinte**.     
 
2. As próximas **novo projeto** janela, efetue o seguinte procedimento:

   ![Selecione o Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Introduza um nome de projeto e a localização do projeto.

   b. No **projeto SDK** na lista pendente, selecione **1.8 do Java** para **Spark 2** cluster ou selecione **Java 1.7** para **Spark 1. x**  cluster.

   c. No **Spark versão** na lista pendente, o Assistente de criação de projeto Scala integra-se a versão correta para o SDK do Spark e Scala SDK. Se a versão de cluster do spark é anterior ao 2.0, selecione **Spark 1. x**. Caso contrário, selecione **Spark 2. x.** Este exemplo utiliza **Spark 2.0.2 (Scala 2.11.8)**.

   d. Selecione **Concluir**.

3. Selecione **src** > **principal** > **scala** para abrir o código no projeto. Este exemplo utiliza o **SparkCore_wasbloTest** script.

### <a name="prerequisite-for-windows"></a>Pré-requisitos para o windows
Enquanto estiver a executar a aplicação do Spark Scala local num computador Windows, poderá obter uma exceção, conforme explicado no [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils.exe está em falta no Windows. 

Para resolver este erro, [transferir o ficheiro executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) numa localização como **C:\WinUtils\bin**. Em seguida, adicione a variável de ambiente **HADOOP_HOME**e defina o valor da variável para **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Cenário 2: Executar a execução local
1. Abra o **SparkCore_wasbloTest** script, o editor de scripts com o botão direito e, em seguida, selecione a opção **executar '[Spark tarefa] XXX'** para efetuar a execução local.
2. Executar uma vez local foi concluída, pode ver o ficheiro de saída, guarde o Explorador de projeto atual **dados** > **__predefinido__**.

    ![Resultado da execução do local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. A nossa ferramentas definiu a predefinição configuração de execução local automaticamente quando efetuar local local e execução de depuração. Abra a configuração **[Spark tarefa] XXX** no canto superior direito, pode ver o **[Spark tarefa] XXX** já criados no **tarefa do Azure HDInsight Spark**. Mudar para **executar localmente** separador.

    ![Configuração de execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [As variáveis de ambiente](#prerequisite-for-windows): Se definir a variável de ambiente de sistema já **HADOOP_HOME** para **C:\WinUtils**, pode automaticamente detetar que não é necessário para adicionar manualmente.
    - [Localização de WinUtils.exe](#prerequisite-for-windows): Se não tiver definido a variável de ambiente de sistema, pode encontrar a localização ao clicar no respetivo botão.
    - Escolha apenas uma das duas opções, e que não são necessários no MacOS e Linux.
4. Também pode definir a configuração manualmente antes de efetuar a depuração de local e execução local. Captura de ecrã anterior, selecione o sinal de adição (**+**). Em seguida, selecione o **tarefa do Azure HDInsight Spark** opção. Introduza as informações de **nome**, **nome da classe principal** para guardar, em seguida, clique no botão de execução local.

### <a name="scenario-3-perform-local-debugging"></a>Cenário 3: Executar uma depuração local
1. Abra o **SparkCore_wasbloTest** script, pontos de interrupção do conjunto.
2. O editor de scripts com o botão direito e, em seguida, selecione a opção **depurar '[Spark tarefa] XXX'** para efetuar a depuração local.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Saiba como efetuar remoto execute e depuração
### <a name="scenario-1-perform-remote-run"></a>Cenário 1: Executar execução remota

1. Para o acesso a **editar configurações** menu, selecione o ícone no canto superior direito. Este menu, pode criar ou editar as configurações para depuração remota.

   ![Editar configurações](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. No **configurações de execução/depuração** diálogo caixa, selecione o sinal de adição (**+**). Em seguida, selecione o **tarefa do Azure HDInsight Spark** opção.

   ![Adicionar uma nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. Mudar para **executar remotamente num Cluster** separador. Introduza as informações de **nome**, **cluster do Spark**, e **nome da classe principal**. Em seguida, selecione **configuração avançada**. Suportam a nossa ferramentas de depuração com **executores**. O **numExectors**, o valor predefinido é 5. A seria melhor não definido como superior a 3.

   ![Execute as configurações de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. No **Spark submissão avançada configuração** caixa de diálogo, selecione **depuração remota ativar Spark**. Introduza o nome de utilizador do SSH e, em seguida, introduza uma palavra-passe ou utilizar um ficheiro de chave privada. Para guardar a configuração, selecione **OK**. Se pretender efetuar a depuração remota, terá de configurá-lo. Não é necessário para defini-lo se pretender utilizar remoto execute.

   ![Ativar depuração remota do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. Agora, a configuração é guardada com o nome que forneceu. Para ver os detalhes da configuração, selecione o nome de configuração. Para efetuar alterações, selecione **editar configurações**. 

6. Depois de concluir as definições de configurações, pode executar o projeto de clusters remotos ou efetuar a depuração remota.
   
   ![Botão de execução remoto](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. Se não pretender ver o registo em execução no painel à direita, pode clicar no **desligar** botão. No entanto, ainda está em execução no back-end e o resultado será apresentado no painel esquerdo.

   ![Botão de execução remoto](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Cenário 2: Executar a depuração remota
1. Configurar pontos de quebra e, em seguida, clique o **depuração remota** ícone. A diferença com submissão remoto é que precisem de nome de utilizador/palavra-passe do SSH de ser configurado.

   ![Selecione o ícone de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. Quando a execução do programa atinge o ponto de quebra, verá um **controlador** separador e dois **Executor** nos separadores o **depurador** painel. Selecione o **retomar programa** ícone para continuar a executar o código, que, em seguida, atinge o ponto de interrupção seguinte. Terá de mudar para o correto **Executor** separador para localizar o executor de destino para a depuração. Pode ver os registos de execução no correspondente **consola** separador.

   ![Separador de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Cenário 3: Executar depuração remota e da correção de erros
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
