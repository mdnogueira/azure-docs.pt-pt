---
title: Utilize blocos de notas do Zeppelin com um cluster do Apache Spark no Azure HDInsight | Microsoft Docs
description: "Instruções passo a passo sobre como utilizar blocos de notas do Zeppelin com clusters do Apache Spark no Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: df489d70-7788-4efa-a089-e5e5006421e2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 19e363c9802d712937b4c1e9db550346e60a01c0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Utilize blocos de notas do Zeppelin com um cluster do Apache Spark no Azure HDInsight

Clusters do HDInsight Spark incluem blocos de notas do Zeppelin que pode utilizar para executar tarefas de Spark. Neste artigo, irá aprender a utilizar o bloco de notas do Zeppelin num cluster do HDInsight.

> [!NOTE]
> Notas do Zeppelin só estão disponíveis para 1.6.3 o Spark no HDInsight 3.5 e 2.1.0 o Spark no HDInsight 3.6.
>

**Pré-requisitos:**

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Iniciar um bloco de notas do Zeppelin
1. No painel de cluster do Spark, clique em **Cluster Dashboard**e, em seguida, clique em **bloco de notas do Zeppelin**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.
   
   > [!NOTE]
   > Também pode aceder o bloco de notas do Zeppelin para o cluster abrindo o seguinte URL no browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
2. Crie um novo bloco de notas. No painel do cabeçalho, clique em **bloco de notas**e, em seguida, clique em **criar nova nota**.
   
    ![Criar um novo bloco de notas do Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "criar um novo bloco de notas do Zeppelin")
   
    Introduza um nome para o bloco de notas e, em seguida, clique em **criar nota**.
3. Além disso, certifique-se que o cabeçalho de bloco de notas mostra um estado ligado. Esta é a designação por um ponto verde no canto superior direito.
   
    ![Estado de bloco de notas do Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "estado de bloco de notas do Zeppelin")
4. Carregue dados de exemplo para uma tabela temporária. Quando cria um cluster do Spark no HDInsight, o ficheiro de dados de exemplo, **hvac.csv**, é copiado para a conta do storage associada em **\HdiSamples\SensorSampleData\hvac**.
   
    No parágrafo vazio que é criado por predefinição no novo bloco de notas, cole o seguinte fragmento.
   
        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
   
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
   
    Prima **SHIFT + ENTER** ou clique em de **reproduzir** botão para o parágrafo executar o fragmento. O estado no canto direito do parágrafo deve progresso da PREPARADO, pendente em execução para CONCLUÍDO. A saída aparece na parte inferior do mesmo paragraph. A captura de ecrã tem o seguinte aspeto:
   
    ![Criar uma tabela temporária de dados não processados](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "criar uma tabela temporária de dados não processados")
   
    Também pode fornecer um título para cada parágrafo. No canto direito, clique o **definições** ícone e, em seguida, clique em **Mostrar título**.
5. Pode agora executar as instruções de Spark SQL no **AVAC** tabela. Cole a seguinte consulta num parágrafo de novo. A consulta obtém o ID de criação e a diferença entre o temperatures reais para cada compilação numa determinada data e de destino. Prima **SHIFT + ENTER**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    O **sql de %** instrução no início informa o bloco de notas para utilizar o interpretador Livy Scala.
   
    A seguinte captura de ecrã mostra a saída.
   
    ![Executar uma instrução de Spark SQL com o bloco de notas](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "executar uma instrução de Spark SQL com o bloco de notas")
   
     Clique nas opções de apresentação (realçadas retângulo) para alternar entre representações diferentes para o mesmo resultado. Clique em **definições** para escolher que consitutes os valores e chave na saída. Captura de ecrã acima utiliza **buildingID** como a chave e a média de **temp_diff** como o valor.
6. Também pode executar as instruções de Spark SQL utilizar variáveis na consulta. O fragmento seguinte mostra como definir uma variável, **Temp**, na consulta com os valores possíveis que pretende consultar com. Quando executar primeiro a consulta, um lista pendente é preenchido automaticamente com os valores especificados para a variável.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Cole este fragmento numa nova parágrafo e prima **SHIFT + ENTER**. A seguinte captura de ecrã mostra a saída.
   
    ![Executar uma instrução de Spark SQL com o bloco de notas](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "executar uma instrução de Spark SQL com o bloco de notas")
   
    Para consultas subsequentes, pode selecionar um novo valor de lista pendente e execute novamente a consulta. Clique em **definições** para escolher que consitutes os valores e chave na saída. Captura de ecrã acima utiliza **buildingID** como a chave, a média de **temp_diff** como valor, e **targettemp** como o grupo.
7. Reinicie o interpretador Livy para sair da aplicação. Para tal, abra definições interpretador, clicando a com sessão iniciada no nome de utilizador a partir do canto superior direito e, em seguida, clique em **interpretador**.
   
    ![Inicie o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "ramo de registo de saída")
8. Desloque-se às definições de interpretador Livy e, em seguida, clique em **reiniciar**.
   
    ![Reinicie o intepreter Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "reiniciar o intepreter Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como posso utilizar pacotes externos com o bloco de notas?
Pode configurar o bloco de notas do Zeppelin no cluster do Apache Spark no HDInsight (Linux) para utilizar pacotes externos, contribuíram de Comunidade que não estão incluído out-of-a-box no cluster. Pode pesquisar o [repositório Maven](http://search.maven.org/) para a lista completa de pacotes que estão disponíveis. Também pode obter uma lista de pacotes disponíveis a partir de outras origens. Por exemplo, uma lista completa dos pacotes contribuíram de Comunidade está disponível em [Spark pacotes](http://spark-packages.org/).

Neste artigo, irá ver como utilizar o [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pacote com o bloco de notas do Jupyter.

1. Abra as definições de interpretador. No canto superior direito, clique o com sessão iniciada no nome de utilizador e, em seguida, clique em **interpretador**.
   
    ![Inicie o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "ramo de registo de saída")
2. Desloque-se às definições de interpretador Livy e, em seguida, clique em **editar**.
   
    ![Alterar as definições do interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "alterar as definições do interpretador")
3. Adicionar uma nova chave denominada **livy.spark.jars.packages** e defina o respetivo valor no formato `group:id:version`. Deste modo, se pretender utilizar o [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) pacote, tem de definir o valor da chave a `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Alterar as definições do interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "alterar as definições do interpretador")
   
    Clique em **guardar** e, em seguida, reinicie o interpretador Livy.
4. **Sugestão**: Se pretender que compreender como chegar ao introduzir o valor da chave acima, eis como.
   
    a. Localize o pacote no repositório de Maven. Para este tutorial, utilizámos [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. A partir dos repositório, reúna os valores para **GroupId**, **ArtifactId**, e **versão**.
   
    ![Utilizar pacotes externos com o bloco de notas do Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "utilizar pacotes externos com o bloco de notas do Jupyter")
   
    c. Concatenar os três valores separados por ponto e vírgula (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde são guardadas as notas do Zeppelin?
Os blocos de notas do Zeppelin são guardados para a headnodes de cluster. Por isso, se não eliminar o cluster, os blocos de notas serão também eliminados. Se pretender manter os blocos de notas para utilização posterior em outros clusters, tem de exportá-las depois de concluir as tarefas em execução. Para exportar um bloco de notas, clique em de **exportar** ícone conforme mostrado na imagem abaixo.

![Transferir o bloco de notas](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "transferir o bloco de notas")

Isto guarda o bloco de notas como um ficheiro JSON na sua localização de transferência.

## <a name="livy-session-management"></a>Gestão de sessão Livy
Quando executa o primeiro parágrafo de código no seu bloco de notas do Zeppelin, uma nova sessão de Livy é criada no cluster do HDInsight Spark. Esta sessão é partilhada entre todos os blocos de notas do Zeppelin que cria posteriormente. Se por alguma razão Livy sessão é terminado (reinicialização do cluster, etc.), não será capaz de executar tarefas do bloco de notas do Zeppelin.

Nesse caso, tem de efetuar os seguintes passos antes de poder iniciar tarefas em execução de um bloco de notas do Zeppelin. 

1. Reinicie o interpretador Livy do bloco de notas do Zeppelin. Para tal, abra definições interpretador, clicando a com sessão iniciada no nome de utilizador a partir do canto superior direito e, em seguida, clique em **interpretador**.
   
    ![Inicie o interpretador](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "ramo de registo de saída")
2. Desloque-se às definições de interpretador Livy e, em seguida, clique em **reiniciar**.
   
    ![Reinicie o intepreter Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "reiniciar o intepreter Zeppelin")
3. Execute uma célula de código a partir de um bloco de notas do Zeppelin existente. Esta ação cria uma nova sessão de Livy no cluster do HDInsight.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 







