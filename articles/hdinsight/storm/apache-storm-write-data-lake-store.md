---
title: Escrita do Apache Storm para armazenamento/Data Lake Store, Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar o Apache Storm para escrever no armazenamento compatível com HDFS para o HDInsight. Armazenamento do Azure ou de Azure Data Lake Store fornecer o armazenamento de HDFS comptabile para o HDInsight. Este documento e o exemplo associado, demonstram como o componente de HdfsBolt pode ser utilizado para escrever o armazenamento de predefinido de um Storm num cluster do HDInsight."
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: larryfr
ms.openlocfilehash: efb0a19e0793a93b2bfab93adb747e6f130341df
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Escrever HDFS do Apache Storm no HDInsight

Saiba como utilizar o Storm para escrever dados no armazenamento compatível com HDFS utilizado pelo Apache Storm no HDInsight. HDInsight pode utilizar ambas Storage do Azure e do Azure Data Lake armazenam como armazenamento HDFS comptabile. Storm fornece um [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) componente que escreve dados para o HDFS. Este documento fornece informações sobre como escrever para o tipo de armazenamento do HdfsBolt. 

> [!IMPORTANT]
> A topologia de exemplo utilizada neste documento baseia-se nos componentes que estão incluídos com o Storm no HDInsight. Pode necessitar de modificações para trabalhar com o Azure Data Lake Store quando utilizado com outros clusters do Apache Storm.

## <a name="get-the-code"></a>Obter o código

O projeto que contém esta topologia está disponível como uma transferência a partir do [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Para compilar a este projeto, terá da seguinte configuração para o seu ambiente de desenvolvimento:

* [1.8 do Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior. HDInsight 3.5 ou superior necessário Java 8.

* [Maven 3](https://maven.apache.org/download.cgi)

As seguintes variáveis de ambiente podem ser definidas quando instalar o Java e o JDK na sua estação de trabalho de desenvolvimento. No entanto, deve verificar que existe e que contêm os valores corretos para o seu sistema.

* `JAVA_HOME`-devem apontar para o diretório onde está instalado o JDK.
* `PATH`-deve conter os seguintes caminhos:
  
    * `JAVA_HOME`(ou o caminho equivalente).
    * `JAVA_HOME\bin`(ou o caminho equivalente).
    * O diretório onde está instalado o Maven.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Como utilizar o HdfsBolt com o HDInsight

> [!IMPORTANT]
> Antes de utilizar o HdfsBolt Storm no HDInsight, primeiro tem de utilizar uma ação de script para copiar ficheiros jar necessária para o `extpath` para Storm. Para obter mais informações, consulte o [configurar o cluster](#configure) secção.

O HdfsBolt utiliza o esquema do ficheiro indicadas por si para compreender como escrever HDFS. Com o HDInsight, utilize um dos esquemas seguintes:

* `wasb://`: Utilizada com uma conta de armazenamento do Azure.
* `adl://`: Utilizada com o Azure Data Lake Store.

A tabela seguinte fornece exemplos de como utilizar o esquema do ficheiro para diferentes cenários:

| Esquema | Notas |
| ----- | ----- |
| `wasb:///` | A conta do storage predefinida é um contentor de BLOBs numa conta do Storage do Azure |
| `adl:///` | A conta do storage predefinida é um diretório no Azure Data Lake Store. Durante a criação do cluster, especifique o diretório no Data Lake Store que seja a raiz do HDFS do cluster. Por exemplo, o `/clusters/myclustername/` diretório. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Uma conta do storage do Azure (adicionais) de não predefinidas associada com o cluster. |
| `adl://STORENAME/` | A raiz do Data Lake Store utilizados pelo cluster. Este esquema permite-lhe aceder aos dados que estão localizados fora do diretório que contém o sistema de ficheiros do cluster. |

Para obter mais informações, consulte o [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) referência em Apache.org.

### <a name="example-configuration"></a>Configuração de exemplo

O YAML seguinte é um excerpt do `resources/writetohdfs.yaml` incluído no exemplo de ficheiro. Este ficheiro define a topologia do Storm utilizando o [Flux](https://storm.apache.org/releases/1.1.0/flux.html) framework para o Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Este YAML define os seguintes itens:

* `syncPolicy`: Define quando os ficheiros estão synched/descarregados corresponde ao sistema de ficheiros. Neste exemplo, todas as cadeias de identificação de 1000.
* `fileNameFormat`: Define o padrão de nome de ficheiro e caminho a utilizar quando escrever ficheiros. Neste exemplo, o caminho é fornecido no tempo de execução utilizando um filtro e a extensão de ficheiro é `.txt`.
* `recordFormat`: Define o formato interno dos ficheiros de escrita. Neste exemplo, os campos são delimitados pelo `|` caráter.
* `rotationPolicy`: Define quando deve rodar os ficheiros. Neste exemplo, é efetuada sem rotação.
* `hdfs-bolt`: Utiliza os componentes anteriores como parâmetros de configuração para o `HdfsBolt` classe.

Para obter mais informações sobre a arquitetura de Flux, consulte [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>Configurar o cluster

Por predefinição, o Storm no HDInsight não inclui os componentes que HdfsBolt utiliza para comunicar com o Storage do Azure ou de Data Lake Store no classpath do Storm. Utilize a ação de script seguinte para adicionar estes componentes para o `extlib` diretório para Storm no cluster:

* URI de script:`https://000aarperiscus.blob.core.windows.net/certs/stormextlib.sh`
* Nós a aplicar aos: Nimbus, Supervisor
* Os parâmetros: nenhuma

Para obter informações sobre como utilizar este script com o cluster, consulte o [HDInsight personalizar clusters com ações de script](./../hdinsight-hadoop-customize-cluster-linux.md) documento.

## <a name="build-and-package-the-topology"></a>Criar e a topologia do pacote

1. Transferir o projeto de exemplo da [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) ao seu ambiente de desenvolvimento.

2. A partir de uma linha de comandos, terminal ou a shell de sessão, diretórios de alteração para a raiz do projeto transferido. Para criar e a topologia do pacote, utilize o seguinte comando:
   
        mvn compile package
   
    Uma vez concluída a compilação e empacotamento, há um novo diretório designado `target`, que contém um ficheiro denominado `StormToHdfs-1.0-SNAPSHOT.jar`. Este ficheiro contém a topologia compilada.

## <a name="deploy-and-run-the-topology"></a>Implementar e executar a topologia

1. Utilize o seguinte comando para copiar a topologia para o cluster do HDInsight. Substitua **utilizador** com o nome de utilizador SSH que utilizou quando criar o cluster. Substitua **CLUSTERNAME** pelo nome do cluster.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs1.0-SNAPSHOT.jar
   
    Quando lhe for pedido, introduza a palavra-passe utilizada quando o utilizador do SSH para o cluster. Se tiver utilizado uma chave pública em vez de uma palavra-passe, poderá ter de utilizar o `-i` parâmetro para especificar o caminho para a chave privada correspondente.
   
   > [!NOTE]
   > Para obter mais informações sobre como utilizar `scp` com o HDInsight, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Após a conclusão do carregamento, utilize o seguinte para ligar ao cluster do HDInsight através de SSH. Substitua **utilizador** com o nome de utilizador SSH que utilizou quando criar o cluster. Substitua **CLUSTERNAME** pelo nome do cluster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Quando lhe for pedido, introduza a palavra-passe utilizada quando o utilizador do SSH para o cluster. Se tiver utilizado uma chave pública em vez de uma palavra-passe, poderá ter de utilizar o `-i` parâmetro para especificar o caminho para a chave privada correspondente.
   
   Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Assim que estiver ligado, utilize o seguinte comando para criar um ficheiro denominado `dev.properties`:

        nano dev.properties

4. Utilize o seguinte texto como o conteúdo a `dev.properties` ficheiro:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > Este exemplo assume que o cluster utiliza uma conta de armazenamento do Azure como armazenamento de predefinido. Se o cluster utiliza o Azure Data Lake Store, utilize `hdfs.url: adl:///` em vez disso.
    
    Para guardar o ficheiro, utilize __Ctrl + X__, em seguida, __Y__e, finalmente, __Enter__. Os valores existentes neste ficheiro de definir o URL do arquivo Data Lake e o nome do diretório que dados são escritos para.

3. Utilize o seguinte comando para iniciar a topologia:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Este comando inicia a topologia utilizando a estrutura do Flux submetendo-lo para o nó Nimbus do cluster. A topologia é definida pelo `writetohdfs.yaml` ficheiros incluídos no jar. O `dev.properties` ficheiro é transmitido como um filtro e os valores contidos no ficheiro são lidas pela topologia.

## <a name="view-output-data"></a>Ver dados de saída

Para ver os dados, utilize o seguinte comando:

    hdfs dfs -ls /stormdata/

É apresentada uma lista dos ficheiros criados por esta topologia.

A lista seguinte é um exemplo dos dados retuned por comandos anteriores:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Parar a topologia

Topologias do Storm executam até parado ou o cluster é eliminado. Para parar a topologia, utilize o seguinte comando:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Elimina o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como utilizar o Storm para escrever Storage do Azure e o Azure Data Lake Store, detetar outros [Storm exemplos para o HDInsight](apache-storm-example-topology.md).

