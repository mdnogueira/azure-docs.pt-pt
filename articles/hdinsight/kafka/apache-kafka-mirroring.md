---
title: "Espelhar tópicos do Apache Kafka - Azure HDInsight | Microsoft Docs"
description: "Saiba como utilizar a funcionalidade de espelhamento do Apache Kafka para manter uma réplica de uma Kafka num cluster do HDInsight através do espelhamento tópicos para um cluster secundário."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: a7063375ac4a2f9f172b5c380c2d5472a12e1bfb
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Utilizar MirrorMaker para replicar tópicos Kafka do Apache com Kafka no HDInsight

Saiba como utilizar a funcionalidade de espelhamento do Apache Kafka para replicar tópicos para um cluster secundário. Espelhamento pode ser executado como um processo contínuo, ou ligados intermitentemente utilizado como um método de migração dos dados de um cluster para outro.

Neste exemplo, o espelhamento é utilizado para replicar tópicos entre dois clusters do HDInsight. Ambos os clusters são numa rede Virtual na mesma região do Azure.

> [!WARNING]
> Espelhamento não deve ser considerado como um meio para conseguir tolerância a falhas. O desvio para itens dentro de um tópico são diferentes entre clusters de origem e de destino, pelo que os clientes não é possível utilizar as duas-no alternadamente.
>
> Se preocupados com tolerância a falhas, deve definir a replicação para os tópicos no seu cluster. Para obter mais informações, consulte [começar com Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="how-kafka-mirroring-works"></a>Como funciona o Kafka espelhamento

Espelhamento funciona utilizando a ferramenta de MirrorMaker (parte do Apache Kafka) para consumir registos de tópicos no cluster de origem e, em seguida, criar uma cópia local no cluster de destino. MirrorMaker utiliza um (ou mais) *consumidores* que ler a partir do cluster de origem e um *produtor* que escreve para o cluster local (destino).

O diagrama seguinte ilustra o processo de espelhamento de:

![Diagrama do processo de espelhamento](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka no HDInsight fornece acesso ao serviço Kafka através da internet pública. Os produtores de Kafka ou consumidores tem de ser na mesma rede virtual do Azure que os nós do Kafka cluster. Para este exemplo, ambos os Kafka origem e destino clusters estão localizados numa rede virtual do Azure. O diagrama seguinte mostra como a comunicação flua entre os clusters:

![Diagrama de origem e destino Kafka clusters numa rede virtual do Azure](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Os clusters de origem e de destino podem ser diferentes do número de nós e partições e são também diferentes desvios dentro os tópicos. Espelhamento mantém o valor da chave que é utilizado para criação de partições, pelo que a ordem de registo é preservada numa base por chave.

### <a name="mirroring-across-network-boundaries"></a>Espelhamento através de limites de rede

Se precisar de espelhar entre clusters Kafka em redes diferentes, existem as seguintes considerações adicionais:

* **Gateways**: as redes tem de ser capazes de comunicar ao nível do TCPIP.

* **Resolução de nomes**: Kafka a clusters em cada rede devem ser capazes de ligar-se entre si utilizando nomes de anfitrião. Esta operação pode requerer um servidor de sistema de nomes de domínio (DNS) em cada rede que está configurado para reencaminhar pedidos para as outras redes.

    Ao criar uma Azure Virtual Network, em vez de utilizar o DNS automático fornecido com a rede, tem de especificar um servidor DNS personalizado e o endereço IP para o servidor. Depois de criada a rede Virtual, tem, em seguida, criar uma Máquina Virtual do Azure que utiliza esse endereço IP, em seguida, instalar e configurar o software DNS no mesmo.

    > [!WARNING]
    > Criar e configurar o servidor DNS personalizado antes de instalar o HDInsight para a rede Virtual. Não há nenhuma configuração adicional necessária para o HDInsight utilizar o servidor DNS configurado para a rede Virtual.

Para obter mais informações sobre ligar duas redes virtuais do Azure, consulte [configurar uma ligação VNet a VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Criar Kafka clusters

Pode criar uma Azure virtual network e Kafka clusters manualmente, é mais fáceis de utilizar um modelo Azure Resource Manager. Utilize os seguintes passos para implementar uma Azure virtual network e dois clusters de Kafka à sua subscrição do Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    O modelo Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]
    > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Este modelo cria um cluster Kafka contém três nós de trabalho.

2. Utilize as seguintes informações para preencher as entradas no **implementação personalizada** painel:
    
    ![Implementação personalizada do HDInsight](./media/apache-kafka-mirroring/parameters.png)
    
    * **Grupo de recursos**: criar um grupo ou selecione um existente. Este grupo contém o cluster do HDInsight.

    * **Localização**: selecione uma localização geograficamente perto de si.
     
    * **Nome do Cluster de base**: este valor é utilizado como o nome de base para o Kafka clusters. Por exemplo, introduzir **hdi** cria clusters com o nome **origem hdi** e **dest hdi**.

    * **Nome de utilizador de início de sessão do cluster**: O nome de utilizador de administrador para a origem e destino Kafka clusters.

    * **Palavra-passe de início de sessão do cluster**: A palavra-passe de utilizador do administrador para a origem e destino Kafka clusters.

    * **Nome de utilizador SSH**: O utilizador SSH para criar para a origem e destino Kafka clusters.

    * **Palavra-passe SSH**: A palavra-passe para o utilizador SSH para a origem e destino Kafka clusters.

3. Leia o **termos e condições**e, em seguida, selecione **concordo com os termos e condições indicadas acima**.

4. Por fim, verifique **afixar ao dashboard** e, em seguida, selecione **Compra**. Demora cerca de 20 minutos para criar os clusters.

> [!IMPORTANT]
> O nome dos clusters do HDInsight são **origem BASENAME** e **dest BASENAME**, onde BASENAME é o nome fornecido para o modelo. Utilize estes nomes em passos posteriores quando ligar para os clusters.

## <a name="create-topics"></a>Criar tópicos

1. Ligar para o **origem** cluster utilizando o SSH:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Substitua **sshuser** com o nome de utilizador do SSH utilizado quando criar o cluster. Substitua **BASENAME** com o nome de base utilizado ao criar o cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize os seguintes comandos para localizar os anfitriões de Zookeeper para o cluster de origem:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Substitua `$CLUSTERNAME` com o nome do cluster de origem. Quando lhe for pedido, introduza a palavra-passe para a conta de início de sessão (administrador) do cluster.

3. Para criar um tópico com o nome `testtopic`, utilize o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Utilize o seguinte comando para verificar se foi criado o tópico:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    Contém a resposta `testtopic`.

4. Utilize o seguinte para ver as informações do anfitrião Zookeeper para este (o **origem**) cluster:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Esta ação devolve informações semelhante para o seguinte texto:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Guarde estas informações. É utilizado na secção seguinte.

## <a name="configure-mirroring"></a>Configurar o espelhamento

1. Ligar para o **destino** cluster através de uma sessão SSH diferentes:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Substitua **sshuser** com o nome de utilizador do SSH utilizado quando criar o cluster. Substitua **BASENAME** com o nome de base utilizado ao criar o cluster.

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A `consumer.properties` ficheiro é utilizado para configurar a comunicação com o **origem** cluster. Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano consumer.properties
    ```

    Utilize o seguinte texto como o conteúdo a `consumer.properties` ficheiro:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Substitua **SOURCE_ZKHOSTS** com as informações de anfitriões de Zookeeper do **origem** cluster.

    Este ficheiro descreve a informação de consumidor a utilizar ao ler a partir da origem de Kafka cluster. Para a configuração de consumidor mais informações, consulte [folhas de consumidor](https://kafka.apache.org/documentation#consumerconfigs) em kafka.apache.org.

    Para guardar o ficheiro, utilize **Ctrl + X**, **Y**e, em seguida, **Enter**.

3. Antes de configurar o produtor que comunica com o cluster de destino, tem de encontrar o Mediador de anfitriões para o **destino** cluster. Utilize os seguintes comandos para obter estas informações:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Substitua `$CLUSTERNAME` com o nome do cluster de destino. Quando lhe for pedido, introduza a palavra-passe para a conta de início de sessão (administrador) do cluster.

    O `echo` comando devolve as informações semelhante para o seguinte texto:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. A `producer.properties` ficheiro é utilizado para comunicar o __destino__ cluster. Para criar o ficheiro, utilize o seguinte comando:

    ```bash
    nano producer.properties
    ```

    Utilize o seguinte texto como o conteúdo a `producer.properties` ficheiro:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Substitua **DEST_BROKERS** com as informações de Mediador do passo anterior.

    Para obter mais informações produtor configuração, consulte [produtor folhas](https://kafka.apache.org/documentation#producerconfigs) em kafka.apache.org.

## <a name="start-mirrormaker"></a>Iniciar MirrorMaker

1. A ligação SSH para a **destino** do cluster, utilize o seguinte comando para iniciar o processo de MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Os parâmetros utilizados neste exemplo são:

    * **-consumer.config**: Especifica o ficheiro que contém as propriedades de consumidor. Estas propriedades são utilizadas para criar um consumidor que lê a partir de *origem* Kafka cluster.

    * **-producer.config**: Especifica o ficheiro que contém as propriedades de produtor. Estas propriedades são utilizadas para criar um produtor que escreve o *destino* Kafka cluster.

    * **lista branca –**: uma lista de tópicos que MirrorMaker replica do cluster de origem para o destino.

    * **-num.streams**: O número de threads de consumidor para criar.

 No arranque, MirrorMaker devolve informações semelhantes para o seguinte texto:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. A ligação SSH para a **origem** do cluster, utilize o seguinte comando para iniciar um produtor e enviar mensagens para o tópico:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Substitua `$CLUSTERNAME` com o nome do cluster de origem. Quando lhe for pedido, introduza a palavra-passe para a conta de início de sessão (administrador) do cluster.

     Quando chegam a uma linha em branco com um cursor, escreva algumas mensagens de texto. As mensagens são enviadas para o tópico a **origem** cluster. Quando tiver terminado, utilize **Ctrl + C** terminar o processo de produtor.

3. A ligação SSH para a **destino** do cluster, utilize **Ctrl + C** terminar o processo de MirrorMaker. Para verificar que as mensagens e um tópico foram replicadas para o destino, utilize os seguintes comandos:

    ```bash
    DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Substitua `$CLUSTERNAME` com o nome do cluster de destino. Quando lhe for pedido, introduza a palavra-passe para a conta de início de sessão (administrador) do cluster.

    A lista de tópicos inclui agora `testtopic`, que é criado quando MirrorMaster reflete o tópico do cluster de origem para o destino. As mensagens obtidas do tópico são os mesmos tal como foi introduzido no cluster de origem.

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Uma vez que os passos neste documento criar ambos os clusters no mesmo grupo de recursos do Azure, pode eliminar o grupo de recursos no portal do Azure. A eliminar o grupo de recursos remove todos os recursos que criou ao seguir este documento, a rede Virtual do Azure e conta de armazenamento utilizada pelos clusters.

## <a name="next-steps"></a>Passos Seguintes

Neste documento, aprendeu a utilizar MirrorMaker para criar uma réplica de um cluster de Kafka. Utilize as seguintes ligações para detetar outras formas de trabalhar com Kafka:

* [Documentação do Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) em cwiki.apache.org.
* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)
* [Utilizar o Apache Spark com Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Utilizar o Apache Storm com Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Ligar ao Kafka através de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)
