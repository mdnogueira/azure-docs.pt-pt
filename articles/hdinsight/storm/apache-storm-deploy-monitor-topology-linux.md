---
title: Implementar e gerir topologias Apache Storm no HDInsight baseado em Linux | Microsoft Docs
description: Saiba como implementar, monitorizar e gerir topologias Apache Storm, utilizar o Dashboard Storm no HDInsight baseado em Linux. Utilize as ferramentas Hadoop para o Visual Studio.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 35086e62-d6d8-4ccf-8cae-00073464a1e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: larryfr
ms.openlocfilehash: a972344e2b6205fbcf69d2969c42211ec5b24869
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-manage-apache-storm-topologies-on-hdinsight"></a>Implementar e gerir topologias Apache Storm no HDInsight

Neste documento, aprender as noções básicas de gestão e monitorização topologias Storm em execução no Storm nos clusters do HDInsight.

> [!IMPORTANT]
> Os passos neste artigo requerem um Storm baseado em Linux num cluster do HDInsight. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). 
>
> Para obter informações sobre como implementar e monitorizar topologias no HDInsight baseado em Windows, consulte [implementar e gerir topologias Apache Storm no HDInsight baseado em Windows](apache-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>Pré-requisitos

* **Um Storm baseado em Linux no HDInsight cluster**: consulte [introdução ao Apache Storm no HDInsight](apache-storm-tutorial-get-started-linux.md) para obter os passos sobre como criar um cluster

* (Opcional) **Familiaridade com SSH e o SCP**: para obter mais informações, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcional) **Visual Studio**: Azure SDK 2.5.1 ou mais recente e as ferramentas do Data Lake para Visual Studio. Para obter mais informações, consulte [começar a utilizar as ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Uma das seguintes versões do Visual Studio:

  * Visual Studio 2012 com [atualização 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 com [atualização 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (qualquer edição)

  * Visual Studio 2017 (qualquer edição). Ferramentas do Data Lake para Visual Studio 2017 são instaladas como parte da carga de trabalho do Azure.

## <a name="submit-a-topology-visual-studio"></a>Submeter uma topologia: Visual Studio

As ferramentas do HDInsight pode ser utilizadas para submeter topologias de c# ou híbrida ao cluster do Storm. Os seguintes passos utilizarem uma aplicação de exemplo. Para obter informações sobre como criar utilizando as ferramentas do HDInsight, consulte [desenvolver topologias c# utilizando as ferramentas do HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Se já não instalou a versão mais recente das ferramentas do Data Lake para Visual Studio, consulte [começar a utilizar as ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > As ferramentas do Data Lake para Visual Studio chamavam-as ferramentas do HDInsight para Visual Studio.
    >
    > Ferramentas do Data Lake para Visual Studio são incluídas no __carga de trabalho do Azure__ para Visual Studio 2017.

2. Abra o Visual Studio, selecione **ficheiro** > **novo** > **projeto**.

3. No **novo projeto** diálogo caixa, expanda **instalada** > **modelos**e, em seguida, selecione **HDInsight**. Na lista de modelos, selecione **exemplo do Storm**. Na parte inferior da caixa de diálogo, escreva um nome para a aplicação.

    ![Imagem](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. No **Explorador de soluções**, clique com o botão direito no projeto e selecione **envio para Storm no HDInsight**.

   > [!NOTE]
   > Se lhe for solicitado, introduza as credenciais de início de sessão para a sua subscrição do Azure. Se tiver mais do que uma subscrição, inicie sessão no que contém o seu cluster Storm no HDInsight.

5. Selecione o seu cluster Storm no HDInsight do **Cluster do Storm** na lista pendente e, em seguida, selecione **submeter**. Pode monitorizar se a submissão é efetuada com êxito utilizando a **saída** janela.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Submeter uma topologia: SSH e o comando de Storm

1. Utilize o SSH para ligar ao cluster do HDInsight. Substitua **USERNAME** o nome de início de sessão SSH. Substitua **CLUSTERNAME** com o nome de cluster do HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obter mais informações sobre como utilizar o SSH para ligar ao cluster do HDInsight, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para iniciar uma topologia de exemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Este comando inicia a topologia do WordCount de exemplo no cluster. Esta topologia gera aleatoriamente frases, em seguida, contagens e a ocorrência de cada palavra nas frases.

   > [!NOTE]
   > Ao submeter a topologia para o cluster, primeiro, tem de copiar o ficheiro jar que contém o cluster antes de utilizar o comando `storm`. Para copiar o ficheiro para o cluster, pode utilizar o `scp` comando. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > O exemplo de WordCount e outros exemplos de storm starter já estão incluídos no seu cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Submeter uma topologia: através de programação

Pode implementar programaticamente uma topologia utilizando o serviço de Nimbus. [https://github.com/Azure-Samples/hdinsight-Java-Deploy-Storm-Topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fornece um exemplo de aplicação Java que demonstra como implementar e iniciar uma topologia através do serviço de Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitorizar e gerir: Visual Studio

Quando uma topologia for submetida com o Visual Studio, o **topologias Storm** é apresentada a vista. Selecione a topologia da lista para ver informações sobre a topologia em execução.

![monitor do Visual studio](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]
> Também pode ver **topologias Storm** de **Explorador de servidores** expandindo **Azure** > **HDInsight**e, em seguida, clicar um cluster Storm no HDInsight e selecionando **topologias do Storm vista**.

Selecione a forma spouts ou bolts para ver informações sobre estes componentes. Abre uma nova janela para cada item selecionado.

### <a name="deactivate-and-reactivate"></a>Desativar e reativar

Desativar uma topologia interrompe-lo até que seja cancelado ou reativado. Para efetuar estas operações, utilize o __desativar__ e __reativar__ botões no topo do __resumo da topologia__.

### <a name="rebalance"></a>Reequilibre

Reequilíbrio uma topologia permite que o sistema rever o paralelismo da topologia. Por exemplo, se tiver redimensionado o cluster para adicionar mais notas, reequilíbrio permite uma topologia ver os novos nós.

Uma topologia de reequilibrar, utilize o __rebalancear__ na parte superior da parte a __resumo da topologia__.

> [!WARNING]
> Reequilíbrio primeiro uma topologia desativa a topologia, em seguida, redistribui com trabalhadores uniformemente no cluster e, finalmente, devolve a topologia para o estado em que estava antes reequilíbrio ocorreu. Por isso, se a topologia estava ativa, torna-Active Directory novamente. Se foi desativada, continua a ser desativada.

### <a name="kill-a-topology"></a>Eliminar uma topologia

Topologias do Storm continuam em execução até que estão paradas ou o cluster é eliminado. Para parar uma topologia, utilize o __Kill__ na parte superior da parte a __resumo da topologia__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorizar e gerir: SSH e o comando de Storm

O `storm` utilitário permite-lhe trabalhar com topologias em execução na linha de comandos. Utilize `storm -h` para uma lista completa dos comandos.

### <a name="list-topologies"></a>Topologias de lista

Utilize o seguinte comando para listar todas as topologias em execução:

    storm list

Este comando devolve informações semelhantes ao texto seguinte:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Desativar e reativar

Desativar uma topologia interrompe-lo até que seja cancelado ou reativado. Utilize o seguinte comando para desativar e reativar:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

Storm as topologias, uma vez iniciadas, continuar em execução até parado. Para parar uma topologia, utilize o seguinte comando:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Reequilibre

Reequilíbrio uma topologia permite que o sistema rever o paralelismo da topologia. Por exemplo, se tiver redimensionado o cluster para adicionar mais notas, reequilíbrio permite uma topologia ver os novos nós.

> [!WARNING]
> Reequilíbrio primeiro uma topologia desativa a topologia, em seguida, redistribui com trabalhadores uniformemente no cluster e, finalmente, devolve a topologia para o estado em que estava antes reequilíbrio ocorreu. Por isso, se a topologia estava ativa, torna-Active Directory novamente. Se foi desativada, continua a ser desativada.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitorizar e gerir: IU do Storm

A IU do Storm fornece uma interface Web para trabalhar com topologias em execução e está incluída no cluster do HDInsight. Para ver a IU do Storm, utilize um web browser para abrir **https://CLUSTERNAME.azurehdinsight.net/stormui**, onde **CLUSTERNAME** é o nome do cluster.

> [!NOTE]
> Se lhe for pedido que forneça um nome de utilizador e uma palavra-passe, introduza o administrador do cluster (admin) e a palavra-passe que utilizou ao criar o cluster.

### <a name="main-page"></a>Página principal

Página principal da IU do Storm fornece as seguintes informações:

* **Resumo do cluster**: informações básicas sobre o cluster do Storm.
* **Resumo da topologia**: uma lista de execução de topologias. Utilize as hiperligações nesta secção para visualizar mais informações sobre topologias específicas.
* **Resumo do supervisor**: informações sobre o supervisor de Storm.
* **Configuração de nimbus**: configuração de Nimbus para o cluster.

### <a name="topology-summary"></a>Topologia de resumo

Selecionar uma ligação do **resumo da topologia** secção apresenta as seguintes informações sobre a topologia:

* **Resumo da topologia**: informações básicas sobre a topologia.
* **Ações de topologia**: ações de gestão que pode efetuar para a topologia.

  * **Ativar**: retoma o processamento de uma topologia desativada.
  * **Desativar**: pausa uma topologia em execução.
  * **Rebalancear**: ajusta o paralelismo da topologia. Deve rebalancear as topologias em execução depois de ter alterado o número de nós no cluster. Esta operação permite que a topologia ajuste o paralelismo para compensar o número maior ou menor de nós no cluster.

    Para obter mais informações, consulte <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Compreender o paralelismo de uma topologia do Storm</a>.
  * **Kill**: termina uma topologia do Storm após o tempo limite especificado.
* **Estatísticas de topologia**: estatísticas sobre a topologia. Para definir o período de tempo para as entradas restantes na página, utilize as ligações no **janela** coluna.
* **Spouts**: spouts utilizados pela topologia. Utilize as hiperligações nesta secção para visualizar mais informações sobre spouts específicos.
* **Bolts**: bolts utilizados pela topologia. Utilize as hiperligações nesta secção para visualizar mais informações sobre bolts específicos.
* **Configuração da topologia**: A configuração da topologia selecionada.

### <a name="spout-and-bolt-summary"></a>Spout e resumo Bolt

Selecionar um spout do **Spouts** ou **Bolts** secções apresenta as seguintes informações sobre o item seleccionado:

* **Resumo de componente**: informações básicas sobre o spout ou bolt.
* **Estatísticas de spout/Bolt**: estatísticas sobre a spout ou bolt. Para definir o período de tempo para as entradas restantes na página, utilize as ligações no **janela** coluna.
* **Estatísticas de entrada** (apenas bolt): informações sobre os fluxos de entrada consumidos pelo bolt.
* **Estatísticas de saída**: informações sobre os fluxos emitidos pelo spout ou bolt.
* **Executores**: informações sobre as instâncias do spout ou bolt. Selecione o **porta** produzidos de entrada para um executor específico ver um registo de informações de diagnóstico para esta instância.
* **Erros**: quaisquer informações de erro para o spout ou bolt.

## <a name="monitor-and-manage-rest-api"></a>Monitorizar e gerir: API de REST

A IU do Storm é desenvolvida com a API REST, pelo que pode efetuar a gestão e semelhantes funcionalidades de monitorização, utilizando a API REST. Pode utilizar a API REST para criar personalizadas ferramentas de gestão e monitorização topologias do Storm.

Para obter mais informações, consulte [API de REST de IU do Storm](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). As seguintes informações são específicas para utilizar a API REST com Apache Storm no HDInsight.

> [!IMPORTANT]
> A API de REST do Storm não está disponível publicamente através da internet e tem de ser acedida através de um túnel SSH ao nó principal do cluster do HDInsight. Para obter informações sobre como criar e utilizar um túnel SSH, consulte [utilize túnel SSH para aceder à IU da web do Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras web UIs](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

O URI base para a API de REST nos clusters do HDInsight baseado em Linux está disponível no nó principal no **https://HEADNODEFQDN:8744/api/v1/**. O nome de nó principal do domínio é gerado durante a criação do cluster e não é estático.

Pode encontrar o nome de domínio completamente qualificado (FQDN) para o nó principal do cluster de várias formas diferentes:

* **A partir de uma sessão SSH**: Utilize o comando `headnode -f` numa sessão SSH para o cluster.
* **Da Web do Ambari**: selecione **serviços** na parte superior da página, em seguida, selecione **Storm**. Do **resumo** separador, selecione **servidor de IU do Storm**. O FQDN do nó que aloja a IU do Storm e a REST API é apresentado na parte superior da página.
* **Da API de REST do Ambari**: Utilize o comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para obter informações sobre a IU do Storm e a REST API em execução no nó. Substitua **palavra-passe** com a palavra-passe de administrador para o cluster. Substitua **CLUSTERNAME** com o nome do cluster. Na resposta, a entrada de "host_name" contém o FQDN do nó.

### <a name="authentication"></a>Autenticação

Pedidos para a API REST têm de utilizar **autenticação básica**, por isso, utilize o nome de administrador de cluster do HDInsight e a palavra-passe.

> [!NOTE]
> Porque a autenticação básica é enviada através da utilização de texto não encriptado, deve **sempre** utilizar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

As informações que são devolvidas a partir da API de REST podem apenas ser utilizáveis partir do cluster. Por exemplo, o nome de domínio completamente qualificado (FQDN) para servidores de Zookeeper devolvido não está acessível a partir da Internet.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [topologias baseadas em Java desenvolver com o Maven](apache-storm-develop-java-topology.md).

Para obter uma lista das topologias de exemplo mais, consulte [topologias de exemplo para Storm no HDInsight](apache-storm-example-topology.md).
