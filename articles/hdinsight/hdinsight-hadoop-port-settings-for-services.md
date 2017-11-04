---
title: "Portas utilizadas pelos serviços do Hadoop no HDInsight - Azure | Microsoft Docs"
description: "Uma lista de portas utilizadas pelos serviços de Hadoop em execução no HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd14aed9-ec25-4bb3-a20c-e29562735a7d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/23/2017
ms.author: larryfr
ms.openlocfilehash: d474cce902dad1390d55ed7bad556d9b0610605f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="ports-used-by-hadoop-services-on-hdinsight"></a>Portas utilizadas pelos serviços do Hadoop no HDInsight

Este documento fornece uma lista das portas utilizadas pelos serviços de Hadoop em execução nos clusters do HDInsight baseado em Linux. Também fornece informações sobre as portas utilizadas para ligar ao cluster através de SSH.

## <a name="public-ports-vs-non-public-ports"></a>Portas públicas vs portas não público

Clusters do HDInsight baseado em Linux expõem apenas três portas publicamente na internet; 22, 23 e 443. Estas portas são utilizadas para aceder de forma segura o cluster através de SSH e serviços expostos através do protocolo HTTPS seguro.

Internamente, o HDInsight é implementado por várias máquinas virtuais do Azure (os nós do cluster) em execução numa rede Virtual do Azure. Dentro da rede virtual, pode aceder portas não expostas através da internet. Por exemplo, se ligar a um de nós principais através de SSH, do nó principal pode, em seguida, diretamente aceder a serviços em execução em nós de cluster.

> [!IMPORTANT]
> Se não especificar uma rede Virtual do Azure como uma opção de configuração para o HDInsight, é criada uma automaticamente. No entanto, não é possível associar outros computadores (como outras máquinas virtuais do Azure ou no seu computador de desenvolvimento do cliente) a esta rede virtual.


Para associar computadores adicionais para a rede virtual, tem de criar primeiro a rede virtual e, em seguida, especifique-o ao criar o cluster do HDInsight. Para obter mais informações, consulte [capacidades de expandir HDInsight utilizando uma rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Portas públicas

Todos os nós no cluster do HDInsight estão localizados numa rede Virtual do Azure e não podem ser acedidos diretamente da internet. Um gateway público fornece acesso à internet para as seguintes portas que são comuns a todos os tipos de cluster do HDInsight.

| Serviço | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| sshd |22 |SSH |Liga-se os clientes para sshd no headnode primário. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Liga-se os clientes para sshd no nó de extremidade. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Liga-se os clientes para sshd no headnode secundário. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |IU da web Ambari. Consulte [gerir HDInsight utilizando a IU da Web do Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API de REST do Ambari. Consulte [gerir HDInsight utilizando a API de REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |API de REST do HCatalog. Consulte [utilizar o Hive com o Curl](hadoop/apache-hadoop-use-pig-curl.md), [utilizar o Pig com o Curl](hadoop/apache-hadoop-use-pig-curl.md), [utilizar o MapReduce com Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Estabelece ligação ao ramo de registo através de ODBC. Consulte [ligar o Excel para o HDInsight com o controlador Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Estabelece ligação ao ramo de registo através de JDBC. Consulte [ligar ao ramo de registo no HDInsight com o controlador JDBC ramo de registo](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Seguem-se disponível para tipos de cluster específicos:

| Serviço | Porta | Protocolo | Tipo de cluster | Descrição |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |API de REST de HBase. Consulte [introdução ao hbase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |API de REST do Spark. Consulte [tarefas submeter Spark remotamente com o Livy](spark/apache-spark-livy-rest-interface.md) |
| Storm |443 |HTTPS |Storm |IU da web Storm. Consulte [implementar e gerir topologias Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Autenticação

Todos os serviços publicamente expostos na internet têm de ser autenticados:

| Porta | Credenciais |
| --- | --- |
| 22 ou 23 |As credenciais de utilizador do SSH especificadas durante a criação do cluster |
| 443 |O nome de início de sessão (predefinição: admin) e palavra-passe que foram definidas durante a criação do cluster |

## <a name="non-public-ports"></a>Portas não público

> [!NOTE]
> Alguns serviços só estão disponíveis em tipos de cluster específicos. Por exemplo, o HBase só está disponível em tipos de cluster de HBase.

> [!IMPORTANT]
> Alguns serviços executam apenas no um headnode de cada vez. Se tentar ligar ao serviço no headnode primário e receber um erro 404, tente novamente utilizando o headnode secundário.

### <a name="ambari"></a>Ambari

| Serviço | Nós | Porta | Caminho de URL | Protocolo | 
| --- | --- | --- | --- | --- |
| IU da web do Ambari | Nós de cabeçalho | 8080 | / | HTTP |
| API de REST do Ambari | Nós de cabeçalho | 8080 | / api/v1 | HTTP |

Exemplos:

* API de REST do Ambari:`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Portas do HDFS

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| IU da web do NameNode |Nós de cabeçalho |30070 |HTTPS |Web da IU para ver o Estado |
| Serviço de metadados NameNode |Nós de cabeçalho |8020 |IPC |Metadados do sistema de ficheiros |
| DataNode |Todos os nós de trabalho |30075 |HTTPS |IU da Web para ver o estado, os registos, etc. |
| DataNode |Todos os nós de trabalho |30010 |&nbsp; |Transferência de dados |
| DataNode |Todos os nós de trabalho |30020 |IPC |Operações de metadados |
| NameNode secundário |Nós de cabeçalho |50090 |HTTP |Ponto de verificação para metadados NameNode |

### <a name="yarn-ports"></a>Portas YARN

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| IU da web do Gestor de recursos |Nós de cabeçalho |8088 |HTTP |Web da IU para o Resource Manager |
| IU da web do Gestor de recursos |Nós de cabeçalho |8090 |HTTPS |Web da IU para o Resource Manager |
| Interface de administração do Gestor de recursos |Nós de cabeçalho |8141 |IPC |Para as submissões de aplicação (Hive, o servidor do Hive, Pig, etc.) |
| Gestor de recursos de programador |Nós de cabeçalho |8030 |HTTP |Interface administrativa |
| Interface de aplicação do Gestor de recursos |Nós de cabeçalho |8050 |HTTP |Endereço da interface do Gestor de aplicações |
| NodeManager |Todos os nós de trabalho |30050 |&nbsp; |O endereço do Gestor de contentor |
| IU da web do NodeManager |Todos os nós de trabalho |30060 |HTTP |Interface do Gestor de recursos |
| Linha cronológica endereço |Nós de cabeçalho |10200 |RPC |O serviço RPC de serviço de linha cronológica. |
| Linha cronológica IU da web do |Nós de cabeçalho |8181 |HTTP |A linha cronológica serviço IU da web do |

### <a name="hive-ports"></a>Portas de ramo de registo

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nós de cabeçalho |10001 |Thrift |Serviço para ligar ao ramo de registo (Thrift/JDBC) |
| Metastore do Hive |Nós de cabeçalho |9083 |Thrift |Serviço para estabelecer a ligação para os metadados do Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Portas de WebHCat

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor de WebHCat |Nós de cabeçalho |30111 |HTTP |API Web em cima HCatalog e outros serviços do Hadoop |

### <a name="mapreduce-ports"></a>Portas de MapReduce

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| JobHistory |Nós de cabeçalho |19888 |HTTP |IU da web do MapReduce JobHistory |
| JobHistory |Nós de cabeçalho |10020 |&nbsp; |Servidor de MapReduce JobHistory |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Mapeamento intermédio transferências produz para pedir Reducers |

### <a name="oozie"></a>Oozie

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor do Oozie |Nós de cabeçalho |11000 |HTTP |URL para o serviço de Oozie |
| Servidor do Oozie |Nós de cabeçalho |11001 |HTTP |Porta para Oozie admin |

### <a name="ambari-metrics"></a>Métricas de Ambari

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Linha cronológica (histórico de aplicação) |Nós de cabeçalho |6188 |HTTP |A linha cronológica serviço IU da web do |
| Linha cronológica (histórico de aplicação) |Nós de cabeçalho |30200 |RPC |A linha cronológica serviço IU da web do |

### <a name="hbase-ports"></a>Portas de HBase

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HMaster |Nós de cabeçalho |16000 |&nbsp; |&nbsp; |
| Informações de HMaster IU da Web |Nós de cabeçalho |16010 |HTTP |A porta para a IU da web de mestre de HBase |
| Servidor de região |Todos os nós de trabalho |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |A porta que os clientes utilizam para ligar ao ZooKeeper |

### <a name="kafka-ports"></a>Portas Kafka

| Serviço | Nós | Porta | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Mediador |Nós de trabalho |9092 |[Protocolo de transmissão Kafka](http://kafka.apache.org/protocol.html) |Utilizado para comunicação de cliente |
| &nbsp; |Nós de zookeeper |2181 |&nbsp; |A porta que os clientes utilizam para ligar ao Zookeeper |

### <a name="spark-ports"></a>Portas do Spark

| Serviço | Nós | Porta | Protocolo | Caminho de URL | Descrição |
| --- | --- | --- | --- | --- | --- |
| Servidores de Thrift de Spark |Nós de cabeçalho |10002 |Thrift | &nbsp; | Serviço para estabelecer a ligação para o Spark SQL (Thrift/JDBC) |
| Servidor Livy | Nós de cabeçalho | 8998 | HTTP | /Batches | Serviço para executar instruções, tarefas e aplicações |

Exemplos:

* O Livy: `curl "http://10.0.0.11:8998/batches"`. Neste exemplo, `10.0.0.11` é o endereço IP do headnode que aloja o serviço de Livy.