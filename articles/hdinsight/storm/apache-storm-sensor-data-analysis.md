---
title: Analisar dados de sensores com Apache Storm e HBase | Microsoft Docs
description: "Saiba como ligar ao Apache Storm com uma rede virtual. Utilizar o Storm no HBase para processar dados de sensores de um hub de eventos e visualizá-lo com D3.js."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/19/2017
ms.author: larryfr
ms.openlocfilehash: 87c2aece68c5de06d683abf971b6c7ccf7f67a54
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analisar dados de sensor com Apache Storm, o Hub de eventos e o HBase no HDInsight (Hadoop)

Saiba como utilizar o Apache Storm no HDInsight para processar dados de sensores do Hub de eventos do Azure. Os dados são armazenados em seguida, para o Apache HBase no HDInsight e visualizados utilizando D3.js.

O modelo Azure Resource Manager utilizado neste documento demonstra como criar vários recursos do Azure num grupo de recursos. O modelo cria uma rede Virtual do Azure, dois clusters do HDInsight (Storm e HBase) e uma aplicação Web do Azure. Uma implementação de node.js de um dashboard web em tempo real é automaticamente implementada na aplicação web.

> [!NOTE]
> As informações neste documento e o exemplo neste documento exigem HDInsight versão 3.6.
>
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="architecture"></a>Arquitetura

![diagrama da arquitetura](./media/apache-storm-sensor-data-analysis/devicesarchitecture.png)

Neste exemplo é composta pelos seguintes componentes:

* **Os Hubs de eventos do Azure**: contém dados que são recolhidos dos sensores.
* **Storm no HDInsight**: fornece processamento em tempo real de dados a partir do Hub de eventos.
* **O HBase no HDInsight**: Fornece um arquivo de dados NoSQL persistente para os dados depois de ter sido processada pela Storm.
* **Serviço de rede Virtual do Azure**: permite as comunicações seguras entre o Storm no HDInsight e HBase nos clusters do HDInsight.
  
  > [!NOTE]
  > Uma rede virtual é necessária quando utilizar o cliente de Java HBase API. Não está exposta através do gateway público para clusters de HBase. Instalar a clusters HBase e Storm na mesma rede virtual permite que o cluster do Storm (ou qualquer outro sistema na rede virtual) aceder diretamente ao HBase através da API de cliente.

* **Web site do dashboard**: um dashboard de exemplo que gráficos de dados em tempo real.
  
  * O Web site está implementado no Node.js.
  * [Socket.IO](http://socket.io/) é utilizada para comunicação em tempo real entre a topologia do Storm e o Web site.
    
    > [!NOTE]
    > A utilização de Socket.io para comunicação é um detalhe de implementação. Pode utilizar qualquer estrutura de comunicações, tais como WebSockets não processados ou SignalR.

  * [D3.js](http://d3js.org/) é utilizado para os dados que são enviados para o Web site da graph.

> [!IMPORTANT]
> Dois clusters são necessárias, conforme não é não existe nenhum método suportado para criar um cluster do HDInsight para Storm e HBase.

A topologia lê dados de Hub de eventos utilizando o `org.apache.storm.eventhubs.spout.EventHubSpout` classe e escreve dados no HBase utilizando o `org.apache.storm.hbase.bolt.HBaseBolt` classe. Comunicação com o Web site é conseguida utilizando [socket.io client.java](https://github.com/nkzawa/socket.io-client.java).

O diagrama seguinte explica o esquema da topologia:

![diagrama da topologia](./media/apache-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Este diagrama é uma visão simplificada da topologia. Para cada partição no seu Hub de eventos, é criada uma instância de cada componente. Estas instâncias são distribuídas por nós do cluster e dados são encaminhados entre eles, da seguinte forma:
> 
> * Dados do spout para o parser tem balanceamento de carga.
> * Dados do parser para o Dashboard e o HBase são agrupados por ID de dispositivo, para que as mensagens do mesmo dispositivo sempre fluxo para o componente da mesmo.

### <a name="topology-components"></a>Componentes de topologia

* **Event Hub Spout**: O spout é fornecido como parte da versão do Apache Storm 0.10.0 e planos superiores.
  
  > [!NOTE]
  > Spout o Hub de eventos utilizado neste exemplo requer um Storm no clusters do HDInsight versão 3.5 ou 3.6.

* **ParserBolt.java**: os dados que são emitidos pelo spout for JSON não processado e, ocasionalmente mais do que um evento é emitido um momento. Este bolt lê os dados emitidos pelo spout e analisa a mensagem JSON. Bolt, em seguida, emite os dados como uma cadeia de identificação contém vários campos.
* **DashboardBolt.java**: este componente demonstra como utilizar a biblioteca de clientes Socket.io para Java para enviar dados em tempo real para o dashboard de web.
* **não hbase.yaml**: A definição de topologia utilizada quando executado no modo local. Não utilize componentes de HBase.
* **com hbase.yaml**: A definição de topologia utilizada ao executar a topologia no cluster. Utilizar o HBase componentes.
* **Dev.Properties**: as informações de configuração para o spout de Hub de eventos, bolt de HBase e componentes de dashboard.

## <a name="prepare-your-environment"></a>Preparar o ambiente

Antes de utilizar este exemplo, tem de preparar o ambiente de desenvolvimento. Também tem de criar um Hub de eventos do Azure, que é utilizado por este exemplo.

Terá dos seguintes itens instalados no seu ambiente de desenvolvimento:

* [NODE.js](http://nodejs.org/): utilizado para pré-visualizar dashboard web localmente no seu ambiente de desenvolvimento.
* [Java e o JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): utilizadas para desenvolver a topologia do Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): utilizada para criar e compilar o projeto.
* [Git](http://git-scm.com/): utilizado para transferir o projeto a partir do GitHub.
* Um **SSH** cliente: utilizada para ligar para os clusters do HDInsight baseado em Linux. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Para criar um hub de eventos, utilize os passos em [criar um hub de eventos](../../event-hubs/event-hubs-create.md) documento.

> [!IMPORTANT]
> Guarde o nome de hub de eventos, espaço de nomes e a chave para o RootManageSharedAccessKey. Estas informações são utilizadas para configurar a topologia do Storm.

Não precisa de um cluster do HDInsight. Os passos neste documento fornecem um modelo Azure Resource Manager que cria os recursos necessários para este exemplo. O modelo cria os seguintes recursos:
 
* Uma rede Virtual do Azure
* Um cluster Storm no HDInsight (baseado em Linux, dois nós de trabalho)
* O HBase no HDInsight cluster (baseado em Linux, dois nós de trabalho)
* Uma aplicação Web do Azure que aloja o dashboard da web

## <a name="download-and-configure-the-project"></a>Transferir e configurar o projeto

Utilize o seguinte para transferir o projeto a partir do GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Depois do comando for concluído, tem a seguinte estrutura de diretórios:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Este documento não entrar em detalhes completos de código incluídos neste exemplo. No entanto, o código totalmente é comentado.

Para configurar o projeto para ler a partir do Hub de eventos, abra o `hdinsight-eventhub-example/TemperatureMonitor/dev.properties` de ficheiros e adicionar as suas informações de Hub de eventos para as seguintes linhas:

```bash
eventhub.policy.key: the_key_for_RootManageSharedAccessKey
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>Compilar e testar localmente

> [!IMPORTANT]
> Com a topologia localmente requer um ambiente de desenvolvimento do Storm de trabalho. Para obter mais informações, consulte [configurar um ambiente de desenvolvimento do Storm](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html) em Apache.org.

Antes de testar, tem de iniciar o dashboard para ver o resultado da topologia e gerar dados a armazenar no Hub de eventos.

> [!IMPORTANT]
> O componente de HBase desta topologia não está ativo quando testar localmente. A API de Java para o cluster HBase não pode ser acedida de fora da rede Virtual do Azure que contém os clusters.

### <a name="start-the-web-application"></a>Iniciar a aplicação web

1. Abra uma linha de comandos e altere os diretórios para `hdinsight-eventhub-example/dashboard`. Utilize o seguinte comando para instalar as dependências necessárias para a aplicação web:
   
    ```bash
    npm install
    ```

2. Utilize o seguinte comando para iniciar a aplicação web:
   
    ```bash
    node server.js
    ```
   
    Verá uma mensagem semelhante ao seguinte texto:
   
        Server listening at port 3000

3. Abra um browser e introduza `http://localhost:3000/` como o endereço. É apresentada uma página semelhante ao seguinte imagem:
   
    ![dashboard da Web](./media/apache-storm-sensor-data-analysis/emptydashboard.png)
   
    Deixe esta linha de comandos aberta. Depois de teste, utilize Ctrl-C para parar o servidor web.

### <a name="generate-data"></a>Gerar dados

> [!NOTE]
> Os passos nesta secção utilizam Node.js para que possa ser utilizados em qualquer plataforma. Para outros exemplos de idioma, consulte o `SendEvents` diretório.

1. Abra uma nova linha de comandos, shell ou terminal e altere os diretórios para `hdinsight-eventhub-example/SendEvents/nodejs`. Para instalar as dependências necessárias para a aplicação, utilize o seguinte comando:

    ```bash
    npm install
    ```

2. Abra o `app.js` ficheiro num editor de texto e adicione as informações de Hub de eventos que obteve anteriormente:
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'Your-eventhub-namespace';
    // Event Hub Name
    var hubname ='Your-eventhub-name';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'RootManageSharedAccessKey';
    var my_key = 'Your-Key';
    ```
   
   > [!NOTE]
   > Este exemplo assume que está a utilizar `RootManageSharedAccessKey` para aceder ao seu hub de eventos.

3. Utilize o seguinte comando para inserir novas entradas no Hub de eventos:
   
    ```bash
    node app.js
    ```
   
    Pode ver várias linhas de saída que contêm os dados enviados para o Hub de eventos:
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="build-and-start-the-topology"></a>Criar e iniciar a topologia

1. Abra uma nova linha de comandos e altere os diretórios para `hdinsight-eventhub-example/TemperatureMonitor`. Para criar e a topologia do pacote, utilize o seguinte comando: 

    ```bash
    mvn clean package
    ```

2. Para iniciar a topologia no modo local, utilize o seguinte comando:

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * `--local`inicia a topologia no modo local.
    * `--filter`utiliza o `dev.properties` ficheiro para preencher a parâmetros na definição de topologia.
    * `resources/no-hbase.yaml`utiliza o `no-hbase.yaml` definição de topologia.
 
   Depois de iniciada, a topologia lê entradas de Hub de eventos e envia-os para o dashboard em execução no seu computador local. Deverá ver as linhas são apresentadas no dashboard do web, semelhante a imagem seguinte:
   
    ![dashboard com os dados](./media/apache-storm-sensor-data-analysis/datadashboard.png)

2. Enquanto o dashboard está em execução, utilize o `node app.js` comando dos passos anteriores para enviar dados de novo para os Event Hubs. Porque os valores de temperatura aleatoriamente são gerados, o gráfico deve ser atualizado para mostrar as alterações de grandes dimensões em temperatura.
   
   > [!NOTE]
   > Tem de estar no **hdinsight-eventhub-exemplo/SendEvents/Nodejs** diretório ao utilizar o `node app.js` comando.

3. Depois de verificar que o dashboard de atualizações, pare a topologia utilizando Ctrl + C. Pode utilizar Ctrl + C para parar o servidor local web também.

## <a name="create-a-storm-and-hbase-cluster"></a>Criar um cluster do Storm e HBase

Os passos nesta secção utilize um [modelo Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md) para criar um cluster de rede Virtual do Azure e um Storm e HBase na rede virtual. Além disso, o modelo cria uma aplicação Web do Azure e implementa uma cópia do dashboard para a mesma.

> [!NOTE]
> Uma rede virtual é utilizada para que a topologia em execução no cluster do Storm pode comunicar diretamente com o cluster de HBase utilizando a API de Java de HBase.

O modelo do Resource Manager utilizado neste documento está localizado num contentor de blob público em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json**.

1. Clique no botão seguinte para iniciar sessão no Azure e abrir o modelo do Resource Manager no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/apache-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Do **implementação personalizada** secção, introduza os seguintes valores:
   
    ![Parâmetros do HDInsight](./media/apache-storm-sensor-data-analysis/parameters.png)
   
   * **Nome do Cluster de base**: este valor é utilizado como o nome de base para o Storm e clusters do HBase. Por exemplo, introduzir **abc** cria um cluster do Storm com o nome **storm abc** e um cluster HBase com o nome **hbase abc**.
   * **Nome de utilizador de início de sessão do cluster**: O nome de utilizador de administrador para os clusters de Storm e HBase.
   * **Palavra-passe de início de sessão do cluster**: A palavra-passe de utilizador do administrador para os clusters de Storm e HBase.
   * **Nome de utilizador SSH**: O utilizador SSH para criar para os clusters de Storm e HBase.
   * **Palavra-passe SSH**: A palavra-passe para o utilizador SSH para os clusters de Storm e HBase.
   * **Localização**: A região que são criados os clusters no.
     
     Clique em **OK** para guardar os parâmetros.

3. Utilize o **Noções básicas** secção para criar um grupo de recursos ou selecione um existente.
4. No **localização do grupo de recursos** menu pendente, selecione a mesma localização selecionou para o **localização** parâmetro o **definições** secção.
5. Leia os termos e condições e, em seguida, selecione **concordo com os termos e condições indicadas acima**.
6. Por fim, verifique **afixar ao dashboard** e, em seguida, selecione **Compra**. Demora cerca de 20 minutos para criar os clusters.

Depois de criar os recursos, são apresentadas informações sobre o grupo de recursos.

![Grupo de recursos para a vnet e clusters](./media/apache-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Tenha em atenção que os nomes dos clusters de HDInsight são **storm BASENAME** e **hbase BASENAME**, onde BASENAME é o nome fornecido para o modelo. Utilize estes nomes num passo posterior ao estabelecer ligação com os clusters. Também tenha em atenção que o nome do dashboard site **basename dashboard**. Este valor é utilizado mais tarde neste documento.

## <a name="configure-the-dashboard-bolt"></a>Configurar o bolt de Dashboard

Para enviar dados para o dashboard implementado como uma aplicação web, tem de modificar as seguintes linhas de `dev.properties`ficheiro:

```yaml
dashboard.uri: http://localhost:3000
```

Alteração `http://localhost:3000` para `http://BASENAME-dashboard.azurewebsites.net` e guarde o ficheiro. Substitua **BASENAME** com o nome de base fornecidos no passo anterior. Também pode utilizar o grupo de recursos criado anteriormente para selecionar o dashboard e ver o URL.

## <a name="create-the-hbase-table"></a>Criar a tabela HBase

Para armazenar dados no HBase, vamos primeiro tem de criar uma tabela. Pré-criar recursos Storm tem de escrever, como a tentar criar recursos a partir de dentro de um Storm topologia pode resultar em várias instâncias tentar criar o recurso do mesmo. Criar os recursos fora da topologia e utilizar o Storm para leitura/escrita e de análise.

1. Utilize o SSH para ligar ao cluster HBase utilizando o utilizador do SSH e a palavra-passe fornecida para o modelo durante a criação do cluster. Por exemplo, se ligar utilizando o `ssh` comando, teria de utilizar a seguinte sintaxe:
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    Substitua `sshuser` com o nome de utilizador SSH que forneceu ao criar o cluster. Substitua `clustername` com o nome do cluster HBase.

2. A partir de sessão SSH, inicie a shell de HBase.
   
    ```bash
    hbase shell
    ```
   
    Assim que a shell foi carregadas, verá um `hbase(main):001:0>` linha.

3. A partir da HBase shell, introduza o seguinte comando para criar uma tabela para armazenar os dados de sensor:
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. Certifique-se de que a tabela foi criada utilizando o seguinte comando:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Esta ação devolve informações semelhante ao exemplo seguinte, que indica que existem 0 linhas na tabela.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Introduza `exit` para sair da shell de HBase:

## <a name="configure-the-hbase-bolt"></a>Configurar o bolt HBase

Ao escrever no HBase do Storm cluster, tem de fornecer o bolt HBase com os detalhes de configuração do HBase cluster.

1. Utilize um dos exemplos seguintes para obter o quórum de Zookeeper para o cluster de HBase:

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > Substitua `your_HDInsight_cluster_name` pelo nome do seu cluster do HDInsight. Para obter mais informações sobre como instalar o `jq` utilitário, consulte [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
    >
    > Quando lhe for pedido, introduza a palavra-passe para o início de sessão de administrador do HDInsight.

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name'
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > Substitua ' your_HDInsight_cluster_name com o nome do cluster do HDInsight. Quando lhe for pedido, introduza a palavra-passe para o início de sessão de administrador do HDInsight.
    >
    > Este exemplo requer o Azure PowerShell. Para obter mais informações sobre como utilizar o Azure PowerShell, consulte [começar com o Azure PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6)

    As informações devolvidas por estes exemplos são semelhantes ao seguinte texto:

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    Estas informações são utilizadas por Storm para comunicar com o cluster de HBase.

2. Modificar o `dev.properties` de ficheiros e adicionar as informações de quórum Zookeeper para a seguinte linha:

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Criar pacote e implementar a solução para o HDInsight

No seu ambiente de desenvolvimento, utilize os seguintes passos para implementar a topologia do Storm no cluster do storm.

1. Do `TemperatureMonitor` diretório, utilize o seguinte comando para efetuar uma nova compilação e criar um JAR do pacote do seu projeto:
   
        mvn clean package
   
    Este comando cria um ficheiro denominado `TemperatureMonitor-1.0-SNAPSHOT.jar in the `destino ' diretório do seu projeto.

2. Utilizar o scp para carregar o `TemperatureMonitor-1.0-SNAPSHOT.jar` e `dev.properties` ficheiros ao cluster do Storm. No exemplo seguinte, substitua `sshuser` com o utilizador SSH que forneceu ao criar o cluster, e `clustername` com o nome do cluster do Storm. Quando lhe for pedido, introduza a palavra-passe para o utilizador SSH.
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > Poderá demorar vários minutos a carregar os ficheiros.

    Para obter mais informações sobre como utilizar o `scp` e `ssh` comandos com o HDInsight, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)

3. Depois do ficheiro foi carregado, ligue-se ao cluster Storm através de SSH.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` com o nome de utilizador SSH. Substitua `clustername` com o nome de cluster do Storm.

4. Para iniciar a topologia, utilize o seguinte comando da sessão SSH:
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * `--remote`submete a topologia para o serviço de Nimbus, que distribui-lo para os nós do supervisor no cluster.
    * `--filter`utiliza o `dev.properties` ficheiro para preencher a parâmetros na definição de topologia.
    * `-R /with-hbase.yaml`utiliza o `with-hbase.yaml` topologia incluída no pacote.

5. Depois de iniciado a topologia, abra um browser para o Web site publicadas no Azure, em seguida, utilize o `node app.js` comando para enviar dados para Event Hub. Deverá ver o dashboard de web para apresentar as informações de atualização.
   
    ![dashboard](./media/apache-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Ver os dados de HBase

Utilize os seguintes passos para ligar ao HBase e certifique-se de que os dados escritos para a tabela:

1. Utilize o SSH para ligar ao HBase cluster.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` com o nome de utilizador SSH. Substitua `clustername` com o nome do cluster HBase.

2. A partir de sessão SSH, inicie a shell de HBase.
   
    ```bash
    hbase shell
    ```
   
    Assim que a shell foi carregadas, verá um `hbase(main):001:0>` linha.

3. Ver as linhas da tabela:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Este comando devolve informações semelhante para o seguinte texto, com a indicação de que não há dados na tabela.
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > Esta operação de análise devolve um máximo de 10 linhas da tabela.

## <a name="delete-your-clusters"></a>Eliminar os clusters

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Para eliminar os clusters, armazenamento e a aplicação web de uma só vez, elimine o grupo de recursos que contém-los.

## <a name="next-steps"></a>Passos seguintes

Para obter mais exemplos de topologias do Storm com o HDInsight, consulte [topologias de exemplo para Storm no HDInsight](apache-storm-example-topology.md)

Para mais informações sobre o Apache Storm, consulte o [Apache Storm](https://storm.incubator.apache.org/) site.

Para obter mais informações sobre o HBase no HDInsight, consulte o [HBase com o HDInsight Overview](../hbase/apache-hbase-overview.md).

Para mais informações sobre Socket.io, consulte o [socket.io](http://socket.io/) site.

Para mais informações sobre D3.js, consulte [D3.js - documentos de orientada por dados](http://d3js.org/).

Para obter informações sobre como criar topologias em Java, consulte [topologias de Java desenvolver para o Apache Storm no HDInsight](apache-storm-develop-java-topology.md).

Para obter informações sobre como criar topologias no .NET, consulte [desenvolver topologias c# para Apache Storm no HDInsight com o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
