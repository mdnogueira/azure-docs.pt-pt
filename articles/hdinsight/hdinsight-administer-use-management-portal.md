---
title: Gerir clusters do Hadoop baseados em Windows no HDInsight com o portal do Azure | Microsoft Docs
description: "Saiba mais sobre como administrar o serviço de HDInsight. Criar um cluster do HDInsight, abra a consola de JavaScript interativa e abra a consola de comandos do Hadoop."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 9295a988-bd88-453a-8c8b-55fa103bf39c
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: ecaad702843a63bb82b781339d25fde10df0a0a4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gerir clusters do Hadoop baseados em Windows no HDInsight ao utilizar o portal do Azure

Utilizar o [portal do Azure][azure-portal], pode criar clusters do Hadoop baseados em Windows no Azure HDInsight, alterar a palavra-passe de utilizador do Hadoop e ativar o protocolo RDP (Remote Desktop Protocol) para que possa aceder de comando de Hadoop consola no cluster.

As informações neste artigo aplica-se apenas a clusters do HDInsight baseado no Windows. Para obter informações sobre a gestão de clusters baseados em Linux, consulte [clusters do Hadoop gerir no HDInsight ao utilizar o portal do Azure](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Conta de armazenamento do Azure** -cluster do HDInsight um utiliza um contentor de armazenamento de Blobs do Azure como o sistema de ficheiros predefinido. Para obter mais informações sobre como o Blob storage do Azure fornece uma experiência totalmente integrada com clusters do HDInsight, consulte [utilizar armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter mais informações sobre como criar uma conta de armazenamento do Azure, consulte [como criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Abra o Portal
1. Inicie sessão no [https://portal.azure.com](https://portal.azure.com).
2. Depois de abrir o portal, pode:

   * Clique em **novo** no menu à esquerda para criar um novo cluster:

       ![novo botão de cluster do HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Clique em **Clusters do HDInsight** no menu esquerdo.

       ![Botão de cluster HDInsight portal do Azure](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Se **HDInsight** não são apresentados no menu à esquerda, clique em **procurar**.

     ![Botão de cluster procurar portal do Azure](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Criar clusters
Para as instruções de criação através do Portal, consulte [criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight funciona com um intervalo Hadoop wide componentes. Para obter a lista dos componentes que tenham sido verificado e suportados, consulte [qual a versão do Hadoop é no Azure HDInsight](hdinsight-component-versioning.md). Pode personalizar o HDInsight ao utilizar uma das seguintes opções:

* Utilize a ação de Script para executar scripts personalizados que podem personalizar um cluster para alterar a configuração de cluster ou instalar componentes personalizados, tais como Giraph ou Solr. Para obter mais informações, consulte [através da ação de Script de cluster de HDInsight personalizar](hdinsight-hadoop-customize-cluster.md).
* Utilize os parâmetros de personalização de cluster no Azure PowerShell ou de SDK .NET do HDInsight durante a criação do cluster. Estas alterações de configuração, em seguida, são mantidas através da duração do cluster e não são afetadas por reimages de nó de cluster que plataforma Azure executa periodicamente para manutenção. Para obter mais informações sobre como utilizar os parâmetros de personalização do cluster, consulte [criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Alguns componentes de Java nativas, como o Mahout e Cascading, podem ser executados no cluster como ficheiros JAR. Estes ficheiros JAR podem ser distribuídos ao Blob storage do Azure e submetidos para clusters do HDInsight através de mecanismos de submissão de tarefas do Hadoop. Para obter mais informações, consulte [Hadoop submeter as tarefas através de programação](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > Se tiver problemas de implementação de ficheiros JAR em clusters do HDInsight ou chamar JAR ficheiros nos clusters do HDInsight, contacte [Microsoft Support](https://azure.microsoft.com/support/options/).
  >
  > Cascata não é suportada pelo HDInsight e não é elegível para Support da Microsoft. Para apresenta uma lista dos componentes suportados, consulte [Novidades nas versões do cluster fornecidas pelo HDInsight](hdinsight-component-versioning.md).
  >
  >

Não é suportada a instalação de software personalizadas no cluster utilizando a ligação de ambiente de trabalho remoto. Deve evitar armazenar todos os ficheiros em unidades de nó principal, como estes serão perdidos se precisar de voltar a criar os clusters. Recomendamos o armazenamento de ficheiros no Blob storage do Azure. O blob storage é persistente.

## <a name="list-and-show-clusters"></a>Listar e Mostrar clusters
1. Inicie sessão no [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Clusters do HDInsight** no menu esquerdo.
3. Clique no nome do cluster. Se a lista de cluster for longa, pode utilizar o filtro no topo da página.
4. Faça duplo clique em cluster na lista para mostrar os detalhes.

    **Menu e essentials**:

    ![Essentials de cluster HDInsight portais do Azure](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Para personalizar o menu, clique com botão direito em qualquer lugar no menu e, em seguida, clique em **personalizar**.
   * **Definições** e **todas as definições**: apresenta o **definições** painel para o cluster, o que lhe permite aceder às informações detalhadas de configuração para o cluster.
   * **Dashboard**, **Cluster Dashboard** e **URL: estes são todas as formas para aceder ao dashboard do cluster, o que é Web do Ambari para clusters baseados em Linux. -**Secure Shell * *: Mostra as instruções para ligar ao cluster através de ligação de Secure Shell (SSH).
   * **Dimensionar o Cluster**: permite-lhe alterar o número de nós de trabalho para este cluster.
   * **Eliminar**: elimina o cluster.
   * **Início Rápido**: apresenta informações que irão ajudar a começar a utilizar o HDInsight.
   * * * Utilizadores: Permite-lhe definir as permissões para *portal management* deste cluster para outros utilizadores na sua subscrição do Azure.

     > [!IMPORTANT]
     > Isto *apenas* afeta o acesso e permissões para este cluster no portal do Azure e não tem qualquer efeito em que pode ligar a ou submeter tarefas ao cluster do HDInsight.
     >
     >
   * **Etiquetas**: as etiquetas permitem-lhe definir os pares chave-valor para definir uma taxonomia personalizada dos seus serviços em nuvem. Por exemplo, pode criar uma chave denominada **projeto**e, em seguida, utiliza um valor comum para todos os serviços associados a um projeto específico.
   * **As vistas Ambari**: ligações para Web do Ambari.

     > [!IMPORTANT]
     > Para gerir os serviços fornecidos pelo cluster do HDInsight, tem de utilizar Web do Ambari ou a API de REST do Ambari. Para obter mais informações sobre a utilização do Ambari, consulte [gerir clusters do HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Utilização**:

     ![Utilização de cluster HDInsight portal do Azure](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Clique em **definições**.

    ![Utilização de cluster HDInsight portal do Azure](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Propriedades**: ver as propriedades de cluster.
   * **Identidade do AAD do cluster**:
   * **Chaves de armazenamento do Azure**: ver a conta do storage predefinida e a respetiva chave. A conta de armazenamento é configuração durante o processo de criação do cluster.
   * **Início de sessão do cluster**: mudar o nome de utilizador do cluster HTTP e a palavra-passe.
   * **As Metastores externas**: ver as metastores do Hive e Oozie. As metastores só podem ser configuradas durante o processo de criação do cluster.
   * **Dimensionar o Cluster**: aumentar e diminuir o número de nós de trabalho de cluster.
   * **Ambiente de trabalho remoto**: Ativar e desativar o acesso de ambiente de trabalho remoto (RDP) e configurar o nome de utilizador do RDP.  O nome de utilizador do RDP tem de ser diferente do nome de utilizador HTTP.
   * **Parceiro de registo**:

     > [!NOTE]
     > Esta é uma lista das funcionalidades disponíveis; genérica nem todas estará presentes para todos os tipos de cluster.
     >
     >
6. Clique em **propriedades**:

    A secção de propriedades apresenta o seguinte:

   * **Nome de anfitrião**: nome do Cluster.
   * **URL de cluster**.
   * **Estado**: incluem abortada, aceite, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, operacional, executar, erro, eliminar, eliminar, ServiceHost DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, ClusterCustomization CertRolloverQueued, ResizeQueued,
   * **Região**: localização do Azure. Para obter uma lista de localizações do Azure suportadas, consulte o **região** caixa de lista pendente no [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Dados criados**.
   * **Sistema operativo**: O **Windows** ou **Linux**.
   * **Tipo**: Hadoop, HBase, Storm, Spark.
   * **Versão**. Consulte [as versões do HDInsight](hdinsight-component-versioning.md)
   * **Subscrição**: nome da subscrição.
   * **ID de subscrição**.
   * **Origem de dados principal**. A conta de armazenamento de Blobs do Azure utilizada como predefinição sistema de ficheiros Hadoop.
   * **Escalão de preço de nós de trabalho**.
   * **Escalão de preço de nó de HEAD**.

## <a name="delete-clusters"></a>Eliminar clusters
Eliminar um cluster não elimina a conta do storage predefinida ou quaisquer contas do storage ligadas. Pode voltar a criar o cluster utilizando as mesmas contas do storage e metastores as mesmas.

1. Iniciar sessão para o [Portal][azure-portal].
2. Clique em **Procurar tudo** no menu à esquerda, clique em **Clusters do HDInsight**, clique no nome do cluster.
3. Clique em **eliminar** no menu superior e, em seguida, siga as instruções.

Consulte também [pausa/encerramento clusters](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Dimensionar clusters
O cluster dimensionamento funcionalidade permite-lhe alterar o número de nós de trabalho utilizado por um cluster que está em execução no Azure HDInsight sem ter de recriar o cluster.

> [!NOTE]
> Apenas clusters do hdinsight versão 3.1.3 ou superior, são suportadas. Se não souber a versão do cluster, pode verificar a página de propriedades.  Consulte [clusters lista e mostrar](#list-and-show-clusters).
>
>

O impacto da alteração do número de nós de dados para cada tipo de cluster suportada pelo HDInsight:

* Hadoop

    Perfeitamente pode aumentar o número de nós de trabalho de um cluster de Hadoop está em execução sem afetar quaisquer tarefas pendentes ou em execução. Também podem ser submetidas novas tarefas enquanto a operação está em curso. Falhas na operação de dimensionamento normalmente são processadas, para que o cluster está sempre deixado no estado funcional.

    Quando um cluster do Hadoop é dimensionado para baixo, reduzindo o número de nós de dados, alguns dos serviços em cluster são reiniciados. Isto faz com que todos os em execução e trabalhos pendentes a falhar após a conclusão da operação de dimensionamento. Pode, no entanto, a submeter as tarefas depois de concluída a operação.
* HBase

    Perfeitamente pode adicionar ou remover nós ao HBase cluster enquanto estiver em execução. Servidores regionais são equilibrados automaticamente dentro de alguns minutos de concluir a operação de dimensionamento. No entanto, pode equilibrar também manualmente os servidores regionais por iniciar sessão nos headnode do cluster e executando os comandos seguintes a partir de uma janela de linha de comandos:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Para obter mais informações sobre como utilizar a shell de HBase, consulte]
* Storm

    Perfeitamente pode adicionar ou remover nós de dados ao cluster do Storm enquanto estiver em execução. Mas, após uma conclusão com êxito da operação de dimensionamento, terá de reequilibrar a topologia.

    Reequilíbrio pode ser conseguido de duas formas:

  * IU da web do Storm
  * Ferramenta de interface de linha de comandos (CLI)

    Consulte o [documentação do Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A IU da web de Storm está disponível no cluster do HDInsight:

    ![Reequilibre de escala do HDInsight storm](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Eis um exemplo como utilizar o comando da CLI para rebalancear a topologia do Storm:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Dimensionar clusters**

1. Iniciar sessão para o [Portal][azure-portal].
2. Clique em **Procurar tudo** no menu à esquerda, clique em **Clusters do HDInsight**, clique no nome do cluster.
3. Clique em **definições** no menu superior e, em seguida, clique em **escala Cluster**.
4. Introduza **nós de número de trabalho**. O limite no número de nós de cluster varia entre subscrições do Azure. Pode contactar o suporte à faturação para aumentar o limite.  As informações de custo irão refletir as alterações efetuadas ao número de nós.

    ![Escala do Spark do HDInsight Hadoop HBase Storm](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Colocar em pausa/encerramento clusters
A maioria das tarefas do Hadoop são tarefas de lote só são ocasionalmente executaram. Para a maior parte dos clusters do Hadoop, existem grande períodos de tempo que o cluster não está a ser utilizado para processamento. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado.
Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados.

Existem várias formas pode programar o processo:

* Fábrica de dados do Azure do utilizador. Consulte [serviço ligado do Azure HDInsight](../data-factory/compute-linked-services.md) e [transformar e analisar utilizando o Azure Data Factory](../data-factory/transform-data.md) para o HDInsight a pedido e personalizada definida serviços ligados.
* Utilize o Azure PowerShell.  Consulte [analisar dados de atraso do voo](hdinsight-analyze-flight-delay-data.md).
* Utilize a CLI do Azure. Consulte [gerir clusters do HDInsight ao utilizar a CLI do Azure](hdinsight-administer-use-command-line.md).
* Utilize o SDK .NET do HDInsight. Consulte [tarefas do Hadoop submeter](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para informações de preços, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar um cluster a partir do Portal, consulte o artigo [eliminar clusters](#delete-clusters)

## <a name="change-cluster-username"></a>Nome de utilizador de cluster de alteração
Um cluster do HDInsight pode ter duas contas de utilizador. A conta de utilizador de cluster do HDInsight é criada durante o processo de criação. Também pode criar uma conta de utilizador do RDP para aceder ao cluster através de RDP. Consulte [ambiente de trabalho remoto permitem](#connect-to-hdinsight-clusters-by-using-rdp).

**Para alterar o nome de utilizador de cluster do HDInsight e a palavra-passe**

1. Iniciar sessão para o [Portal][azure-portal].
2. Clique em **Procurar tudo** no menu à esquerda, clique em **Clusters do HDInsight**, clique no nome do cluster.
3. Clique em **definições** no menu superior e, em seguida, clique em **início de sessão do Cluster**.
4. Se **início de sessão do Cluster** foi ativada, tem de clicar em **desativar**e, em seguida, clique em **ativar** antes de poder alterar o nome de utilizador e palavra-passe...
5. Alterar o **nome de início de sessão do Cluster** e/ou o **palavra-passe de início de sessão do Cluster**e, em seguida, clique em **guardar**.

    ![Alterar o HDInsight cluster utilizador username palavra-passe http utilizador](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Conceder/revogar o acesso
Clusters do HDInsight tem os seguintes serviços web HTTP (todos esses serviços tem pontos finais RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por predefinição, estes serviços são concedidos para o acesso. Pode revogar/conceder o acesso do portal do Azure.

> [!NOTE]
> Por conceder/revogar o acesso irá repor a palavra-passe e nome de utilizador do cluster.
>
>

**Para conceder/revogar o acesso de serviços web HTTP**

1. Iniciar sessão para o [Portal][azure-portal].
2. Clique em **Procurar tudo** no menu à esquerda, clique em **Clusters do HDInsight**, clique no nome do cluster.
3. Clique em **definições** no menu superior e, em seguida, clique em **início de sessão do Cluster**.
4. Se **início de sessão do Cluster** foi ativada, tem de clicar em **desativar**e, em seguida, clique em **ativar** antes de poder alterar o nome de utilizador e palavra-passe...
5. Para **nome de utilizador de início de sessão de Cluster** e **palavra-passe de início de sessão do Cluster**, introduza o novo nome de utilizador e palavra-passe (respetivamente) para o cluster.
6. Clique em **GUARDAR**.

    ![HDInsight geral remover o acesso de serviço web de http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Localizar a conta do storage predefinida
Cada cluster de HDInsight tem uma conta do storage predefinida. A conta do storage predefinida e respetivas chaves para um cluster, é apresentado em **definições**/**propriedades**/**chaves de armazenamento do Azure**. Consulte [clusters lista e mostrar](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Localizar o grupo de recursos
No modo Azure Resource Manager, cada cluster do HDInsight é criado com um grupo de recursos do Azure. O grupo de recursos do Azure que pertence um cluster é apresentado no:

* A lista de cluster tem um **grupo de recursos** coluna.
* Cluster **essencial** mosaico.  

Consulte [clusters lista e mostrar](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Abra a consola de consulta do HDInsight
A consola de consulta do HDInsight inclui as seguintes funcionalidades:

* **Editor de ramo de registo**: a interface de web de uma GUI para submeter tarefas do Hive.  Consulte [executar consultas do Hive através da consola de consulta](hadoop/apache-hadoop-use-hive-query-console.md).

    ![Editor de portal do hive do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Histórico da tarefa**: tarefas do Hadoop do Monitor.  

    ![Histórico de tarefas portal do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Clique em **nome da consulta** para mostrar os detalhes incluindo propriedades da tarefa, **consulta da tarefa**, e * * resultado da tarefa. Também pode transferir a consulta e a saída para a estação de trabalho.
* **Ficheiro de Browser**: procure a conta do storage predefinida e as contas do storage ligadas.

    ![Procurar de browser de ficheiro do portal do HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    Na captura de ecrã, o  **<Account>**  tipo indica o item é uma conta de armazenamento do Azure.  Clique no nome de conta para procurar os ficheiros.
* **IU do Hadoop**.

    ![Portal do HDInsight Hadoop IU](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    De **Hadoop IU*, pode procurar ficheiros e verifique os registos.
* **IU do yarn**.

    ![Portal de HDInsight IU do YARN](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Executar consultas do Hive
Para executou tarefas do Hive a partir do Portal, clique em **Editor do Hive** na consola de consulta do HDInsight. Consulte [consola aberta HDInsight consulta](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Monitorizar trabalhos
Para monitorizar tarefas a partir do Portal, clique em **histórico de tarefas** na consola de consulta do HDInsight. Consulte [consola aberta HDInsight consulta](#open-hdinsight-query-console).

## <a name="browse-files"></a>Procurar ficheiros
Para procurar ficheiros armazenados na conta de armazenamento predefinida e as contas do storage ligadas, clique em **ficheiro Browser** na consola de consulta do HDInsight. Consulte [consola aberta HDInsight consulta](#open-hdinsight-query-console).

Também pode utilizar o **procurar o sistema de ficheiros** utilitário do **Hadoop IU** na consola do HDInsight.  Consulte [consola aberta HDInsight consulta](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Monitorizar a utilização do cluster
O **utilização** secção do painel do cluster de HDInsight apresenta informações sobre o número de núcleos disponíveis à sua subscrição para utilização com o HDInsight, bem como o número de núcleos alocados para este cluster e como são alocados para os nós dentro deste cluster. Consulte [clusters lista e mostrar](#list-and-show-clusters).

> [!IMPORTANT]
> Para monitorizar os serviços fornecidos pelo cluster do HDInsight, tem de utilizar Web do Ambari ou a API de REST do Ambari. Para obter mais informações sobre a utilização do Ambari, consulte [gerir clusters do HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Abra a IU de Hadoop
Para monitorizar o cluster, procure o sistema de ficheiros e verifique os registos, clique em **Hadoop IU** na consola de consulta do HDInsight. Consulte [consola aberta HDInsight consulta](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Abra a IU do Yarn
Para utilizar a interface de utilizador do Yarn, clique em **IU do Yarn** na consola de consulta do HDInsight. Consulte [consola aberta HDInsight consulta](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Ligar a clusters através de RDP
As credenciais para o cluster que indicou durante a criação do respetiva conceda acesso para os serviços em cluster, mas não para o próprio cluster através do ambiente de trabalho remoto. Pode ativar o acesso de ambiente de trabalho remoto quando aprovisionar um cluster ou depois de um cluster seja aprovisionado. Para instruções sobre como ativar o ambiente de trabalho remoto durante a criação, consulte [cluster do HDInsight criar](hdinsight-hadoop-provision-linux-clusters.md).

**Para ativar o ambiente de trabalho remoto**

1. Iniciar sessão para o [Portal][azure-portal].
2. Clique em **Procurar tudo** no menu à esquerda, clique em **Clusters do HDInsight**, clique no nome do cluster.
3. Clique em **definições** no menu superior e, em seguida, clique em **ambiente de trabalho remoto**.
4. Introduza **expira em**, **nome de utilizador do ambiente de trabalho remoto** e **palavra-passe de ambiente de trabalho remoto**e, em seguida, clique em **ativar**.

    ![HDInsight Ativar desativar configurar o ambiente de trabalho remoto](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    Os valores predefinidos para expira em é uma semana.

   > [!NOTE]
   > Também pode utilizar o SDK .NET do HDInsight para ativar o ambiente de trabalho remoto num cluster. Utilize o **EnableRdp** método no objeto de cliente HDInsight da seguinte forma: **cliente. EnableRdp (clustername, localização, "rdpuser", "rdppassword" DateTime.Now.AddDays(6))**. Da mesma forma, para desativar o ambiente de trabalho remoto no cluster, pode utilizar **cliente. DisableRdp (clustername, localização)**. Para obter mais informações sobre estes métodos, consulte [referência do SDK do HDInsight .NET](http://go.microsoft.com/fwlink/?LinkId=529017). Isto é aplicável apenas para clusters do HDInsight em execução no Windows.
   >
   >

**Para ligar a um cluster através de RDP**

1. Iniciar sessão para o [Portal][azure-portal].
2. Clique em **Procurar tudo** no menu à esquerda, clique em **Clusters do HDInsight**, clique no nome do cluster.
3. Clique em **definições** no menu superior e, em seguida, clique em **ambiente de trabalho remoto**.
4. Clique em **Connect** e siga as instruções. Se ligar desativar, tem de ativá-la primeiro. Certifique-se com o nome de utilizador do ambiente de trabalho remoto utilizador e palavra-passe.  Não é possível utilizar as credenciais de utilizador do Cluster.

## <a name="open-hadoop-command-line"></a>Abra a linha de comandos de Hadoop
Para ligar ao cluster, utilizando o ambiente de trabalho remoto e utilizar a linha de comandos do Hadoop, deve primeiro ativou acesso de ambiente de trabalho remoto para o cluster, tal como descrito na secção anterior.

**Para abrir uma linha de comandos de Hadoop**

1. Ligar ao cluster através de ambiente de trabalho remoto.
2. No ambiente de trabalho, faça duplo clique em **linha de comandos do Hadoop**.

    ![HDI. HadoopCommandLine][image-hadoopcommandline]

    Para obter mais informações sobre os comandos de Hadoop, consulte [referência de comandos do Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Captura de ecrã anterior, o nome da pasta tem o número de versão do Hadoop incorporado. O número de versão pode alterado com base na versão dos componentes do Hadoop instalado no cluster. Pode utilizar variáveis de ambiente de Hadoop para fazer referência a essas pastas. Por exemplo:

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu como criar um cluster do HDInsight através do Portal e como abrir a ferramenta de linha de comandos do Hadoop. Para obter mais informações, consulte os artigos seguintes:

* [Administrar HDInsight com o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administrar HDInsight ao utilizar a CLI do Azure](hdinsight-administer-use-command-line.md)
* [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Submeter tarefas do Hadoop através de programação](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Qual a versão do Hadoop é no Azure HDInsight?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Linha de comandos de Hadoop"
