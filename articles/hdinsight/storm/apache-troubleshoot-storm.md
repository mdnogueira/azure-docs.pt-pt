---
title: "Resolver problemas de Storm através do Azure HDInsight | Microsoft Docs"
description: "Obtenha respostas a questões recorrentes sobre como utilizar o Apache Storm com o Azure HDInsight."
keywords: "FAQ do HDInsight, Storm, do Azure, manual, problemas comuns de resolução de problemas"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.openlocfilehash: 70a3d762431d90acdd6ed2a432a569f34d0ce447
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Resolver problemas de Storm através do Azure HDInsight

Saiba mais sobre os principais problemas e as resoluções para trabalhar com payloads do Apache Storm no Apache Ambari.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Como acedo a IU do Storm num cluster
Tem duas opções para aceder a IU do Storm num browser:

### <a name="ambari-ui"></a>IU do Ambari
1. Aceda ao dashboard do Ambari.
2. Na lista de serviços, selecione **Storm**.
3. No **ligações rápidas** menu, selecione **IU do Storm**.

### <a name="direct-link"></a>Ligação direta
Pode aceder a IU do Storm no seguinte URL:

https://\<nome DNS de cluster\>/stormui

Exemplo:

 https://stormcluster.azurehdinsight.NET/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Como transferir as informações de ponto de verificação do Storm event hub spout da topologia de um para outro

Quando desenvolver topologias ler a partir do Event Hubs do Azure utilizando o hub de eventos do HDInsight Storm spout o ficheiro. JAR, tem de implementar uma topologia que tem o mesmo nome num cluster de novo. No entanto, tem de manter os dados de ponto de verificação que foi consolidados para Apache ZooKeeper no cluster antigo.

### <a name="where-checkpoint-data-is-stored"></a>Armazenar dados de ponto de verificação
Dados de ponto de verificação para desvios são armazenados pelo spout de hub de eventos no ZooKeeper em dois caminhos de raiz:
- Pontos de verificação do Nontransactional spout são armazenados no /eventhubspout.
- Dados de ponto de verificação de spout transacional são armazenados na / transacional.

### <a name="how-to-restore"></a>Como restaurar
Para obter os scripts e as bibliotecas que utilizar para exportar dados para fora ZooKeeper e, em seguida, importar os dados de volta para ZooKeeper com um novo nome, consulte [exemplos de HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

A pasta de lib tem ficheiros. JAR que contém a implementação para a operação de exportação/importação. A pasta de bash tem um script de exemplo que demonstra como exportar dados do servidor ZooKeeper no cluster antigo e, em seguida, importá-lo novamente para o servidor ZooKeeper no novo cluster.

Execute o [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) script de nós de ZooKeeper para exportar e, em seguida, importar dados. Atualize o script para a versão correta do Hortonworks Data Platform (HDP). (Estamos a trabalhar efetuar estes scripts genérico no HDInsight. Genéricos podem executar scripts de qualquer nó no cluster sem modificações pelo utilizador.)

O comando de exportação escreve os metadados de um caminho de sistema de ficheiros distribuído do Apache Hadoop (HDFS) (num arquivo de Blob Storage do Azure ou do Azure Data Lake Store) numa localização que definir.

### <a name="examples"></a>Exemplos

#### <a name="export-offset-metadata"></a>Exportar metadados de deslocamento
1. Utilize o SSH para ir para o cluster de ZooKeeper no cluster a partir do qual o ponto de verificação deslocamento tem de ser exportado.
2. Execute o seguinte comando (depois de atualizar a cadeia de versão HDP) para exportar dados de deslocamento de ZooKeeper para o caminho do HDFS /stormmetadta/zkdata:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importar metadados de deslocamento
1. Utilize o SSH para ir para o cluster de ZooKeeper no cluster a partir do qual o ponto de verificação deslocamento tem de ser exportado.
2. Execute o seguinte comando (depois de atualizar a cadeia de versão HDP) para importar dados de deslocamento de ZooKeeper do /stormmetadata/zkdata de caminho do HDFS para o servidor ZooKeeper no cluster de destino:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Eliminar os metadados de deslocamento para que as topologias podem começar a processar os dados de início ou de um timestamp que o utilizador escolhe
1. Utilize o SSH para ir para o cluster de ZooKeeper no cluster a partir do qual o ponto de verificação deslocamento tem de ser exportado.
2. Execute o seguinte comando (depois de atualizar a cadeia de versão HDP) para eliminar todos os dados de deslocamento de ZooKeeper do cluster atual:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Como localizar o binários de Storm num cluster
Binários do Storm para a pilha HDP atual são /usr/hdp/current/storm-client. A localização é o mesmo para nós principais e nós de trabalho.
 
Podem existir vários binários para versões HDP específicas no /usr/hdp (por exemplo, /usr/hdp/2.5.0.1233/storm). A pasta de /usr/hdp/current/storm-client é symlinked para a versão mais recente que está em execução no cluster.

Para obter mais informações, consulte [ligar a um cluster do HDInsight utilizando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Como posso determinar a topologia de implementação de um cluster do Storm
Em primeiro lugar, identifique todos os componentes que são instalados com o HDInsight Storm. Um cluster do Storm é constituída por quatro categorias de nó:

* Nós de gateway
* Nós de cabeçalho
* Nós de zooKeeper
* Nós de trabalho
 
### <a name="gateway-nodes"></a>Nós de gateway
Um nó de gateway é um gateway e o serviço de proxy inverso que permite acesso de público para um serviço de gestão do Active Directory Ambari. Ele também faz eleição de leader do Ambari.
 
### <a name="head-nodes"></a>Nós de cabeçalho
Nós principais do Storm executam os seguintes serviços:
* Nimbus
* Servidor do Ambari
* Servidor de métricas do Ambari
* Recoletor de métricas do Ambari
 
### <a name="zookeeper-nodes"></a>Nós de zooKeeper
O HDInsight inclui um quórum de ZooKeeper três nós. O tamanho de quórum é fixa e não pode ser reconfigurado.
 
Serviços de Storm no cluster estão configurados para utilizar automaticamente o quórum de ZooKeeper.
 
### <a name="worker-nodes"></a>Nós de trabalho
Nós de trabalho do Storm executam os seguintes serviços:
* Supervisor
* Trabalho Java as máquinas virtuais (JVMs), para a execução de topologias
* Agente do Ambari
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Como localizar o Storm event hub spout os binários para o desenvolvimento
 
Para obter mais informações sobre como utilizar os ficheiros do Storm event hub spout. JAR com a topologia, consulte os seguintes recursos.
 
### <a name="java-based-topology"></a>Topologia baseada em Java
[Processar eventos provenientes dos Hubs de eventos do Azure com o Storm no HDInsight (Java)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-com base topologia (Mono nos clusters do HDInsight 3.4 + Linux Storm)
[Process events from Azure Event Hubs with Storm on HDInsight (C#)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology) (Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight [C#])
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Hub de eventos mais recente do Storm spout binários para clusters do HDInsight 3.5 + Linux Storm
Para saber como utilizar o mais recente Storm event hub spout que funciona com clusters do HDInsight 3.5 + Linux Storm, consulte o repositório mvn [ficheiro Leia-me](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Exemplos de código de origem
Consulte [exemplos](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) como de leitura e escrita do Hub de eventos do Azure com uma topologia de Apache Storm (escrita em Java) num cluster do Azure HDInsight.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Como localizar o ficheiros de configuração de Storm Log4J em clusters
 
Para identificar os ficheiros de configuração do Apache Log4J para serviços do Storm.
 
### <a name="on-head-nodes"></a>Em nós principais
A configuração de Nimbus Log4J é lida /usr/hdp/\<versão HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>Em nós de trabalho
A configuração do supervisor Log4J é lida /usr/hdp/\<versão HDP\>/storm/log4j2/cluster.xml.
 
O ficheiro de configuração de trabalho Log4J é lida /usr/hdp/\<versão HDP\>/storm/log4j2/worker.xml.
 
Exemplos: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

