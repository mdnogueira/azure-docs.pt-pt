---
title: Instalar e utilizar Giraph no HDInsight (Hadoop) - Azure | Microsoft Docs
description: "Saiba como instalar Giraph nos clusters do HDInsight baseado em Linux utilizando as ações de Script. Ações de script permitem-lhe personalizar o cluster durante a criação, alteração da configuração de cluster ou instalar os serviços e utilitários."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9fcac906-8f06-4002-9fe8-473e42f8fd0f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: larryfr
ms.openlocfilehash: 0f26c35512bb92323b5a9c1688f96a958e0749c6
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Instalar Giraph em clusters do HDInsight Hadoop e utilizar Giraph para processar gráficos em grande escala

Saiba como instalar o Apache Giraph num cluster do HDInsight. A funcionalidade de ação de script do HDInsight permite-lhe personalizar o seu cluster executando um script de bash. Scripts podem ser utilizados para personalizar clusters durante e após a criação do cluster.

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="whatis"></a>O que é Giraph

[Apache Giraph](http://giraph.apache.org/) permite-lhe efetuar o processamento através da utilização do Hadoop e pode ser utilizado com o Azure HDInsight. Relações entre objetos de modelo de gráficos. Por exemplo, as ligações entre routers numa rede grande, como a Internet ou relações entre as pessoas em redes sociais. Processamento de gráfico permite-lhe razão sobre as relações entre objetos de um gráfico, tais como:

* Identificar potenciais amigos com base na sua relações atuais.

* Identificar a rota mais curta entre dois computadores numa rede.

* Calcular a classificação de página de páginas Web.

> [!WARNING]
> Componentes fornecidos com o cluster do HDInsight são totalmente suportados - Support da Microsoft ajuda a isolar e resolver problemas relacionados com estes componentes.
>
> Componentes personalizados, tais como Giraph, recebem suporte comercialmente razoável para ajudar a resolver o problema. Support da Microsoft podem ser capaz de resolver o problema. Caso contrário, tem de consultar Comunidades de open source para onde se encontra profundo conhecimentos para que a tecnologia. Por exemplo, existem vários sites de Comunidade que podem ser utilizadas, como: [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos do Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).


## <a name="what-the-script-does"></a>O que faz o script

Este script executa as seguintes ações:

* Instala Giraph para`/usr/hdp/current/giraph`

* Copia o `giraph-examples.jar` ficheiro para o armazenamento de predefinido (WASB) para o cluster:`/example/jars/giraph-examples.jar`

## <a name="install"></a>Instalar Giraph utilizando ações de Script

Um script de exemplo para instalar Giraph num cluster do HDInsight está disponível na seguinte localização:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Esta secção fornece instruções sobre como utilizar o script de exemplo ao criar o cluster utilizando o portal do Azure.

> [!NOTE]
> Uma ação de script pode ser aplicada utilizando qualquer um dos seguintes métodos:
> * Azure PowerShell
> * A CLI do Azure
> * O SDK .NET do HDInsight
> * Modelos do Azure Resource Manager
> 
> Também pode aplicar ações de script para clusters já em execução. Para obter mais informações, consulte [HDInsight personalizar clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniciar criação de um cluster, utilizando os passos no [clusters do HDInsight baseado em Linux criar](hdinsight-hadoop-create-linux-clusters-portal.md), mas não concluir a criação.

2. No **configuração opcional** secção, selecione **ações de Script**e forneça as seguintes informações:

   * **NOME**: introduza um nome amigável para a ação de script.

   * **URI de SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: esta entrada de verificação

   * **TRABALHO**: deixe esta entrada desmarcada

   * **ZOOKEEPER**: deixe esta entrada desmarcada

   * **Os parâmetros**: deixe este campo em branco

3. Na parte inferior do **ações de Script**, utilize o **selecione** botão para guardar a configuração. Por último, utilize o **selecione** na parte inferior do **configuração opcional** secção para guardar as informações de configuração opcional.

4. Continuar a criar o cluster, conforme descrito em [clusters do HDInsight baseado em Linux criar](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Como utilizar o Giraph no HDInsight?

Depois de criar o cluster, utilize os seguintes passos para executar o exemplo de SimpleShortestPathsComputation incluído com Giraph. Este exemplo utiliza o basic [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) implementação para localizar o caminho mais curto entre objetos de um gráfico.

1. Ligar ao cluster do HDInsight com o SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter informações, veja [Use SSH with HDInsight (Utilizar SSH com o HDInsight)](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilize o seguinte comando para criar um ficheiro denominado **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Utilize o seguinte texto como o conteúdo deste ficheiro:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Estes dados descreve uma relação entre objetos de um gráfico direcionado, utilizando o formato `[source_id, source_value,[[dest_id], [edge_value],...]]`. Cada linha representa uma relação entre um `source_id` objeto e um ou mais `dest_id` objetos. O `edge_value` pode considerar como a força ou distância da ligação entre `source_id` e `dest\_id`.

    Desenhado horizontalmente, e utilizar o valor (ou ponderação) como a distância entre objetos, os dados aspeto que poderão ter o diagrama a seguir:

    ![tiny_graph.txt desenhado como circles com linhas de variando distância entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Para guardar o ficheiro, utilize **Ctrl + X**, em seguida, **Y**e, finalmente, **Enter** para aceitar o nome de ficheiro.

4. Utilize o seguinte para armazenar os dados para o armazenamento primário para o cluster do HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Execute o exemplo de SimpleShortestPathsComputation utilizando o seguinte comando:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Os parâmetros utilizados com este comando são descritos na tabela seguinte:

   | Parâmetro | O que faz |
   | --- | --- |
   | `jar` |O ficheiro jar que contém os exemplos. |
   | `org.apache.giraph.GiraphRunner` |A classe utilizada para iniciar os exemplos. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |O exemplo que está a ser utilizado. Neste exemplo, calcula o caminho mais curto entre 1 de ID e todos os outros IDs no gráfico. |
   | `-ca mapred.job.tracker` |Headnode para o cluster. |
   | `-vif` |O formato de entrada a utilizar para os dados de entrada. |
   | `-vip` |O ficheiro de dados de entrada. |
   | `-vof` |O formato de saída. Neste exemplo, a ID e o valor como texto simples. |
   | `-op` |A localização de saída. |
   | `-w 2` |O número de funcionários a utilizar. Neste exemplo, 2. |

    Para obter mais informações sobre estes e outros parâmetros utilizados Giraph exemplos, consulte o [início rápido de Giraph](http://giraph.apache.org/quick_start.html).

6. Depois de concluída a tarefa, os resultados são armazenados no **/example/out/shotestpaths** diretório. Os nomes de ficheiro de saída de começar por **parte-m -** e terminar com um número a indicar o primeiro, o segundo, o ficheiro etc. Utilize o seguinte comando para ver o resultado:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    O resultado deverá ser semelhante para o seguinte texto:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation exemplo é difícil coded começar com 1 de ID de objeto e localizar o caminho mais curto para outros objetos. O resultado é no formato de `destination_id` e `distance`. O `distance` é o valor (ou ponderação) das margens entre 1 de ID de objeto e o ID de destino.

    Visualizar estes dados, pode verificar os resultados por estiverem em deslocação os caminhos mais curto entre 1 de ID e todos os outros objetos. O caminho mais curto entre ID 1 e ID 4 é 5. Este valor é a total distância entre <span style="color:orange">ID 1 e 3</span>e, em seguida, <span style="color:red">ID 3 e 4</span>.

    ![Desenho de objetos como circles com mais curto caminhos desenhados entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Passos seguintes

* [Instalar e utilizar Hue nos clusters do HDInsight](hdinsight-hadoop-hue-linux.md).

* [Instalar Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install-linux.md).
