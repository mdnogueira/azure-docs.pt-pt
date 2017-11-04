---
title: Kernels para bloco de notas do Jupyter nos clusters do Spark no Azure HDInsight | Microsoft Docs
description: "Saiba mais sobre os kernels PySpark, PySpark3 e Spark para o bloco de notas do Jupyter disponível com clusters do Spark no Azure HDInsight."
keywords: Bloco de notas do jupyter no spark, spark do jupyter
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0719e503-ee6d-41ac-b37e-3d77db8b121b
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: maxluk
ms.openlocfilehash: 74c761caf1a4d51f199ba0315535ccac63eaf37a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="kernels-for-jupyter-notebook-on-spark-clusters-in-azure-hdinsight"></a>Kernels para bloco de notas do Jupyter nos clusters do Spark no Azure HDInsight 

Clusters do HDInsight Spark fornecem kernels que pode utilizar com o bloco de notas do Jupyter no Spark para testar as suas aplicações. Um kernel é um programa que executa e interpreta o seu código. Os três kernels são:

- **PySpark** – para aplicações escritas no Python2
- **PySpark3** – para aplicações escritas no Python3
- **O Spark** – para aplicações escritas no Scala

Neste artigo, irá aprender a utilizar estas kernels e as vantagens de utilizá-los.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Criar um bloco de notas do Jupyter no Spark HDInsight

1. Do [portal do Azure](https://portal.azure.com/), abra o seu cluster.  Consulte [clusters lista e mostrar](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters) para as instruções. O cluster está aberto num novo painel do portal.

2. Do **ligações rápidas** secção, clique em **Cluster dashboards** para abrir o **Cluster dashboards** painel.  Se não vir **ligações rápidas**, clique em **descrição geral** no menu no painel esquerdo.

    ![Bloco de notas do Jupyter no Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "bloco de notas do Jupyter no Spark") 

3. Clique em **bloco de notas do Jupyter**. Se lhe for solicitado, introduza as credenciais de administrador do cluster.
   
   > [!NOTE]
   > Também pode contactar o bloco de notas do Jupyter no cluster do Spark abrindo o seguinte URL no browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

3. Clique em **novo**e, em seguida, clique em **Pyspark**, **PySpark3**, ou **Spark** para criar um bloco de notas. Utilize o kernel do Spark para aplicações de Scala, kernel do PySpark para aplicações de Python2 e PySpark3 kernel para aplicações de Python3.
   
    ![Kernels para bloco de notas do Jupyter no Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Kernels para bloco de notas do Jupyter no Spark") 

4. Um bloco de notas abre-se com o kernel que selecionou.

## <a name="benefits-of-using-the-kernels"></a>Vantagens de utilizar os kernels

Seguem-se algumas vantagens de utilizar os novo kernels com o bloco de notas do Jupyter nos clusters do Spark HDInsight.

- **Configuração predefinida contextos**. Com **PySpark**, **PySpark3**, ou o **Spark** kernels, não tem de definir os contextos do Spark ou Hive explicitamente antes de começar a trabalhar com as suas aplicações. Estes estiverem disponíveis por predefinição. Estes contextos são:
   
   * **sc** – para o contexto do Spark
   * **sqlContext** – para o contexto de ramo de registo
   
   Por isso, não terá de executar as instruções de como o seguinte para definir os contextos:
   
          sc = SparkContext('yarn-client')
          sqlContext = HiveContext(sc)
   
   Em vez disso, pode utilizar os contextos predefinidos diretamente na sua aplicação.

- **Magia de células**. O kernel do PySpark fornece algumas predefinidas "magia", que são comandos especiais que pode chamar com `%%` (por exemplo, `%%MAGIC` <args>). O comando mágica tem de ser a primeira palavra numa célula de código e permitir várias linhas de conteúdo. A palavra mágica deve ser a primeira palavra na célula. Adicionar nada antes da magia, mesmo comentários, causa um erro.     Para obter mais informações sobre a magia, consulte [aqui](http://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    A tabela seguinte lista a magia diferentes disponível através de kernels.

   | Magic | Exemplo | Descrição |
   | --- | --- | --- |
   | Ajuda |`%%help` |Gera uma tabela de todos os a magia disponível com o exemplo e descrição |
   | informações |`%%info` |Informações da sessão saídas para o ponto de final Livy atual |
   | Configurar |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Configura os parâmetros para a criação de uma sessão. O sinalizador force (-f) é obrigatório se uma sessão já foi criada, que garante que a sessão é removida e recriada. Observe [POST /sessions de Livy corpo do pedido](https://github.com/cloudera/livy#request-body) para obter uma lista de parâmetros válidos. Os parâmetros devem ser transmitidos como uma cadeia JSON e tem de estar na linha seguinte após a magia, conforme apresentado na coluna de exemplo. |
   | SQL Server |`%%sql -o <variable name>`<br> `SHOW TABLES` |Executa uma consulta do Hive contra o sqlContext. Se o `-o` parâmetro é transmitido, o resultado da consulta é continuado no % % contexto de Python local como uma [Pandas](http://pandas.pydata.org/) dataframe. |
   | local |`%%local`<br>`a=1` |Todos os o código em linhas subsequentes é executado localmente. Código tem de ser válido Python2 código mesmo, independentemente do kernel que está a utilizar. Sim, mesmo se tiver selecionado **PySpark3** ou **Spark** kernels ao criar o bloco de notas, se utilizar o `%%local` mágica numa célula, nessa célula só podem ter código Python2 válido... |
   | registos |`%%logs` |Os registos para a sessão atual do Livy saídas. |
   | eliminar |`%%delete -f -s <session number>` |Elimina uma específica de sessão do ponto final Livy atual. Tenha em atenção que não é possível eliminar a sessão iniciada para o kernel do próprio. |
   | Limpeza |`%%cleanup -f` |Elimina todas as sessões atuais ponto final Livy, incluindo sessão este bloco de notas. O sinalizador -f force é obrigatório. |

   > [!NOTE]
   > Além a magia adicionada pelo kernel do PySpark, também pode utilizar o [incorporada IPython magia](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), incluindo `%%sh`. Pode utilizar o `%%sh` magic para executar scripts e bloco de código no headnode cluster.
   >
   >
2. **Auto visualização**. O **Pyspark** kernel automaticamente visualiza o resultado das consultas do Hive e do SQL Server. Pode escolher entre vários tipos diferentes de visualizações incluindo tabela circular, linha, área, a barra.

## <a name="parameters-supported-with-the-sql-magic"></a>Parâmetros suportados com o % % magic de sql
O `%%sql` magic suporta diferentes parâmetros que pode utilizar para controlar o tipo de saída que recebe quando executar consultas. A tabela seguinte lista o resultado.

| Parâmetro | Exemplo | Descrição |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Utilize este parâmetro para manter o resultado da consulta, no % % contexto de Python local, como um [Pandas](http://pandas.pydata.org/) dataframe. O nome da variável dataframe é o nome da variável que especificar. |
| -q |`-q` |Utilize esta opção para desativar as visualizações para a célula. Se não quiser visualizar automaticamente o conteúdo de uma célula e apenas pretende capturá-la como uma dataframe, então, utilizar `-q -o <VARIABLE>`. Se pretender desativar visualizações sem capturar os resultados (por exemplo, para executar uma consulta SQL, como um `CREATE TABLE` instrução), utilize `-q` sem especificar um `-o` argumento. |
| -m |`-m <METHOD>` |Onde **método** está **demorar** ou **exemplo** (predefinição é **demorar**). Se o método for **demorar**, kernel escolhe elementos da parte superior do conjunto de dados de resultado especificado pelo MAXROWS (descrito mais à frente nesta tabela). Se o método for **exemplo**, kernel aleatoriamente amostras de elementos de conjunto de dados de acordo com `-r` parâmetro, descrito a seguir nesta tabela. |
| -r |`-r <FRACTION>` |Aqui **FRAÇÃO** é um número de vírgula flutuante entre 0,0 e 1,0. Se o método de exemplo para a consulta SQL é `sample`, em seguida, o kernel amostras aleatoriamente a fração especificada de elementos do conjunto de resultados para a. Por exemplo, se executar uma consulta SQL com os argumentos `-m sample -r 0.01`, em seguida, 1 de % das linhas resultado aleatoriamente servem como amostra. |
| -n |`-n <MAXROWS>` |**MAXROWS** é um valor de número inteiro. O kernel limita o número de linhas de saída a **MAXROWS**. Se **MAXROWS** é como um número negativo **-1**, em seguida, o número de linhas no conjunto de resultados não é limitado. |

**Exemplo:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

A instrução acima faz o seguinte:

* Seleciona todos os registos da **hivesampletable**.
* Uma vez que utilizamos - q, desligar automaticamente visualização.
* Uma vez que utilizamos `-m sample -r 0.1 -n 500` amostras de 10% as linhas a hivesampletable aleatoriamente e limita o tamanho do resultado definido como 500 linhas.
* Por fim, porque é utilizado `-o query2` também economizam a saída para um dataframe chamado **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Considerações ao utilizar os novo kernels

Qualquer kernel utilizar, deixar os blocos de notas com consome recursos de cluster.  Com estas kernels, porque os contextos são a configuração predefinidos, simplesmente sair os blocos de notas não eliminar o contexto e, por conseguinte, os recursos de cluster continuem a estar em utilização. É uma boa prática para utilizar o **fechar e parar** opção do bloco de notas **ficheiro** menu quando tiver terminado de utilizar o bloco de notas, que inutilizam o contexto e, em seguida, sai o bloco de notas.     

## <a name="show-me-some-examples"></a>Mostrar alguns exemplos

Quando abre um bloco de notas do Jupyter, verá duas pastas disponíveis ao nível da raiz.

* O **PySpark** pasta tem notas de exemplo que utilizam a nova **Python** kernel.
* O **Scala** pasta tem notas de exemplo que utilizam a nova **Spark** kernel.

Pode abrir o **00 - [leia-ME primeiro] funcionalidades de Kernel do Spark Magic** bloco de notas do **PySpark** ou **Spark** pasta para saber mais sobre a magia diferentes disponíveis. Também pode utilizar os outros exemplo blocos de notas disponíveis em duas pastas para aprender a alcançar diferentes cenários de utilização de blocos de notas do Jupyter com clusters do HDInsight Spark.

## <a name="where-are-the-notebooks-stored"></a>Onde estão armazenados os blocos de notas?

Blocos de notas do Jupyter são guardados para a conta de armazenamento associada ao cluster no **/HdiNotebooks** pasta.  Blocos de notas, ficheiros de texto e pastas que criar a partir de dentro do Jupyter estejam acessíveis a partir da conta de armazenamento.  Por exemplo, se utilizar o Jupyter para criar uma pasta **myfolder** e um bloco de notas **myfolder/mynotebook.ipynb**, pode aceder nesse bloco de notas em `/HdiNotebooks/myfolder/mynotebook.ipynb` dentro da conta de armazenamento.  A reversão também se aplica, ou seja, se carregar um bloco de notas diretamente à sua conta de armazenamento em `/HdiNotebooks/mynotebook1.ipynb`, o bloco de notas é também visível a partir do Jupyter.  Blocos de notas permanecem na conta de armazenamento, mesmo depois do cluster é eliminado.

A forma como os blocos de notas são guardados para a conta de armazenamento é compatível com HDFS. Deste modo, se lhe SSH para o cluster que pode utilizar comandos de gestão de ficheiros conforme mostrado no seguinte fragmento:

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


No caso de existirem problemas de acesso à conta de armazenamento para o cluster, os blocos de notas também são guardados no headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Browser suportado

Blocos de notas do Jupyter nos clusters do Spark HDInsight são suportados apenas no Google Chrome.

## <a name="feedback"></a>Comentários
Os novo kernels estão em fase de evolução e serão madura ao longo do tempo. Isto pode significar que APIs alterar como estes kernels madura. Agradecemos quaisquer comentários que tenham ao utilizar estas kernels de novo. Isto é útil em formação de versão final destes kernels. Pode deixar os seus comentários comentários sob o **comentários** secção na parte inferior deste artigo.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
