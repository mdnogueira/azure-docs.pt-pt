---
title: "Tutorial do Apache Storm: Introdução ao Storm baseado em Linux no HDInsight | Microsoft Docs"
description: "Comece com a análise de macrodados com os exemplos do Apache Storm e do Storm Starter no HDInsight baseado em Linux. Saiba como utilizar o Storm para o processamento de dados em tempo real."
keywords: "apache storm,tutorial do apache storm,análise de macrodados,storm starter"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7ade85d98c41bddddb61fe75d212b351ff62eef0
ms.openlocfilehash: 1e24cebace0e6b7de7ae318f06d6430b3caa342a


---
# <a name="apache-storm-tutorial-get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Tutorial do Apache Storm: Introdução aos exemplos do Storm Starter para a análise de macrodados no HDInsight

O Apache Storm é um sistema de cálculo dimensionável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Storm no Azure HDInsight, pode criar um cluster do Storm baseado na nuvem que executa a análise de macrodados em tempo real.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight Deprecation on Windows (Descontinuação do HDInsight no Windows)](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Tem de ter o seguinte para concluir com êxito este tutorial do Apache Storm:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Estar familiarizado com o SSH e o SCP**. Para obter mais informações sobre a utilização do SSH e do SCP com o HDInsight, consulte o seguinte:
  
    * **Clientes Linux, Unix ou OS X**: ver [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Clientes Windows**: Consulte [Utilizar o SSH (PuTTY) com o Hadoop baseado em Linux no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>Requisitos do controlo de acesso

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Criar um cluster do Storm

Nesta secção, vai criar um cluster do HDInsight versão 3.5 (versão 1.0.1 do Storm) através de um modelo do Azure Resource Manager. Para obter informações sobre as versões do HDInsight e os respetivos SLAs, consulte [Controlo de versões de componentes do HDInsight](hdinsight-component-versioning.md). Para obter outros métodos de criação de clusters, consulte [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para abrir o modelo no portal do Azure.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight-35.json" target="_blank"><img src="./media/hdinsight-apache-storm-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    O modelo está localizado num contentor de blobs público, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*. 

2. No painel __Implementação personalizada__, introduza o seguinte:
   
    * __Grupo de recursos__: grupo de recursos em que o cluster é criado.

    * **Nome do cluster**: nome para o cluster de Hadoop.

    * __Nome e palavra-passe de início de sessão do cluster__: o nome de início de sessão predefinido é __admin__.
    
    * __Nome de utilizador SSH__ e __palavra-passe__: utilizador e palavra-passe para ligar ao cluster utilizando SSH.

    * __Localização__: localização geográfica do cluster.
     
     Anote estes valores.  Serão necessários mais tarde no tutorial.
     
     > [!NOTE]
     > O SSH é utilizado para aceder remotamente ao cluster do HDInsight com uma linha de comandos. O nome de utilizador e a palavra-passe que utilizar aqui são utilizados aquando da ligação ao cluster através de SSH. Além disso, o nome de utilizador do SSH tem de ser exclusivo, pois cria uma conta de utilizador em todos os nós do cluster do HDInsight. Seguem-se alguns dos nomes de conta reservados para utilização pelos serviços do cluster e que não podem ser utilizados como nomes de utilizador do SSH:
     > 
     > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.
     > 
     > Para obter mais informações sobre a utilização do SSH com o HDInsight, consulte um dos seguintes artigos:
     > 
     > * [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
     > * [Utilizar o SSH (PuTTY) com o Hadoop baseado em Linux no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Selecione __Aceito os termos e condições acima apresentados__, clique em **OK** e, em seguida, selecione __Afixar ao dashboard__

6. Clique em **Comprar**. Verá um novo mosaico intitulado Submeter a implementação para a implementação do Modelo. A criação de um cluster demora cerca de 20 minutos.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Executar um exemplo do Storm Starter no HDInsight

Os exemplos [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) estão incluídos no cluster do HDInsight. Nos passos seguintes, executará o exemplo WordCount.

1. Ligar ao cluster do HDInsight com o SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Se utilizou uma palavra-passe para proteger a sua conta de utilizador do SSH, ser-lhe-á solicitado que a introduza. Se utilizou uma chave pública, poderá ter de utilizar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Para obter mais informações sobre a utilização do SSH com o HDInsight baseado em Linux, consulte os seguintes artigos:
   
    * [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar o SSH (PuTTY) com o Hadoop baseado em Linux no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Utilize o seguinte comando para iniciar uma topologia de exemplo:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > Em versões anteriores do HDInsight, o nome da classe da topologia é `storm.starter.WordCountTopology` em vez de `org.apache.storm.starter.WordCountTopology`.
   
    Deste modo, é iniciada a topologia do WordCount de exemplo no cluster, com o nome amigável “wordcount”. Irá gerar aleatoriamente frases e contar a ocorrência de cada palavra nas frases.
   
    > [!NOTE]
    > Ao submeter as topologias para o cluster, primeiro, tem de copiar o ficheiro jar que contém o cluster antes de utilizar o comando `storm`. Isto pode ser efetuado utilizando o comando `scp` a partir do cliente onde o ficheiro existe. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > O exemplo de WordCount e outros exemplos de storm starter já estão incluídos no seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Se estiver interessado em visualizar a origem para os exemplos de inicio de storm, pode encontrar o código em [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter). Esta ligação é para o Storm 1.0.x, que é fornecido com o HDInsight 3.5. Para outras versões do Storm, utilize o botão __Ramo__ na parte superior da página para selecionar uma versão diferente do Storm.

## <a name="monitor-the-topology"></a>Monitorizar a topologia

A IU do Storm fornece uma interface Web para trabalhar com topologias em execução e está incluída no cluster do HDInsight.

Siga estes passos para monitorizar a topologia através da IU do Storm:

1. Abra um browser para https://CLUSTERNAME.azurehdinsight.net/stormui, sendo que **CLUSTERNAME** é o nome do cluster. Esta ação irá abrir a IU do Storm.
    
    > [!NOTE]
    > Se lhe for pedido que forneça um nome de utilizador e uma palavra-passe, introduza o administrador do cluster (admin) e a palavra-passe que utilizou ao criar o cluster.

2. Em **Resumo da topologia**, selecione a entrada **wordcount** na coluna **Nome**. Deste forma, são apresentadas mais informações sobre a topologia.
    
    ![Dashboard do Storm com informações de topologia do WordCount do Storm Starter.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
    
    Esta página fornece as seguintes informações:
    
    * **Estatísticas de topologia** – informações básicas sobre o desempenho da topologia, organizadas em intervalos de tempo.
     
        > [!NOTE]
        > Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.

    * **Spouts** – informações básicas sobre spouts, incluindo o último erro devolvido por cada spout.
    
    * **Bolts** – informações básicas sobre bolts.
    
    * **Configuração da topologia** – informações detalhadas sobre a configuração da topologia.
     
    Esta página também fornece ações que podem ser efetuadas na topologia:
   
    * **Ativar** – retoma o processamento de uma topologia desativada.
    
    * **Desativar** – coloca em pausa uma topologia em execução.
    
    * **Rebalancear** – ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. Isto permite que a topologia ajuste o paralelismo para compensar o número maior/menor de nós no cluster. Para obter mais informações, consulte [Compreender o paralelismo de uma topologia do Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
    
    * **Eliminar** – termina uma topologia do Storm após o tempo limite especificado.

3. Nesta página, selecione uma entrada da secção **Spouts** ou **Bolts**. Esta ação apresenta informações sobre o componente selecionado.
   
    ![Dashboard do Storm com informações sobre os componentes selecionados.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    Esta página apresenta as seguintes informações:
   
    * **Estatísticas de Spout/Bolt** – Informações básicas sobre o desempenho dos componentes, organizadas em intervalos de tempo.
     
        > [!NOTE]
        > Selecionar um intervalo de tempo específico altera o intervalo de tempo das informações apresentadas noutras secções da página.
     
    * **Estatísticas de entrada** (apenas bolt) – Informações sobre componentes que produzem os dados consumidos pelo bolt.
    
    * **Estatísticas de saída** – Informações sobre dados emitidos por este bolt.
    
    * **Executores** – Informações sobre as instâncias deste componente.
    
    * **Erros** – Erros produzidos por este componente.

4. Ao visualizar os detalhes de um spout ou bolt, selecione uma entrada na coluna **Porta** na secção **Executores** para ver os detalhes de uma instância específica do componente.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    A partir destes dados, pode ver que a palavra **sete** ocorreu 1493957 vezes. Tal refere-se ao número de vezes que foi encontrada desde que esta topologia foi iniciada.

## <a name="stop-the-topology"></a>Parar a topologia

Regresse à página **Resumo da topologia** para aceder à topologia da contagem de palavras e, em seguida, selecione o botão **Eliminar** na secção **Ações de topologia**. Quando lhe for solicitado, introduza 10 para os segundos a aguardar antes de parar a topologia. Após o período de tempo limite, a topologia deixará de ser apresentada quando visitar a secção **IU do Storm** do dashboard.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="a-idnextanext-steps"></a><a id="next"></a>Passos seguintes

Neste tutorial sobre o Apache Storm, utilizou o Storm Starter para saber como criar um Storm no cluster do HDInsight e utilizar o Storm Dashboard para implementar, monitorizar e gerir topologias do Storm. Seguidamente, saiba como [Desenvolver topologias baseadas em Java com o Maven](hdinsight-storm-develop-java-topology.md).

Se já estiver familiarizado com o desenvolvimento de topologias baseadas em Java e pretender implementar uma topologia existente no HDInsight, consulte [Implementar e gerir topologias do Apache Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md).

Se for um programador do .NET, pode criar topologias C# ou C#/Java híbridas com o Visual Studio. Para obter mais informações, veja [Develop C# topologies for Apache Storm on HDInsight using Hadoop tools for Visual Studio (Desenvolver topologias C# para Apache Storm no HDInsight com ferramentas do Hadoop para o Visual Studio)](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Para obter topologias de exemplo que podem ser utilizadas com Storm no HDInsight, veja os exemplos seguintes:

* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/



<!--HONumber=Jan17_HO3-->


