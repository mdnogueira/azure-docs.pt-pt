---
title: "Introdução ao Apache Storm no Azure HDInsight | Microsoft Docs"
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
ms.date: 03/17/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 4787928ed066b9aed51a8512deeda6cd49897d82
ms.lasthandoff: 04/11/2017

---
#<a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-linux-based-hdinsight"></a>Introdução aos exemplos do Storm Starter para a análise de macrodados no HDInsight baseado no Linux

O Apache Storm é um sistema de cálculo dimensionável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Storm no Azure HDInsight, pode criar um cluster do Storm baseado na nuvem que executa a análise de macrodados em tempo real.

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight Deprecation on Windows (Descontinuação do HDInsight no Windows)](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Estar familiarizado com o SSH e o SCP**. Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="access-control-requirements"></a>Requisitos do controlo de acesso

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Criar um cluster do Storm

Utilize os seguintes passos para criar um Storm num cluster HDInsight:

1. No [portal do Azure](https://portal.azure.com), selecione **+ NOVO**, **Inteligência + Análise**e, em seguida, selecione **HDInsight**.

    ![Criar um cluster HDInsight](./media/hdinsight-apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. No painel **Noções Básicas**, introduza as seguintes informações:

    * **Nome do Cluster**: o nome do cluster HDInsight.
    * **Subscrição**: selecione a subscrição que pretende utilizar.
    * **Nome de utilizador de início de sessão do cluster** e **Palavra-passe de início de sessão do cluster**: o início de sessão ao aceder ao cluster através de HTTPS. Utilize estas credenciais para aceder aos serviços, como a IU Web do Ambari ou a API REST.
    * **Nome de utilizador do Secure Shell (SSH)**: o início de sessão utilizado ao aceder ao cluster através de SSH. Por predefinição, a palavra-passe é igual à palavra-passe de início de sessão do cluster.
    * **Grupo de Recursos**: o grupo de recursos em que o cluster é criado.
    * **Localização**: a região do Azure em que o cluster é criado.

    ![Selecionar subscrição](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. Selecione **Tipo de cluster**e, em seguida, defina os seguintes valores no painel **Configuração de cluster**:

    * **Tipo de Cluster**: Storm

    * **Sistema operativo**: Linux

    * **Versão**: Storm 1.0.1 (HDI 3.5)

    * **Escalão do Cluster**: Standard

    Por fim, utilize o botão **Selecionar** para guardar as definições.

    ![Selecionar tipo de cluster](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. Depois de selecionar o tipo de cluster, utilize o botão __Selecionar__ para definir o tipo de cluster. Em seguida, utilize o botão __Seguinte__ para concluir a configuração básica.

5. No painel **Armazenamento**, selecione ou crie uma Conta de armazenamento. Para seguir os passos neste documento, deixe os outros campos neste painel com os valores predefinidos. Utilize o botão __Seguinte__ para guardar a configuração do armazenamento.

    ![Configurar as definições de conta de armazenamento do HDInsight](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. No painel **Resumo**, reveja a configuração do cluster. Utilize as ligações __Editar__ para alterar quaisquer definições que estejam incorretas. Por fim, utilize o botão the__Create__ para criar o cluster.

    ![Resumo da configuração do cluster](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)

    > [!NOTE]
    > A criação do cluster pode demorar até 20 minutos.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Executar um exemplo do Storm Starter no HDInsight

1. Ligar ao cluster do HDInsight com o SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Se utilizou uma palavra-passe para proteger a sua conta de utilizador do SSH, é solicitado que a introduza. Se utilizou uma chave pública, poderá ter de utilizar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para iniciar uma topologia de exemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount

    > [!NOTE]
    > Em versões anteriores do HDInsight, o nome da classe da topologia é `storm.starter.WordCountTopology` em vez de `org.apache.storm.starter.WordCountTopology`.

    Deste modo, este comando inicia a topologia do WordCount de exemplo no cluster, com o nome amigável “wordcount”. Gera aleatoriamente frases e contar a ocorrência de cada palavra nas frases.

    > [!NOTE]
    > Ao submeter as topologias para o cluster, primeiro, tem de copiar o ficheiro jar que contém o cluster antes de utilizar o comando `storm`. Utilize o comando `scp` para copiar o ficheiro. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > O exemplo de WordCount e outros exemplos de storm starter já estão incluídos no seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Se estiver interessado em visualizar a origem para os exemplos de inicio de storm, pode encontrar o código em [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter). Esta ligação é para o Storm 1.0.x, que é fornecido com o HDInsight 3.5. Para outras versões do Storm, utilize o botão __Ramo__ na parte superior da página para selecionar uma versão diferente do Storm.

## <a name="monitor-the-topology"></a>Monitorizar a topologia

A IU do Storm fornece uma interface Web para trabalhar com topologias em execução e está incluída no cluster do HDInsight.

Siga estes passos para monitorizar a topologia através da IU do Storm:

1. Para apresentar a IU congestionamento, abra um browser e intrduza https://CLUSTERNAME.azurehdinsight.net/stormui. Substitua **CLUSTERNAME** pelo nome do cluster.

    > [!NOTE]
    > Se lhe for pedido que forneça um nome de utilizador e uma palavra-passe, introduza o administrador do cluster (admin) e a palavra-passe que utilizou ao criar o cluster.

2. Em **Resumo da topologia**, selecione a entrada **wordcount** na coluna **Nome**. São apresentadas mais informações sobre a topologia.

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

    * **Rebalancear** – ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. O rebalanceamento ajusta o paralelismo para compensar o número maior/menor de nós no cluster. Para obter mais informações, consulte [Compreender o paralelismo de uma topologia do Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    * **Eliminar** – termina uma topologia do Storm após o tempo limite especificado.

3. Nesta página, selecione uma entrada da secção **Spouts** ou **Bolts**. São apresentadas informações sobre o componente selecionado.

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

    Neste exemplo, a palavra **sete** ocorreu 1493957 vezes. Esta contagem refere-se ao número de vezes que a palavra foi encontrada desde que esta topologia foi iniciada.

## <a name="stop-the-topology"></a>Parar a topologia

Regresse à página **Resumo da topologia** para aceder à topologia da contagem de palavras e, em seguida, selecione o botão **Eliminar** na secção **Ações de topologia**. Quando lhe for solicitado, introduza 10 para os segundos a aguardar antes de parar a topologia. Após o período de tempo limite, a topologia deixará de ser apresentada quando visitar a secção **IU do Storm** do dashboard.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a id="next"></a>Passos seguintes

Neste tutorial do Apache Storm, aprendeu as noções básicas de trabalhar com o Storm no HDInsight. Seguidamente, saiba como [Desenvolver topologias baseadas em Java com o Maven](hdinsight-storm-develop-java-topology.md).

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

