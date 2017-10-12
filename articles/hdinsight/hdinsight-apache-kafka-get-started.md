---
title: "Começar com o Apache Kafka - Azure HDInsight | Microsoft Docs"
description: "Saiba como criar um cluster do Apache Kafka no Azure HDInsight. Saiba como criar tópicos, subscritores e consumidores."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: 1e51f546d6c256e1d8f1a1be50c6a2102fe26529
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="start-with-apache-kafka-preview-on-hdinsight"></a>Começar com o Apache Kafka (pré-visualização) no HDInsight

Saiba como criar e utilizar um cluster do [Apache Kafka](https://kafka.apache.org) no Azure HDInsight. O Kafka é uma plataforma open source de transmissão em fluxo distribuída, disponível com o HDInsight. É frequentemente utilizado como mediador de mensagens, uma vez que fornece funcionalidades semelhantes a uma fila de mensagens de publicação-subscrição.

> [!NOTE]
> Atualmente, existem duas versões do Kafka disponíveis com o HDInsight: 0.9.0 (HDInsight 3.4) e 0.10.0 (HDInsight 3.5 e 3.6). Os passos neste documento partem do princípio de que está a utilizar o Kafka no HDInsight 3.6.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Criar um cluster Kafka

Utilize os seguintes passos para criar um Kafka num cluster HDInsight:

1. No [portal do Azure](https://portal.azure.com), selecione **+ NOVO**, **Inteligência + Análise**e, em seguida, selecione **HDInsight**.
   
    ![Criar um cluster HDInsight](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. Em **Informações Básicas**, introduza as seguintes informações:

    * **Nome do Cluster**: o nome do cluster HDInsight.
    * **Subscrição**: selecione a subscrição que pretende utilizar.
    * **Nome de utilizador de início de sessão do cluster** e **Palavra-passe de início de sessão do cluster**: o início de sessão ao aceder ao cluster através de HTTPS. Utilize estas credenciais para aceder aos serviços, como a IU Web do Ambari ou a API REST.
    * **Nome de utilizador do Secure Shell (SSH)**: o início de sessão utilizado ao aceder ao cluster através de SSH. Por predefinição, a palavra-passe é igual à palavra-passe de início de sessão do cluster.
    * **Grupo de Recursos**: o grupo de recursos em que o cluster é criado.
    * **Localização**: a região do Azure em que o cluster é criado.

        > [!IMPORTANT]
        > Para obter uma elevada disponibilidade de dados, recomendamos que selecione uma localização (região) que contenha __três domínios de falha__. Para obter mais informações, consulte a secção [Elevada disponibilidade de dados](#data-high-availability).
   
 ![Selecionar subscrição](./media/hdinsight-apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Selecione **Tipo de cluster** e, em seguida, defina os seguintes valores em **Configuração de cluster**:
   
    * **Tipo de Cluster**: Kafka

    * **Versão**: Kafka 0.10.0 (HDI 3.6)

    * **Escalão do Cluster**: Standard
     
 Por fim, utilize o botão **Selecionar** para guardar as definições.
     
 ![Selecionar tipo de cluster](./media/hdinsight-apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. Depois de selecionar o tipo de cluster, utilize o botão __Selecionar__ para definir o tipo de cluster. Em seguida, utilize o botão __Seguinte__ para concluir a configuração básica.

5. Em **Armazenamento**, selecione ou crie uma Conta de armazenamento. Para seguir os passos neste documento, deixe os outros campos com os valores predefinidos. Utilize o botão __Seguinte__ para guardar a configuração do armazenamento.

    ![Configurar as definições de conta de armazenamento do HDInsight](./media/hdinsight-apache-kafka-get-started/set-hdinsight-storage-account.png)

6. Em __Aplicações (opcional)__, selecione __Seguinte__ para continuar. Não são necessárias aplicações para este exemplo.

7. Em __Tamanho do cluster__ , selecione __Seguinte__ para continuar.

    > [!WARNING]
    > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Para obter mais informações, consulte a secção [Elevada disponibilidade de dados](#data-high-availability).

    ![Definir o tamanho do cluster do Kafka](./media/hdinsight-apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > A entrada **discos por nó de trabalho** controla a escalabilidade do Kafka no HDInsight. O Kafka no HDInsight utiliza o disco local das máquinas virtuais no cluster. O Kafka recebe um fluxo intensivo de dados de E/S, pelo que o [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) é utilizado para proporcionar um elevado débito de transferência e fornecer uma maior capacidade de armazenamento por nó. O tipo de disco gerido pode ser __Standard__ (HDD) ou __Premium__ (SSD). Os discos Premium são utilizados com as VMs das séries DS e GS. Todos os outros tipos de VM utilizam discos Standard.

8. Em __Definições avançadas__, selecione __Seguinte__ para continuar.

9. Em **Resumo**, reveja a configuração do cluster. Utilize as ligações __Editar__ para alterar quaisquer definições que estejam incorretas. Por fim, utilize o botão the__Create__ para criar o cluster.
   
    ![Resumo da configuração do cluster](./media/hdinsight-apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > A criação do cluster pode demorar até 20 minutos.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

> [!IMPORTANT]
> Quando realizar os passos seguintes, tem de utilizar um cliente SSH. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

A partir do seu cliente, utilize SSH para ligar ao cluster:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Substitua **SSHUSER** pelo nome de utilizador de SSH que indicou durante a criação do cluster. Substitua **CLUSTERNAME** pelo nome do cluster.

Quando lhe for pedido, introduza a palavra-passe que utilizou para a conta SSH.

Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>Obter as informações dos anfitriões Zookeeper e Mediador

Ao trabalhar com o Kafka, tem de saber dois valores de anfitrião: dos anfitriões *Zookeeper* e dos anfitriões do *Mediador*. Estes anfitriões são utilizados com a API Kafka e com muitos dos utilitários que são enviados com o Kafka.

Utilize os seguintes passos para criar variáveis de ambiente que contêm as informações do anfitrião. Estas variáveis de ambiente são utilizadas nos passos deste documento.

1. A partir de uma ligação SSH ao cluster, utilize o comando seguinte para instalar o utilitário `jq`. Este utilitário é utilizado para analisar documentos JSON e é útil para obter as informações do anfitrião mediador:
   
    ```bash
    sudo apt -y install jq
    ```

2. Para definir as variáveis de ambiente com informações obtidas a partir do Ambari, utilize os seguintes comandos:

    ```bash
    CLUSTERNAME='your cluster name'
    PASSWORD='your cluster password'
    export KAFKAZKHOSTS=`curl -sS -u admin:$PASSWORD -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin:$PASSWORD -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > Defina `CLUSTERNAME=` com o nome do cluster do Kafka. Defina `PASSWORD=` com a palavra-passe de início de sessão (admin) que utilizou quando criou o cluster.

    O texto seguinte é um exemplo dos conteúdos dos `$KAFKAZKHOSTS`:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    O texto seguinte é um exemplo dos conteúdos dos `$KAFKABROKERS`:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

    > [!NOTE]
    > O comando `cut` é utilizado para limitar a lista de anfitriões a duas entradas de anfitrião. Não precisa de fornecer a lista completa de anfitriões quando cria um consumidor ou produtor do Kafka.
   
    > [!WARNING]
    > As informações devolvidas nesta sessão poderão não ser sempre precisas. Se dimensionar o cluster, novos mediadores serão adicionados ou removidos. Se ocorrer uma falha e um nó for substituído, o nome do anfitrião do nó poderá mudar.
    >
    > Deve obter as informações de anfitriões Zookeeper e do mediador pouco tempo antes de as utilizar, para certificar-se de que as informações são válidas.

## <a name="create-a-topic"></a>Criar um tópico

O Kafka armazena fluxos de dados em categorias chamadas *tópicos*. A partir de uma ligação SSH a um nó principal do cluster, utilize um script fornecido com Kafka para criar um tópico:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Este comando liga ao Zookeeper, utilizando as informações de anfitrião armazenadas nos `$KAFKAZKHOSTS` e, em seguida, cria um tópico Kafka com o nome **teste**. Pode verificar se o tópico foi criado, utilizando o seguinte script para tópicos de lista:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

O resultado deste comando apresenta uma lista de tópicos Kafka, que contém o tópico **teste**.

## <a name="produce-and-consume-records"></a>Produzir e consumir registos

O Kafka armazena *registos* nos tópicos. Os registos são produzidos por *produtores* e consumidos por *consumidores*. Os produtores obtêm registos de *mediadores* Kafka. Cada nó de trabalho no cluster HDInsight é um mediador Kafka.

Utilize os seguintes passos para armazenar registos no tópico de teste que criou anteriormente e, em seguida, leia-os utilizando um consumidor:

1. A partir da sessão SSH, utilize um script fornecido com Kafka para escrever registos no tópico:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Não é reencaminhado para o pedido após este comando. Em vez disso, escreva algumas mensagens de texto e, em seguida, utilize **Ctrl + C** para deixar de enviar para o tópico. Cada linha é enviada como um registo separado.

2. Utilize um script fornecido com Kafka para ler registos do tópico:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Este comando obtém os registos do tópico e apresenta-os. A utilização de `--from-beginning` indica ao consumidor para iniciar a partir do início da transmissão em fluxo, para que todos os registos sejam obtidos.

3. Utilize __Ctrl + C__ para parar o consumidor.

## <a name="producer-and-consumer-api"></a>API de produtor e de consumidor

Pode também produzir e consumir registos através de programação, utilizando as [APIs Kafka](http://kafka.apache.org/documentation#api). Para criar um produtor e consumidor de Java, utilize os seguintes passos a partir do seu ambiente de desenvolvimento.

> [!IMPORTANT]
> Tem de ter os seguintes componentes instalados no seu ambiente de desenvolvimento:
>
> * [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) ou equivalente, como OpenJDK.
>
> * [Apache Maven](http://maven.apache.org/)
>
> * Um cliente SSH e o comando `scp`. Para obter mais informações, veja o documento [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

1. Transfira os exemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Para o exemplo produtor/consumidor, utilize o projeto no diretório `Producer-Consumer`. Este exemplo contém as seguintes classes:
   
    * **Executar** - inicia o consumidor ou o produtor.

    * **Produtor** - armazena 1 000 000 registos no tópico.

    * **Consumidor** - lê registos do tópico.

2. Para criar um pacote de jar, mude os diretórios para a localização do diretório `Producer-Consumer` e utilize o seguinte comando:

    ```
    mvn clean package
    ```

    Este comando cria um diretório com o nome `target`, que contém um ficheiro com o nome `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Utilize os seguintes comandos para copiar o ficheiro `kafka-producer-consumer-1.0-SNAPSHOT.jar` para o cluster HDInsight:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Substitua **SSHUSER** pelo utilizador SSH do seu cluster, e substitua **CLUSTERNAME** pelo nome do seu cluster. Quando lhe for pedido, introduza a palavra-passe do utilizador SSH.

4. Quando o comando `scp` terminar de copiar o ficheiro, ligue-se ao cluster através de SSH. Utilize o seguinte comando para escrever os registos no tópico de teste:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

5. Depois de o processo estar concluído, utilize o seguinte comando para ler a partir do tópico:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    A leitura dos registos, juntamente com uma contagem de registos, é apresentada. Poderá ver um pouco mais do que 1 000 000 registos, uma vez que enviou vários registos para o tópico utilizando um script num passo anterior.

6. Utilize __Ctrl + C__ para sair do consumidor.

### <a name="multiple-consumers"></a>Vários consumidores

Os consumidores de Kafka utilizam um grupo de consumidores quando leem os registos. Utilizar o mesmo grupo com vários consumidores resulta em leituras com balanceamento de carga de um tópico. Cada consumidor no grupo recebe uma parte dos registos. Para ver este processo em ação, utilize os seguintes passos:

1. Abra uma nova sessão SSH para o cluster, para ter duas sessões. Em cada sessão, utilize o seguinte procedimento para iniciar um consumidor com o mesmo ID de grupo de consumidores:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    Este comando inicia um consumidor com o ID de grupo `mygroup`.

    > [!NOTE]
    > Utilize os comandos na secção [Obter as informações dos anfitriões Zookeeper e Mediador](#getkafkainfo) para definir os `$KAFKABROKERS` para esta sessão SSH.

2. Veja como cada sessão conta os registos que recebe do tópico. O total das duas sessões deve ser o mesmo que recebeu anteriormente de um consumidor.

O consumo pelos clientes dentro do mesmo grupo é processado pelas partições do tópico. Para o tópico `test` criado anteriormente, tem oito partições. Se abrir oito sessões SSH e iniciar um consumidor em todas as sessões, cada consumidor lê registos de uma única partição do tópico.

> [!IMPORTANT]
> Não podem existir mais instâncias de consumidor num grupo de consumidores do que partições. Neste exemplo, um grupo de consumidores pode incluir até oito consumidores, pois esse é o número de partições no tópico. Também pode ter vários grupos de consumidores, em que cada grupo não tem mais do que oito consumidores.

Os registos armazenados no Kafka são armazenados pela ordem em que são recebidos dentro de uma partição. Para obter uma entrega por ordem dos registos *dentro de uma partição*, crie um grupo de consumidores em que o número de instâncias de consumidor corresponde ao número de partições. Para obter uma entrega por ordem dos registos *dentro do tópico*, crie um grupo de consumidores com apenas uma instância de consumidor.

## <a name="streaming-api"></a>API de Transmissão em Fluxo

A API de transmissão em fluxo foi adicionada ao Kafka na versão 0.10.0; as versões anteriores dependem do Apache Spark ou Storm para o processamento de fluxo.

1. Se ainda não o fez, transfira os exemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) para o seu ambiente de desenvolvimento. Para o exemplo de transmissão em fluxo, utilize o projeto no diretório `streaming`.
   
    Este projeto contém apenas uma classe, `Stream`, que lê registos a partir do tópico `test` criado anteriormente. Faz a contagem da leitura de palavras e emite cada palavra e contagem para um tópico com o nome `wordcounts`. O tópico `wordcounts` é criado num passo posterior desta secção.

2. Na linha de comandos do seu ambiente de desenvolvimento, altere os diretórios para a localização do diretório `Streaming` e, em seguida, utilize o seguinte comando para criar um pacote de jar:

    ```bash
    mvn clean package
    ```

    Este comando cria um diretório com o nome `target`, que contém um ficheiro com o nome `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Utilize os seguintes comandos para copiar o ficheiro `kafka-streaming-1.0-SNAPSHOT.jar` para o cluster HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Substitua **SSHUSER** pelo utilizador SSH do seu cluster, e substitua **CLUSTERNAME** pelo nome do seu cluster. Quando lhe for pedido, introduza a palavra-passe do utilizador SSH.

4. Assim que o comando `scp` acabar de copiar o ficheiro, ligue ao cluster através de SSH e, em seguida, utilize o comando seguinte para criar o tópico `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. Em seguida, inicie o processo de transmissão em fluxo através do seguinte comando:
   
    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    Este comando inicia o processo de transmissão em fluxo em segundo plano.

6. Utilize o comando seguinte para enviar mensagens para o tópico `test`. Estas mensagens são processadas pelo exemplo de transmissão em fluxo:
   
    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. Utilize o seguinte comando para ver o resultado que é escrito no tópico `wordcounts` pelo processo de transmissão em fluxo:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
   
    > [!NOTE]
    > Para ver os dados, tem de pedir ao consumidor para imprimir a chave e o desserializador a utilizar para a chave e o valor. O nome da chave é a palavra, e o valor da chave contém a contagem.
   
    O resultado é semelhante ao seguinte texto:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
        a       13805
        snow    13637
   
    > [!NOTE]
    > A contagem aumenta sempre que uma palavra é encontrada.

7. Utilize __Ctrl + C__ para sair do consumidor e, em seguida, utilize o comando `fg` para trazer a tarefa de transmissão em fluxo do segundo para o primeiro plano. Utilize também __Ctrl + C__ para sair.

## <a name="data-high-availability"></a>Elevada disponibilidade de dados

Cada região do Azure (localização) fornece _domínios de falha_. Um domínio de falha é um agrupamento lógico de hardware subjacente num centro de dados do Azure. Cada domínio de falha partilha um comutador de rede e uma fonte de alimentação. As máquinas virtuais e os discos geridos que implementam os nós num cluster HDInsight são distribuídos por esses domínios de falha. Esta arquitetura limita o possível impacto de falhas físicas de hardware.

Para obter informações sobre o número de domínios de falha numa região, consulte o documento [Disponibilidade das máquinas virtuais Linux](../virtual-machines/linux/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

> [!IMPORTANT]
> É recomendável utilizar uma região do Azure que contenha três domínios de falha e um fator de replicação de 3.

Se tiver de utilizar uma região que contenha apenas dois domínios de falha, utilize um fator de replicação de 4 para distribuir as réplicas uniformemente entre os dois domínios de falha.

### <a name="kafka-and-fault-domains"></a>Kafka e os domínios de falha

O Kafka não está ciente dos domínios de falha. Durante a criação de réplicas de partição para tópicos, poderá não distribuir as réplicas corretamente para fins de elevada disponibilidade. Para garantir a elevada disponibilidade, utilize a [ferramenta de rebalanceamento de partições de Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Esta ferramenta deve ser executada numa sessão SSH para o nó principal do cluster do Kafka.

Para garantir a maior disponibilidade dos seus dados do Kafka, deve reequilibrar as réplicas de partições do seu tópico nos seguintes momentos:

* Quando é criado um novo tópico ou partição

* Quando expandir um cluster

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu as noções básicas para trabalhar com o Apache Kafka no HDInsight. Utilize o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Analisar os registos do Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters do Kafka](hdinsight-apache-kafka-mirroring.md)
* [Utilizar a transmissão em fluxo de Apache Spark (DStream) com o Kafka no HDInsight](hdinsight-apache-spark-with-kafka.md)
* [Utilizar a Transmissão em Fluxo Estruturada de Apache Spark com o Kafka no HDInsight](hdinsight-apache-kafka-spark-structured-streaming.md)
* [Utilizar o Apache Storm com Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Ligar ao Kafka através de uma Rede Virtual do Azure](hdinsight-apache-kafka-connect-vpn-gateway.md)
