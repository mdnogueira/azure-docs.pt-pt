---
title: Utilizar Kafka do Apache Storm no HDInsight - Azure | Microsoft Docs
description: "Apache Kafka é instalado com Apache Storm no HDInsight. Saiba como escrever Kafka e, em seguida, lidos a partir, utilizar os componentes de KafkaBolt e KafkaSpout fornecidos com o Storm. Além disso, saiba como utilizar a estrutura de Flux para definir e submeter topologias do Storm."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: 50a22877241c77ccb1a7df24ab7df006094a439f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>Utilizar Kafka do Apache Storm no HDInsight

Saiba como utilizar o Apache Storm para leem e escrevem para Apache Kafka. Este exemplo também demonstra como guardar dados de uma topologia do Storm para o sistema de ficheiros compatível com HDFS utilizado pelo HDInsight.

> [!NOTE]
> Os passos neste documento, criar um grupo de recursos do Azure que contém tanto um Storm no HDInsight e um Kafka num cluster do HDInsight. Esses clusters são ambos localizado dentro de uma rede Virtual do Azure, que permite que o cluster do Storm comunicar diretamente com o Kafka do cluster.
> 
> Quando tiver terminado com os passos neste documento, lembre-se eliminar os clusters para evitar encargos em excesso.

## <a name="get-the-code"></a>Obter o código

O código de exemplo utilizado neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Para compilar a este projeto, terá da seguinte configuração para o seu ambiente de desenvolvimento:

* [1.8 do Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior. HDInsight 3.5 ou superior necessário Java 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Um cliente SSH (é necessário o `ssh` e `scp` comandos) – para obter informações, consulte [utilizar o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Num editor de texto ou IDE.

As seguintes variáveis de ambiente podem ser definidas quando instalar o Java e o JDK na sua estação de trabalho de desenvolvimento. No entanto, deve verificar que existe e que contêm os valores corretos para o seu sistema.

* `JAVA_HOME`-devem apontar para o diretório onde está instalado o JDK.
* `PATH`-deve conter os seguintes caminhos:
  
    * `JAVA_HOME`(ou o caminho equivalente).
    * `JAVA_HOME\bin`(ou o caminho equivalente).
    * O diretório onde está instalado o Maven.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight fornece acesso para os mediadores Kafka através da internet pública. Tudo o que sua Kafka tem de ser na mesma rede virtual do Azure que os nós do Kafka cluster. Neste exemplo, clusters Kafka tanto Storm estão localizados numa rede virtual do Azure. O diagrama seguinte mostra como a comunicação flua entre os clusters:

![Diagrama de clusters Storm e Kafka uma Azure virtual network](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Outros serviços em cluster, como o SSH e Ambari podem ser acedidos através da internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e os URIs utilizados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Pode criar uma Azure virtual network, Kafka, e clusters de Storm manualmente, é mais fáceis de utilizar um modelo Azure Resource Manager. Utilize os seguintes passos para implementar uma Azure virtual network, Kafka e Storm clusters a sua subscrição do Azure.

1. Utilize o botão seguinte para iniciar sessão no Azure e abrir o modelo no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    O modelo Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet-v2.json**. Cria os seguintes recursos:
    
    * Grupo de recursos do Azure
    * Rede Virtual do Azure
    * Conta de armazenamento do Azure
    * Kafka no HDInsight versão 3.6 (três nós de trabalho)
    * Storm no HDInsight versão 3.6 (três nós de trabalho)

  > [!WARNING]
  > Para garantir a disponibilidade do Kafka no HDInsight, o cluster tem de conter pelo menos três nós de trabalho. Este modelo cria um cluster Kafka contém três nós de trabalho.

2. Utilize as seguintes orientações para preencher as entradas no **implementação personalizada** secção:
   
    ![Implementação personalizada do HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Grupo de recursos**: criar um grupo ou selecione um existente. Este grupo contém o cluster do HDInsight.
   
    * **Localização**: selecione uma localização geograficamente perto de si.

    * **Nome do Cluster de base**: este valor é utilizado como o nome de base para o Storm e Kafka clusters. Por exemplo, introduzir **hdi** cria um cluster do Storm com o nome **storm hdi** e um cluster de Kafka denominado **kafka hdi**.
   
    * **Nome de utilizador de início de sessão do cluster**: O nome de utilizador de administrador para os clusters de Storm e Kafka.
   
    * **Palavra-passe de início de sessão do cluster**: A palavra-passe de utilizador do administrador para os clusters de Storm e Kafka.
    
    * **Nome de utilizador SSH**: O utilizador SSH para criar para os clusters de Storm e Kafka.
    
    * **Palavra-passe SSH**: A palavra-passe para o utilizador SSH para os clusters de Storm e Kafka.

3. Leia o **termos e condições**e, em seguida, selecione **concordo com os termos e condições indicadas acima**.

4. Por fim, verifique **afixar ao dashboard** e, em seguida, selecione **Compra**. Demora cerca de 20 minutos para criar os clusters.

Depois de criar os recursos, é apresentada a secção para o grupo de recursos.

![Secção de grupo de recurso para a vnet e clusters](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Tenha em atenção que os nomes dos clusters de HDInsight são **storm BASENAME** e **kafka BASENAME**, onde BASENAME é o nome fornecido para o modelo. Utilize estes nomes em passos posteriores quando ligar para os clusters.

## <a name="understanding-the-code"></a>Noções sobre o código

Este projeto contém duas topologias:

* **KafkaWriter**: definido pelo **writer.yaml** ficheiro, esta topologia escreve frases aleatórios Kafka utilizando KafkaBolt fornecido com Apache Storm.

    Esta topologia utiliza personalizadas **SentenceSpout** componente para gerar frases aleatórios.

* **KafkaReader**: definido pelo **reader.yaml** ficheiro, esta topologia lê dados a partir da Kafka utilizando KafkaSpout fornecido com Apache Storm, em seguida, regista os dados stdout.

    Esta topologia utiliza o Storm HdfsBolt para escrever dados para o armazenamento de predefinido para o cluster do Storm.
### <a name="flux"></a>Flux

As topologias são definidas utilizando [Flux](https://storm.apache.org/releases/1.1.0/flux.html). Flux foi introduzida no Storm 0.10.x e permite separar a configuração da topologia do código. Para as topologias que utilizam o framework Flux, a topologia está definida num ficheiro YAML. O ficheiro YAML pode ser incluído como parte da topologia. Também pode ser um ficheiro autónomo utilizado ao submeter a topologia. Flux também suporta a substituição das variáveis em tempo de execução, que é utilizada neste exemplo.

Os seguintes parâmetros são definidos em tempo de execução para estes topologias:

* `${kafka.topic}`: O nome do tópico Kafka que as topologias de leitura/escritam.

* `${kafka.broker.hosts}`: Os anfitriões que o Kafka mediadores executar. As informações de Mediador utilizadas pelo KafkaBolt escrever Kafka.

* `${kafka.zookeeper.hosts}`: Os anfitriões que Zookeeper é executado no Kafka cluster.

Para obter mais informações sobre topologias Flux, consulte [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="download-and-compile-the-project"></a>Transferir e compilar o projeto

1. No seu ambiente de desenvolvimento, transfira o projeto de [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), abra uma linha de comandos e mude de diretório para a localização que transferiu o projeto.

2. Do **hdinsight-storm-java-kafka** diretório, utilize o seguinte comando para compilar o projeto e criar um pacote de implementação:

  ```bash
  mvn clean package
  ```

    O processo de pacote cria um ficheiro denominado `KafkaTopology-1.0-SNAPSHOT.jar` no `target` diretório.

3. Utilize os seguintes comandos para copiar o pacote para o cluster Storm no HDInsight. Substitua **USERNAME** com o nome de utilizador do SSH para o cluster. Substitua **BASENAME** com o nome de base utilizado ao criar o cluster.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Quando lhe for pedido, introduza a palavra-passe que utilizou quando criar os clusters.

## <a name="configure-the-topology"></a>Configurar a topologia

1. Utilize um dos seguintes métodos para detetar os anfitriões de Mediador Kafka:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > O exemplo de Bash assume que `$CLUSTERNAME` contém o nome do cluster do HDInsight. Também parte do princípio que [jq](https://stedolan.github.io/jq/) está instalado. Quando lhe for pedido, introduza a palavra-passe para a conta de início de sessão do cluster.

    O valor devolvido é semelhante ao seguinte texto:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Enquanto pode ser mais do que dois mediador anfitriões para o cluster, não terá de fornecer uma lista completa de todos os anfitriões para os clientes. Um ou dois são suficiente.

2. Utilize um dos seguintes métodos para detetar os anfitriões de Kafka Zookeeper:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > O exemplo de Bash assume que `$CLUSTERNAME` contém o nome do cluster do HDInsight. Também parte do princípio que [jq](https://stedolan.github.io/jq/) está instalado. Quando lhe for pedido, introduza a palavra-passe para a conta de início de sessão do cluster.

    O valor devolvido é semelhante ao seguinte texto:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Enquanto existirem mais de dois nós de Zookeeper, não terá de fornecer uma lista completa de todos os anfitriões para os clientes. Um ou dois são suficiente.

    Guarde este valor, porque é utilizado mais tarde.

3. Editar o `dev.properties` ficheiro na raiz do projeto. Adicione as informações de anfitriões mediador e Zookeeper para as linhas com correspondência neste ficheiro. O exemplo seguinte é configurado utilizando os valores de exemplo dos passos anteriores:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Guardar o `dev.properties` de ficheiros e, em seguida, utilize o seguinte comando carregá-lo para o cluster do Storm:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
    ```

    Substitua **USERNAME** com o nome de utilizador do SSH para o cluster. Substitua **BASENAME** com o nome de base utilizado ao criar o cluster.

## <a name="start-the-writer"></a>Iniciar o escritor

1. Utilize o seguinte para ligar ao cluster Storm através de SSH. Substitua **USERNAME** com o nome de utilizador do SSH utilizado quando criar o cluster. Substitua **BASENAME** com o nome de base utilizado ao criar o cluster.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    Quando lhe for pedido, introduza a palavra-passe que utilizou quando criar os clusters.
   
    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A ligação de SSH, utilize o seguinte comando para criar o tópico Kafka utilizado pela topologia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Substitua `$KAFKAZKHOSTS` com o Zookeeper alojar informações que obteve na secção anterior.

2. A ligação de SSH para o cluster do Storm, utilize o seguinte comando para iniciar a topologia de escritor:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Os parâmetros utilizados com este comando são:

    * `org.apache.storm.flux.Flux`: Utilize Flux para configurar e executar esta topologia.

    * `--remote`: Submeta a topologia para Nimbus. A topologia é distribuída em todos os nós de trabalho no cluster.

    * `-R /writer.yaml`: Utilize o `writer.yaml` ficheiros para configurar a topologia. `-R`indica que este recurso está incluído no ficheiro jar. É por isso, na raiz da jar, `/writer.yaml` é o caminho para o mesmo.

    * `--filter`: A preencher a entradas no `writer.yaml` topologia utilizando valores no `dev.properties` ficheiro. Por exemplo, o valor da `kafka.topic` entrada do ficheiro é utilizada para substituir o `${kafka.topic}` entrada na definição de topologia.

5. Assim que a topologia foi iniciada, utilize o seguinte comando para verificar que está a escrever dados para o tópico Kafka:

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    Substitua `$KAFKAZKHOSTS` com o Zookeeper alojar informações que obteve na secção anterior.

    Este comando utiliza um script fornecido com Kafka para monitorizar o tópico. Depois de um momento, deve começar a devolver aleatórios frases que foi escritos para o tópico. O resultado é semelhante ao seguinte exemplo:

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Utilize Ctrl + c para terminar o script.

## <a name="start-the-reader"></a>Iniciar o leitor

1. A partir de sessão SSH para o cluster do Storm, utilize o seguinte comando para iniciar a topologia de leitor:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Assim que for iniciada a topologia, abra a IU do Storm. Este web IU está localizada em `https://storm-BASENAME.azurehdinsight.net/stormui`. Substitua __BASENAME__ com o nome de base utilizado quando o cluster foi criado. 

    Quando lhe for pedido, utilize o nome de início de sessão de administrador (predefinição, `admin`) e palavra-passe utilizada quando o cluster foi criado. Verá uma página web semelhante para a imagem seguinte:

    ![IU do Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Na IU do Storm, selecione o __kafka leitor__ ligação no __resumo da topologia__ secção para visualizar informações sobre o __kafka leitor__ topologia.

    ![Secção de resumo da topologia da IU da web de Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Para apresentar informações sobre as instâncias do componente registador bolt, selecione o __registador bolt__ ligação no __Bolts (tempo)__ secção.

    ![Ligação registador bolt na secção bolts](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. No __executores__ secção, selecione uma ligação no __porta__ coluna para visualizar informações de registo sobre esta instância do componente.

    ![Executores ligação](./media/hdinsight-apache-storm-with-kafka/executors.png)

    O registo contém um registo dos dados de leitura do tópico Kafka. As informações no registo são semelhantes ao seguinte texto:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Parar as topologias

A partir de uma sessão SSH para o cluster do Storm, utilize os seguintes comandos para parar as topologias do Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Eliminar o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Uma vez que os passos neste documento criar ambos os clusters no mesmo grupo de recursos do Azure, pode eliminar o grupo de recursos no portal do Azure. A eliminar o grupo de recursos remove todos os recursos que criou ao seguir este documento.

## <a name="next-steps"></a>Passos seguintes

Para obter mais topologias de exemplo que podem ser utilizadas com o Storm no HDInsight, consulte [topologias do Storm de exemplo e componentes](storm/apache-storm-example-topology.md).

Para obter informações sobre como implementar e monitorizar topologias no HDInsight baseado em Linux, consulte [implementar e gerir topologias Apache Storm no HDInsight baseado em Linux](storm/apache-storm-deploy-monitor-topology-linux.md)