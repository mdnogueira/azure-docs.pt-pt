---
title: "Criar clusters do Hadoop, utilizando o PowerShell – Azure HDInsight | Microsoft Docs"
description: Saiba como criar clusters do Hadoop, HBase, Storm ou Spark no Linux para o HDInsight ao utilizar o Azure PowerShell.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 4d109fa61d0fcd0c516a519f3b3eb0ad01ccbf05
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Criar clusters baseados em Linux no HDInsight com o Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O Azure PowerShell é um ambiente de script de elevado desempenho que pode utilizar para controlar e automatizar a implementação e gestão das cargas de trabalho no Microsoft Azure. Este documento fornece informações sobre como criar um cluster do HDInsight baseado em Linux utilizando o Azure PowerShell. Também inclui um script de exemplo.

> [!NOTE]
> O Azure PowerShell está disponível apenas em clientes do Windows. Se estiver a utilizar um cliente Linux, Unix ou Mac OS X, consulte o artigo [criar um cluster do HDInsight baseado em Linux utilizando a CLI do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) para obter informações sobre como utilizar a CLI do Azure para criar um cluster.

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte antes de iniciar este procedimento:

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > O suporte do Azure PowerShell para gerir recursos do HDInsight com o Gestor de Serviços do Azure está **preterido**, e foi removido a 1 de janeiro de 2017. Os passos neste documento utilizam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga os passos no [instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) para instalar a versão mais recente do Azure PowerShell. Se tiver scripts que tenham de ser modificados para utilizar os novos cmdlets que funcionam com o Azure Resource Manager, veja [Migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager para clusters do HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para criar um cluster do HDInsight com o Azure PowerShell, tem de concluir os seguintes procedimentos:

* Criar um grupo de recursos do Azure
* Criar uma conta de Armazenamento do Azure
* Criar um contentor de Blob do Azure
* Criar um cluster do HDInsight

O script seguinte demonstra como criar um novo cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Os valores que especificar para o início de sessão do cluster são utilizados para criar a conta de utilizador de Hadoop para o cluster. Utilize esta conta para ligar a serviços alojados no cluster, tais como web UIs ou REST APIs.

Os valores que especificar para o utilizador SSH são utilizados para criar o utilizador SSH para o cluster. Utilize esta conta para iniciar uma sessão remota do SSH no cluster e executar tarefas. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Se planeia utilizar mais do que 32 nós de trabalho (a durante a criação do cluster ou ao aumentar o cluster após a criação), também tem de especificar um tamanho de nó principal pelo menos 8 núcleos e 14 GB de RAM.
>
> Para obter mais informações sobre os tamanhos de nós e os custos associados, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Pode demorar até 20 minutos para criar um cluster.

## <a name="create-cluster-configuration-object"></a>Criar cluster: objeto de configuração

Também pode criar um através de objeto de configuração do HDInsight `New-AzureRmHDInsightClusterConfig` cmdlet. Em seguida, pode modificar este objeto de configuração para ativar as opções de configuração adicionais para o cluster. Por último, utilize o `-Config` parâmetro o `New-AzureRmHDInsightCluster` cmdlet para utilizar a configuração.

O script seguinte cria um objeto de configuração para configurar um servidor R no tipo de cluster do HDInsight. A configuração permite que um nó de extremidade, RStudio e uma conta de armazenamento adicional.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> Não é suportada a utilização de uma conta de armazenamento numa localização diferente que o cluster do HDInsight. Quando utilizar este exemplo, crie a conta de armazenamento adicional na mesma localização que o servidor.

## <a name="customize-clusters"></a>Personalizar clusters

* Consulte [HDInsight personalizar clusters com o arranque](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [clusters do HDInsight de personalizar através da ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos seguintes

Agora que criou com êxito um cluster do HDInsight, utilize os seguintes recursos para aprender a trabalhar com o cluster.

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

