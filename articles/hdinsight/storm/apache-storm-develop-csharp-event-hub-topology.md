---
title: Processar os eventos provenientes dos Hubs de eventos com o Storm - Azure HDInsight | Microsoft Docs
description: Saiba como processar os dados a partir de Event Hubs do Azure com uma topologia de c# Storm criada no Visual Studio, utilizando as ferramentas do HDInsight para Visual Studio.
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: larryfr
ms.openlocfilehash: 9ad160377a8779ae917e6fd2d605ee01b12c3e2a
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Processar eventos provenientes dos Hubs de eventos do Azure com o Storm no HDInsight (c#)

Saiba como trabalhar com os Hubs de eventos do Azure do Apache Storm no HDInsight. Este documento utiliza uma topologia do Storm c# para ler e escrever dados a partir de Evbent Hubs

> [!NOTE]
> Para uma versão de Java deste projeto, consulte [processar eventos provenientes dos Hubs de eventos do Azure com o Storm no HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

Os passos neste documento utilizam SCP.NET, um pacote NuGet que torna mais fácil criar topologias c# e componentes para utilização com o Storm no HDInsight.

> [!IMPORTANT]
> Enquanto os passos neste documento dependem de um ambiente de desenvolvimento do Windows com o Visual Studio, o projeto compilado pode ser submetido para um cluster Storm no HDInsight que utiliza o Linux. Apenas os clusters baseados em Linux criados após 28 de Outubro de 2016, suportam SCP.NET topologias.

HDInsight 3.4 e maior utilização Mono a execução de topologias c#. O exemplo utilizado neste documento funciona com o HDInsight 3.6. Se pretender criar as suas próprias soluções de .NET para o HDInsight, verifique o [compatibilidade Mono](http://www.mono-project.com/docs/about-mono/compatibility/) documento para incompatibilidades potenciais.

> [!WARNING]
> Se tiver algum problema ao compilar projetos que utilizam a versão SCP.NET 1.0.0.x, contacte o suporte da Microsoft para obter assistência.

### <a name="cluster-versioning"></a>Controlo de versões de cluster

O pacote Microsoft.SCP.Net.SDK NuGet que utilizar para o seu projeto têm de corresponder a versão principal do Storm instalado no HDInsight. As versões do HDInsight 3.5 e 3.6 utilizam o Storm 1. x, pelo que deverá utilizar SCP.NET versão 1.0.x.x com esses clusters.

> [!IMPORTANT]
> O exemplo neste documento espera um 3.5 HDInsight ou 3,6 cluster.
>
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

Topologias de c# também têm de visar o .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Como trabalhar com os Event Hubs

A Microsoft fornece um conjunto de componentes de Java que podem ser utilizadas para comunicar com os Hubs de eventos de uma topologia do Storm. Pode encontrar o ficheiro de arquivo (JAR) do Java que contém uma versão compatível do HDInsight 3.6 estes componentes [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> Enquanto os componentes são escritos em Java, pode utilizá-los facilmente a partir de uma topologia de c#.

Os seguintes componentes são utilizados neste exemplo:

* __EventHubSpout__: lê dados a partir do Event Hubs.
* __EventHubBolt__: escreve dados para Event Hubs.
* __EventHubSpoutConfig__: utilizado para configurar EventHubSpout.
* __EventHubBoltConfig__: utilizado para configurar EventHubBolt.

### <a name="example-spout-usage"></a>Exemplo de utilização de spout

SCP.NET fornece métodos para adicionar um EventHubSpout à sua topologia. Estes métodos tornam mais fácil adicionar um spout que utilizar os métodos genéricos para adicionar um componente de Java. O exemplo seguinte demonstra como criar um spout utilizando o __SetEventHubSpout__ e **EventHubSpoutConfig** métodos fornecidos pela SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

O exemplo anterior cria um novo componente spout denominado __EventHubSpout__e configura-lo para comunicar com um hub de eventos. A sugestão de paralelismo para o componente está definida para o número de partições de eventos hub. Esta definição permite Storm criar uma instância do componente para cada partição.

### <a name="example-bolt-usage"></a>Exemplo de utilização de bolt

Utilize o **JavaComponmentConstructor** método para criar uma instância do bolt. O exemplo seguinte demonstra como criar e configurar uma nova instância do **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]
> Este exemplo utiliza uma expressão de Clojure transmitida como uma cadeia, em vez de utilizar **JavaComponentConstructor** para criar um **EventHubBoltConfig**, como o exemplo de spout foi. O método funciona. Utilize o método que funciona melhor para si.

## <a name="download-the-completed-project"></a>Transferir o projeto foi concluído

Pode transferir uma versão completa do projeto criado neste tutorial do [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). No entanto, ainda tem de fornecer as definições de configuração seguindo os passos neste tutorial.

### <a name="prerequisites"></a>Pré-requisitos

* Um [Apache Storm no clusters do HDInsight versão 3.5 ou 3.6](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]
    > O exemplo utilizado neste documento requer Storm no HDInsight versão 3.5 ou 3.6. Este não funciona com versões anteriores do HDInsight, devido a alterações de nome de classe de última hora. Para uma versão deste exemplo que funciona com clusters anteriores, consulte [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Um [hub de eventos do Azure](../../event-hubs/event-hubs-create.md).

* O [SDK .NET do Azure](http://azure.microsoft.com/downloads/).

* O [ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* 1.8 do Java JDK ou posterior no seu ambiente de desenvolvimento. Transferências JDK estão disponíveis no [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * O **JAVA_HOME** variável de ambiente tem de apontar para o diretório que contém o Java.
  * O **%JAVA_HOME%/bin** diretório tem de estar no caminho.

## <a name="download-the-event-hubs-components"></a>Transferir os componentes de Event Hubs

Transfira o spout de Event Hubs e bolt componente [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Criar um diretório com o nome `eventhubspout`e guarde o ficheiro para o diretório.

## <a name="configure-event-hubs"></a>Configurar os Event Hubs

Os Event Hubs é a origem de dados para este exemplo. Utilize as informações na secção "Criar um hub de eventos" [introdução ao Event Hubs](../../event-hubs/event-hubs-create.md).

1. Depois do hub de eventos foi criado, ver o **EventHub** painel no Azure portal e selecione **políticas de acesso partilhado**. Selecione **+ adicionar** para adicionar as políticas seguintes:

   | Nome | Permissões |
   | --- | --- |
   | Escritor |Enviar |
   | leitor |Escutar |

    ![Janela de políticas de acesso de captura de ecrã da partilha](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Selecione o **leitor** e **escritor** políticas. Copie e guarde o valor de chave primária para ambas as políticas, como estes valores são utilizados mais tarde.

## <a name="configure-the-eventhubwriter"></a>Configurar o EventHubWriter

1. Se já não instalou a versão mais recente das ferramentas HDInsight para Visual Studio, consulte [começar a utilizar as ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Transferir a solução de [eventhub-storm-híbrida](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. No **EventHubWriter** projeto, abra o **App. config** ficheiro. Utilize as informações do hub de eventos que configurou anteriormente para preencher o valor para as seguintes chaves:

   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |escritor (se utilizou um nome diferente para a política com *enviar* permissão, utilizá-lo em vez disso.) |
   | EventHubPolicyKey |A chave para a política de escritor. |
   | EventHubNamespace |O espaço de nomes que contém o hub de eventos. |
   | EventHubName |O nome de hub de eventos. |
   | EventHubPartitionCount |O número de partições num hub de eventos. |

4. Guarde e feche o **App. config** ficheiro.

## <a name="configure-the-eventhubreader"></a>Configurar o EventHubReader

1. Abra o **EventHubReader** projeto.

2. Abra o **App. config** de ficheiros para o **EventHubReader**. Utilize as informações do hub de eventos que configurou anteriormente para preencher o valor para as seguintes chaves:

   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |leitor (se utilizou um nome diferente para a política com *escutar* permissão, utilizá-lo em vez disso.) |
   | EventHubPolicyKey |A chave para a política de leitor. |
   | EventHubNamespace |O espaço de nomes que contém o hub de eventos. |
   | EventHubName |O nome de hub de eventos. |
   | EventHubPartitionCount |O número de partições num hub de eventos. |

3. Guarde e feche o **App. config** ficheiro.

## <a name="deploy-the-topologies"></a>Implementar as topologias

1. De **Explorador de soluções**, clique com botão direito do **EventHubReader** projeto e selecione **envio para Storm no HDInsight**.

    ![Captura de ecrã do Explorador de soluções, com o envio para Storm no HDInsight realçado](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. No **submeter topologia** caixa de diálogo, selecione o **Cluster do Storm**. Expanda **configurações adicionais**, selecione **caminhos de ficheiro de Java**, selecione **...** e selecione o diretório que contém o ficheiro JAR que transferiu anteriormente. Por fim, clique em **submeter**.

    ![Caixa de diálogo de captura de ecrã da topologia de envio](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. Quando foi submetida a topologia, o **Visualizador de topologias do Storm** aparece. Para ver informações sobre a topologia, selecione o **EventHubReader** topologia no painel esquerdo.

    ![Captura de ecrã do Visualizador de topologias Storm](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. De **Explorador de soluções**, clique com botão direito do **EventHubWriter** projeto e selecione **envio para Storm no HDInsight**.

5. No **submeter topologia** caixa de diálogo, selecione o **Cluster do Storm**. Expanda **configurações adicionais**, selecione **caminhos de ficheiro de Java**, selecione **...** e selecione o diretório que contém o ficheiro JAR que transferiu anteriormente. Por fim, clique em **submeter**.

6. Quando foi submetida a topologia, atualize a lista de topologia no **Visualizador de topologias do Storm** para verificar que ambas as topologias estão em execução no cluster.

7. No **Visualizador de topologias do Storm**, selecione o **EventHubReader** topologia.

8. Para abrir o componente de resumo para o bolt, faça duplo clique o **LogBolt** componente no diagrama.

9. No **executores** secção, selecione uma das ligações no **porta** coluna. Esta ação apresenta informações registadas pelo componente. As informações com sessão iniciada são semelhantes ao seguinte texto:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Parar as topologias

Para parar as topologias, selecione cada topologia no **Visualizador de topologia do Storm**, em seguida, clique em **Kill**.

![Captura de ecrã do Storm topologia Visualizador, com o botão de Kill realçado](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Elimina o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu como utilizar o Hubs de eventos de Java spout e bolt a partir de uma topologia de c# para trabalhar com dados de Event Hubs do Azure. Para obter mais informações sobre como criar topologias c#, consulte o seguinte:

* [Desenvolver topologias c# para Apache Storm no HDInsight com o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Guia de programação de SCP](apache-storm-scp-programming-guide.md)
* [Topologias de exemplo para Storm no HDInsight](apache-storm-example-topology.md)
