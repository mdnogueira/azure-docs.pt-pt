---
title: Criar clusters do Hadoop, utilizando um browser - Azure HDInsight | Microsoft Docs
description: "Saiba como criar clusters do Hadoop, HBase, Storm ou Spark no Linux para o HDInsight utilizando um browser e o portal de pré-visualização do Azure."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 4c1064232db87c23048fe01f04f403947e60ab93
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight com o portal do Azure
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gestão baseado na web de serviços e recursos alojados na nuvem do Microsoft Azure. Neste artigo, ficará a saber como criar clusters do HDInsight baseado em Linux através do portal.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um browser moderno**. O portal do Azure utiliza HTML5 e Javascript e poderá não funcionar corretamente em browsers mais antigos.

## <a name="create-clusters"></a>Criar clusters
O portal do Azure expõe a maior parte das propriedades do cluster. Utilizar o modelo Azure Resource Manager, pode ocultar muitos dos detalhes. Para obter mais informações, consulte [clusters do Hadoop baseados em criar Linux no HDInsight com modelos Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em  **+** , clique em **Intelligence + análise**e, em seguida, clique em **HDInsight**.
   
    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "criar um novo cluster no portal do Azure")

3. No **HDInsight** painel, clique em **personalizada (tamanho, definições, aplicações)**, clique em **Noções básicas**e, em seguida, introduza as seguintes informações.

    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "criar um novo cluster no portal do Azure")

    * Introduza o **Nome do Cluster**: este nome tem de ser globalmente exclusivo.

    * Do **subscrição** pendente, selecione a subscrição do Azure que será utilizada para o cluster.

    * Clique em **tipo de Cluster**e, em seguida, selecione:
   
        * **Tipo de Cluster**: se não sabe o que escolher, selecione **Hadoop**. É o tipo de cluster mais popular.
     
            > [!IMPORTANT]
            > HDInsight clusters vêm numa variedade de tipos, que correspondem à carga de trabalho ou tecnologia que o cluster está otimizado para. Não há nenhum método suportado para criar um cluster que combina vários tipos, por exemplo, Storm e HBase num cluster. 
            > 
            > 
        
        * **Sistema Operativo**: selecione **Linux**.
        
        * **Versão**: utilize a versão predefinida se não souber o que escolher. Para obter mais informações, veja [HDInsight cluster versions (Versões de clusters HDInsight)](hdinsight-component-versioning.md).
        * **Escalão do Cluster**: o Azure HDInsight disponibiliza as ofertas de nuvem de macrodados em duas categorias -- escalão Standard e escalão Premium. Para obter mais informações, veja [Escalões de Clusters](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).

    * Para **nome de utilizador de início de sessão de Cluster** e **palavra-passe de início de sessão do Cluster**, forneça o nome de utilizador e palavra-passe para o utilizador de admin.

    * Introduza um **nome de utilizador SSH** e se pretende que a palavra-passe do SSH mesma que a palavra-passe de admin especificado anteriormente, selecione o **utilizar a mesma palavra-passe como início de sessão do cluster** caixa de verificação. Se não estiver, forneça um **palavra-passe** ou **chave pública**, que será utilizado para autenticar o utilizador SSH. A abordagem recomendada é utilizar uma chave pública. Clique em **Selecionar**, na parte inferior, para guardar a configuração das credenciais.
   
        Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Para **grupo de recursos**, especifique se pretende criar um novo grupo de recursos ou utilize uma já existente.

    * Especifique um centro de dados **localização** onde será criado o cluster.

    * Clique em **Seguinte**.

4. No **armazenamento** painel, especifique se pretende que armazenamento do Azure (WASB) ou de Data Lake Store como o armazenamento de predefinido. Observe a tabela abaixo para obter mais informações.

    ![Criar um novo cluster no portal do Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "criar um novo cluster no portal do Azure")

    | Armazenamento                                      | Descrição |
    |----------------------------------------------|-------------|
    | **Blobs de armazenamento do Azure como armazenamento de predefinido**   | <ul><li>Para **tipo de armazenamento primário**, selecione **Storage do Azure**. Depois disso, para **método de seleção**, pode escolher **minhas subscrições** se pretender especificar uma conta de armazenamento que faz parte da sua subscrição do Azure e, em seguida, selecione a conta de armazenamento. Caso contrário, clique em **chave de acesso** e forneça as informações da conta de armazenamento que pretende escolher a partir de fora da sua subscrição do Azure.</li><li>Para **contentor predefinido**, pode optar por voltar com o nome do contentor predefinido sugerido pelo portal ou especificar a sua própria.</li><li>Se estiver a utilizar WASB como armazenamento de predefinido, pode clicar (opcionalmente) **contas de armazenamento adicionais** especificar contas de armazenamento adicional para associar o cluster. No **chaves de armazenamento do Azure** painel, clique em **adicionar uma chave de armazenamento**, e, em seguida, pode fornecer uma conta de armazenamento de subscrições do Azure ou de outras subscrições (fornecendo o acesso da conta de armazenamento chave).</li><li>Se estiver a utilizar WASB como armazenamento de predefinido, pode clicar (opcionalmente) **acesso de Data Lake Store** para especificar o Azure Data Lake Store como armazenamento adicional. Para obter mais informações, consulte [criar um cluster do HDInsight com o Data Lake Store utilizando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).</li></ul> |
    | **Como armazenamento de predefinido do Azure Data Lake Store** | Para **tipo de armazenamento primário**, selecione **Data Lake Store** e, em seguida, consulte o artigo [criar um cluster do HDInsight com o Data Lake Store utilizando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) para obter instruções. |
    | **Metastores externas**                      | Opcionalmente, pode especificar uma base de dados do SQL Server para guardar os metadados do Hive e Oozie associados com o cluster. Para **selecionar uma base de dados do SQL Server para o ramo de registo** Selecione uma base de dados do SQL Server e, em seguida, forneça o nome de utilizador/palavra-passe para a base de dados. Repita estes passos para metadados do Oozie.<br><br>Algumas considerações ao utilizar a SQL database do Azure para metastores. <ul><li>A base de dados SQL do Azure utilizado para o metastore têm de permitir conectividade a outros serviços do Azure, incluindo o Azure HDInsight. No dashboard de base de dados SQL do Azure, no lado direito, clique no nome do servidor. Este é o servidor no qual a instância de base de dados do SQL Server está em execução. Assim que estiver na vista de servidores, clique em **configurar**e, em seguida, para **serviços do Azure**, clique em **Sim**e, em seguida, clique em **guardar**.</li><li>Ao criar um metastore, não utilize um nome de base de dados que contém traços ou hífenes, como isto pode provocar a falha no processo de criação de cluster.</li></ul>                                                                                                                                                                       |

    Clique em **Seguinte**. 

    > [!WARNING]
    > Não é suportado utilizar uma conta de armazenamento adicional numa localização diferente do cluster do HDInsight.

5. Opcionalmente, clique em **aplicações** para instalar aplicações que funcionam com clusters do HDInsight. Estas aplicações podem ser desenvolvidas pela Microsoft, por fornecedores independentes de software (ISV) ou por si. Para obter mais informações, consulte [aplicações do HDInsight instalar](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Clique em **tamanho de Cluster** para apresentar informações sobre os nós que será criada para este cluster. Defina o número de nós de trabalho que precisa para o cluster. O custo estimado do cluster será apresentado no painel.
   
    ![Painel de escalões de preço de nó](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "especificar o número de nós de cluster")
   
   > [!IMPORTANT]
   > Se pretender mais do que 32 nós de trabalho, durante a criação do cluster ou ao aumentar o cluster após a criação, tem de selecionar um tamanho de nó principal com pelo menos 8 núcleos e 14GB de ram.
   > 
   > Para obter mais informações sobre os tamanhos de nós e os custos associados, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Clique em **seguinte** para guardar o configuração de preço de nó.

7. Clique em **definições avançadas** para configurar outras definições opcionais, como utilizar **ações de Script** para personalizar um cluster para instalar componentes personalizados ou associar um **deredeVirtual**. Observe a tabela abaixo para obter mais informações.

    ![Painel de escalões de preço de nó](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-advanced.png "especificar o número de nós de cluster")

    | Opção | Descrição |
    |--------|-------------|
    | **Ações de script** | Utilize esta opção se pretender utilizar um script personalizado para personalizar um cluster, como o cluster está a ser criado. Para obter mais informações sobre as ações de script, consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster-linux.md). |
    | **Rede Virtual** | Selecione uma rede virtual do Azure e a sub-rede, se pretende colocar o cluster para uma rede virtual. Para informações sobre como utilizar o HDInsight com uma rede Virtual, incluindo requisitos de configuração específicos para a rede Virtual, consulte [capacidades de expandir HDInsight utilizando uma Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). |

    Clique em **Seguinte**.

8. No **resumo** painel, verifique as informações que introduziu anteriormente e, em seguida, clique em **criar**.

    ![Painel de escalões de preço de nó](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "especificar o número de nós de cluster")
    
    > [!NOTE]
    > Irá demorar algum tempo para que o cluster ser criado, normalmente, cerca de 15 minutos. Utilize o mosaico no Startboard, ou o **notificações** entrada à esquerda da página para verificar o processo de aprovisionamento.
    > 
    > 
12. Depois de ter concluído o processo de criação, clique no mosaico para o cluster a partir do Startboard para iniciar o painel do cluster. Painel do cluster fornece as seguintes informações.
    
    ![Painel do cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "propriedades de Cluster")
    
    Utilize o seguinte para compreender os ícones na parte superior deste painel.
    
    * **Descrição geral** separador fornece todas as informações essenciais sobre o cluster, como o nome, o grupo de recursos pertence, a localização, o sistema operativo, o URL para o dashboard do cluster, etc.
    * **Dashboard** direciona-o para o portal do Ambari associado com o cluster.
    * **Secure Shell**: informações necessárias para aceder ao cluster através de SSH.
    * **Cluster de escala** permite aumentar o número de nós de trabalho associados com o cluster.
    * **Eliminar**: elimina o cluster do HDInsight.
    

## <a name="customize-clusters"></a>Personalizar clusters
* Consulte [HDInsight personalizar clusters com o arranque](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminar o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos seguintes
Agora que criou com êxito um cluster do HDInsight, utilize o seguinte para saber como trabalhar com o cluster:

### <a name="hadoop-clusters"></a>Clusters do Hadoop
* [Utilizar o Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters de HBase
* [Introdução ao HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações de Java para o HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters de Storm
* [Desenvolver topologias de Java de Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utilize os componentes de Python Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implementar e monitorizar as topologias Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clusters do Spark
* [Criar uma aplicação autónoma com o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](spark/apache-spark-livy-rest-interface.md)
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](spark/apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](spark/apache-spark-eventhub-streaming.md)

