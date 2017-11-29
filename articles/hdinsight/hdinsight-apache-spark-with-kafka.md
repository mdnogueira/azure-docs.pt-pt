---
title: "Apache Spark de transmissão em fluxo com Kafka - Azure HDInsight | Microsoft Docs"
description: "Saiba como utilizar o Spark Apache Spark para dados de fluxo ou a sair Kafka Apache DStreams a utilizar. Neste exemplo, transmitir dados através de um bloco de notas do Jupyter do Spark no HDInsight."
keywords: "exemplo de kafka, kafka zookeeper, spark kafka, spark, transmissão em fluxo exemplo kafka de transmissão em fluxo"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: larryfr
ms.openlocfilehash: a780a7d954486dbce402e4bf45be55af8ad36ab9
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>Apache Spark transmissão em fluxo (DStream) exemplo com Kafka no HDInsight

Saiba como utilizar o Spark Apache Spark para dados de fluxo ou a sair Apache Kafka no HDInsight com DStreams. Este exemplo utiliza um bloco de notas do Jupyter que é executado no cluster do Spark.

> [!NOTE]
> Os passos neste documento, criar um grupo de recursos do Azure que contém tanto o Spark no HDInsight e um Kafka num cluster do HDInsight. Esses clusters são ambos localizado dentro de uma rede Virtual do Azure, que permite que o cluster do Spark comunicar diretamente com o Kafka do cluster.
>
> Quando tiver terminado com os passos neste documento, lembre-se eliminar os clusters para evitar encargos em excesso.

> [!IMPORTANT]
> Este exemplo utiliza DStreams, que é uma tecnologia de transmissão em fluxo do Spark mais antiga. Para obter um exemplo que utiliza o Spark mais recente funcionalidades de transmissão em fluxo, consulte o [Spark estruturados transmissão em fluxo com Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) documento.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight fornece acesso para os mediadores Kafka através da internet pública. Tudo o que sua Kafka tem de ser na mesma rede virtual do Azure que os nós do Kafka cluster. Neste exemplo, clusters Kafka tanto Spark estão localizados numa rede virtual do Azure. O diagrama seguinte mostra como a comunicação flua entre os clusters:

![Diagrama de clusters do Spark e Kafka uma Azure virtual network](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Embora Kafka propriamente dito está limitado a comunicação na rede virtual, os outros serviços em cluster, como o SSH e Ambari podem ser acedidos através da internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e os URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Pode criar uma Azure virtual network, Kafka, e clusters do Spark manualmente, é mais fáceis de utilizar um modelo Azure Resource Manager. Utilize os seguintes passos para implementar uma Azure virtual network, Kafka e Spark clusters a sua subscrição do Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Este modelo cria um cluster Kafka contém três nós de trabalho.

    Este modelo cria um cluster do HDInsight 3.6 para Kafka e Spark.

2. Utilize as seguintes informações para preencher as entradas no **implementação personalizada** secção:
   
    ![Implementação personalizada do HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupo de recursos**: criar um grupo ou selecione um existente. Este grupo contém o cluster do HDInsight.

    * **Localização**: selecione uma localização geograficamente perto de si.

    * **Nome do Cluster de base**: este valor é utilizado como o nome de base para o Spark e Kafka clusters. Por exemplo, introduzir **hdi** cria um cluster do Spark com o nome __spark hdi__ e um cluster de Kafka denominado **kafka hdi**.

    * **Nome de utilizador de início de sessão do cluster**: O nome de utilizador de administrador para os clusters do Spark e Kafka.

    * **Palavra-passe de início de sessão do cluster**: A palavra-passe de utilizador do administrador para os clusters do Spark e Kafka.

    * **Nome de utilizador SSH**: O utilizador SSH para criar para os clusters do Spark e Kafka.

    * **Palavra-passe SSH**: A palavra-passe para o utilizador SSH para os clusters do Spark e Kafka.

3. Leia o **termos e condições**e, em seguida, selecione **concordo com os termos e condições indicadas acima**.

4. Por fim, verifique **afixar ao dashboard** e, em seguida, selecione **Compra**. Demora cerca de 20 minutos para criar os clusters.

Depois de tem sido criados os recursos, é apresentada uma página de resumo.

![Grupo de recursos resumo para a vnet e clusters](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Tenha em atenção que os nomes dos clusters de HDInsight são **spark BASENAME** e **kafka BASENAME**, onde BASENAME é o nome fornecido para o modelo. Utilize estes nomes em passos posteriores quando ligar para os clusters.

## <a name="use-the-notebooks"></a>Utilize os blocos de notas

O código de exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Para concluir este exemplo, siga os passos a `README.md`.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Uma vez que os passos neste documento criar ambos os clusters no mesmo grupo de recursos do Azure, pode eliminar o grupo de recursos no portal do Azure. A eliminar o grupo remove todos os recursos que criou ao seguir este documento, a rede Virtual do Azure e conta de armazenamento utilizada pelos clusters.

## <a name="next-steps"></a>Passos seguintes

Neste exemplo, aprendeu a utilizar o Spark para ler e escrever Kafka. Utilize as seguintes ligações para detetar outras formas de trabalhar com Kafka:

* [Introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
* [Utilizar MirrorMaker para criar uma réplica de Kafka no HDInsight](kafka/apache-kafka-mirroring.md)
* [Utilizar o Apache Storm com Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)

