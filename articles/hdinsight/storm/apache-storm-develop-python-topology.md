---
title: Apache Storm com o Python comopnents - Azure HDInsight | Microsoft Docs
description: Saiba como criar uma topologia do Apache Storm que utiliza os componentes de Python.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
keywords: python do Apache storm
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2017
ms.author: larryfr
ms.openlocfilehash: be2db4e413426be3a5e2dece43d74270e3636909
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topologias do Apache Storm no HDInsight com o Python

Saiba como criar uma topologia do Apache Storm que utiliza os componentes de Python. Apache Storm suporta vários idiomas, mesmo que permite combinar os componentes de vários idiomas numa topologia de um. A estrutura de Flux (introduzida com o Storm 0.10.0) permite-lhe criar facilmente soluções que utilizam componentes de Python.

> [!IMPORTANT]
> As informações neste documento foi testadas com o Storm no HDInsight 3.6. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

O código para este projeto está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Pré-requisitos

* Python 2.7 ou superior

* 1.8 do Java JDK ou superior

* Maven 3

* (Opcional) Um ambiente de desenvolvimento local do Storm. Um ambiente de Storm local só é necessário se pretender executar localmente a topologia. Para obter mais informações, consulte [configurar um ambiente de desenvolvimento](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Suporte de vários idiomas de Storm

Apache Storm foi concebido para funcionar com componentes escritos utilizando qualquer linguagem de programação. Os componentes têm de compreender como trabalhar com o [definição de Thrift para Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para o Python, um módulo é fornecido como parte do projeto Apache Storm que lhe permite facilmente interface Storm. Pode encontrar este módulo [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm é um processo de Java que é executado na máquina Virtual de Java (JVM). Componentes escritos em outros idiomas são executados como subprocessos designados. O Storm comunica com estes subprocessos designados utilizando mensagens JSON enviadas stdin/stdout. Podem encontrar mais detalhes sobre a comunicação entre os componentes no [várias lang protocolo](https://storm.apache.org/documentation/Multilang-protocol.html) documentação.

## <a name="python-with-the-flux-framework"></a>Python com o framework Flux

A estrutura do Flux permite-lhe definir topologias Storm em separado dos componentes. A estrutura do Flux utiliza YAML para definir a topologia do Storm. O texto seguinte é um exemplo de como fazer referência um componente de Python no documento YAML:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

A classe `FluxShellSpout` é utilizada para iniciar o `sentencespout.py` script que implementa o spout.

Flux espera os scripts Python de ser o `/resources` diretório dentro do ficheiro jar que contém a topologia. Para este exemplo armazena os scripts Python no `/multilang/resources` diretório. O `pom.xml` inclui este ficheiro utilizando o seguinte XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Conforme mencionado anteriormente, há um `storm.py` ficheiro que implementa a definição de Thrift para Storm. A estrutura do Flux inclui `storm.py` automaticamente quando o projeto foi criado, pelo que não precisa de preocupá-lo.

## <a name="build-the-project"></a>Compilar o projeto

A partir da raiz do projeto, utilize o seguinte comando:

```bash
mvn clean compile package
```

Este comando cria um `target/WordCount-1.0-SNAPSHOT.jar` ficheiro que contém a topologia compilada.

## <a name="run-the-topology-locally"></a>Executar localmente a topologia

Para executar localmente a topologia, utilize o seguinte comando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> Este comando necessita de um ambiente de desenvolvimento local do Storm. Para obter mais informações, consulte [configurar um ambiente de desenvolvimento](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html)

Uma vez iniciado topologia,-emite informações para a consola local semelhante para o seguinte texto:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Para parar a topologia, utilize __Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Execute a topologia do Storm no HDInsight

1. Utilize o seguinte comando para copiar o `WordCount-1.0-SNAPSHOT.jar` ficheiro para Storm no cluster do HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` com o utilizador SSH para o cluster. Substitua `mycluster` com o nome do cluster. Poderá ser-lhe solicitado para introduzir a palavra-passe para o utilizador SSH.

    Para obter mais informações sobre como utilizar o SSH e o SCP, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Depois do ficheiro foi carregado, ligar-se ao cluster através de SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. A partir de sessão SSH, utilize o seguinte comando para iniciar a topologia no cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Pode utilizar a IU do Storm para visualizar a topologia no cluster. A IU do Storm está localizada em https://mycluster.azurehdinsight.net/stormui. Substitua `mycluster` com o nome do cluster.

> [!NOTE]
> Depois de iniciada, uma topologia do Storm executa até parado. Para parar a topologia, utilize um dos seguintes métodos:
>
> * O `storm kill TOPOLOGYNAME` comando na linha de comandos
> * O **Kill** botão na IU do Storm.


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes documentos para outras formas de utilizar o Python com o HDInsight:

* [Como utilizar o Python para as tarefas do MapReduce de transmissão em fluxo](../hadoop/apache-hadoop-streaming-python.md)
* [Como utilizar o Python utilizador definidas funções (UDF) na Pig e do Hive](../hadoop/python-udf-hdinsight.md)
