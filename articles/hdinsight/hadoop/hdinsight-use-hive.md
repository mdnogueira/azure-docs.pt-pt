---
title: "O que é Apache Hive e o HiveQL - Azure HDInsight | Microsoft Docs"
description: "Apache Hive é um sistema de armazém de dados para o Hadoop. Pode consultar os dados armazenados no ramo de registo utilizando o HiveQL, que semelhante para Transact-SQL. Neste documento, saiba como utilizar o Hive e o HiveQL com o Azure HDInsight."
keywords: "hiveql, o que é o hive, hiveql do hadoop, como utilizar o hive, saiba ramo de registo, o que é o ramo de registo"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 2bcaa53ea0a97186b9617ae2ca757e3c806b325e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>O que é Apache Hive e o HiveQL no Azure HDInsight?

[Apache Hive](http://hive.apache.org/) é um sistema de armazém de dados para o Hadoop. Ramo de registo permite sumarização de dados, consulta e análise de dados. Consultas do Hive são escritas no HiveQL, que é uma linguagem de consulta semelhante ao SQL Server.

Ramo de registo permite-lhe a estrutura de projeto dados em grande parte não estruturados. Depois de definir a estrutura, pode utilizar o HiveQL para consultar os dados sem o conhecimento de Java ou MapReduce.

O HDInsight fornece vários tipos de cluster, que estão otimizados para cargas de trabalho específicas. Os seguintes tipos de cluster são frequentemente utilizados para consultas do Hive:

* __Consulta interativa__: cluster de A Hadoop que fornece [baixa latência analíticos processamento (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funcionalidade para melhorar os tempos de resposta para consultas interativas. Para obter mais informações, consulte o [começar com uma consulta interativo no HDInsight](../interactive-query/apache-interactive-query-get-started.md) documento.

* __Hadoop__: cluster de A Hadoop que está otimizado para cargas de trabalho de processamento em lote. Para obter mais informações, consulte o [iniciar com o Hadoop no HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) documento.

* __O Spark__: Apache Spark tem uma funcionalidade incorporada para trabalhar com o Hive. Para obter mais informações, consulte o [começar a utilizar o Spark no HDInsight](../spark/apache-spark-jupyter-spark-sql.md) documento.

* __HBase__: HiveQL pode ser utilizadas para consultar dados armazenados no HBase. Para obter mais informações, consulte o [começar a utilizar o HBase no HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) documento.

## <a name="how-to-use-hive"></a>Como utilizar o Hive

Utilize a tabela seguinte para saber como utilizar o Hive com o HDInsight:

| **Utilize este método** se pretender que... | … .an **interativa** shell | ... **batch** processamento | … .with isto **sistema de operativo de cluster** | … .from isto **sistema operativo do cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Vista do Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Qualquer (baseadas no browser) |
| [Cliente beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux ou Windows * |Linux, Unix, Mac OS X ou Windows |
| [Ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux ou Windows * |Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux ou Windows * |Windows |

> [!IMPORTANT]
> \*Linux é o único sistema operativo utilizado no HDInsight versão 3.4 ou superior. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).
>
> Se estiver a utilizar um cluster do HDInsight baseados em Windows, pode utilizar o [consola consulta](../hadoop/apache-hadoop-use-hive-query-console.md) partir do seu browser ou [ambiente de trabalho remoto](../hadoop/apache-hadoop-use-hive-remote-desktop.md) para executar consultas do Hive.

## <a name="hiveql-language-reference"></a>Referência de linguagem HiveQL

Referência de linguagem HiveQL está disponível no [manual de idioma (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Estrutura do Hive e dados

Ramo de registo compreende como trabalhar com dados estruturados e semiestruturados. Por exemplo, ficheiros de texto em que os campos são delimitados por carateres específicas. A seguinte instrução de HiveQL cria uma tabela dados delimitada por espaços:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Ramo de registo também suporta personalizada **serializador/deserializers (SerDe)** para dados complexos ou irregularly structured. Para obter mais informações, consulte o [como utilizar um SerDe JSON personalizado com o HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) documento.

Para obter mais informações sobre os formatos de ficheiro suportados pelo ramo de registo, consulte o [manual de idioma (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

## <a name="hive-internal-tables-vs-external-tables"></a>Tabelas externas do vs tabelas interno de ramo de registo

Existem dois tipos de tabelas que pode criar com o Hive:

* __Interno__: os dados são armazenados no armazém de dados do Hive. O armazém de dados está localizado em `/hive/warehouse/` no armazenamento de predefinido para o cluster.

    Utilização interna tabelas quando:

    * Dados são temporários.
    * Pretende que o ramo de registo para gerir o ciclo de vida da tabela e os dados.

* __Externo__: os dados são armazenados fora do armazém de dados. Os dados podem ser armazenados em qualquer armazenamento acessível pelo cluster.

    Utilize externo tabelas quando:

    * Os dados são também utilizados fora do Hive. Por exemplo, os ficheiros de dados são atualizados por outro processo (que não bloqueia os ficheiros.)
    * Dados tem de permanecer na localização subjacente, mesmo depois de remover a tabela.
    * É necessário numa localização personalizada, tal como uma conta de armazenamento não predefinidas.
    * Um programa que não seja o ramo de registo gere o formato de dados, localização, etc.

Para obter mais informações, consulte o [Hive internos e externos de tabelas de introdução] [ cindygross-hive-tables] blogue.

## <a name="user-defined-functions-udf"></a>Funções definidas pelo utilizador (UDF)

Ramo de registo também pode ser expandido através de **as funções definidas pelo utilizador (UDF)**. Um UDF permite-lhe implementar funcionalidades ou lógica que não é modelada facilmente em HiveQL. Para obter um exemplo de utilização UDFs com o Hive, consulte os seguintes documentos:

* [Utilize uma função definida pelo utilizador do Java com o Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Utilizar uma função definida pelo utilizador do Python com o Hive e do Pig](../hadoop/python-udf-hdinsight.md)

* [Utilize um c# definido pelo utilizador função com o Hive e do Pig](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar uma função personalizada de definido pelo utilizador do Hive no HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Função definida pelo utilizador ao converter formatos de data/hora do Hive timestamp do Hive de exemplo](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Dados de exemplo

Hive no HDInsight vem previamente carregada com uma tabela interna chamada `hivesampletable`. O HDInsight também fornece conjuntos de dados de exemplo que podem ser utilizados com o Hive. Estes conjuntos de dados são armazenados no `/example/data` e `/HdiSamples` diretórios. Estes diretórios existem no armazenamento de predefinido para o cluster.

## <a id="job"></a>Consulta do Hive de exemplo

As seguintes declarações HiveQL projeto colunas para o `/example/data/sample.log` ficheiro:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

No exemplo anterior, as declarações HiveQL efetuar as seguintes ações:

* `set hive.execution.engine=tez;`: O motor de execução para utilizar Tez define. Utilizar Tez em vez de MapReduce pode fornecer um aumento do desempenho de consulta. Para obter mais informações sobre Tez, consulte o [utilizar o Apache Tez para um melhor desempenho](#usetez) secção.

    > [!NOTE]
    > Esta instrução só é necessário quando utilizar um cluster do HDInsight baseados em Windows. Tez é o motor de execução predefinido para o HDInsight baseado em Linux.

* `DROP TABLE`: Se a tabela já existir, elimine-o.

* `CREATE EXTERNAL TABLE`: Cria um novo **externo** tabela do Hive. As tabelas externas apenas armazenam a definição da tabela no ramo de registo. Os dados for deixados na localização original e o formato original.

* `ROW FORMAT`: Indica ao ramo de registo como estão formatados corretamente os dados. Neste caso, os campos no registo de cada são separados por um espaço.

* `STORED AS TEXTFILE LOCATION`: Indica onde os dados são armazenados de ramo de registo (o `example/data` diretório) e de que esta está armazenada como texto. Os dados podem estar num ficheiro ou distribuídos por vários ficheiros dentro do diretório.

* `SELECT`: Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[erro]**. Esta declaração devolve um valor **3** porque existem três linhas que contêm este valor.

* `INPUT__FILE__NAME LIKE '%.log'`-Hive tenta aplicar o esquema para todos os ficheiros no diretório. Neste caso, o diretório contém ficheiros que não corresponde ao esquema. Para impedir que os dados de libertação da memória nos resultados, esta instrução diz ao ramo de registo que iremos deverá devolver apenas dados de ficheiros que termina em. registo.

> [!NOTE]
> As tabelas externas devem ser utilizadas ao espera os dados subjacentes ser atualizados por uma origem externa. Por exemplo, um processo de carregamento de dados automática ou operação de MapReduce.
>
> Remover uma tabela externa **não** eliminar os dados, apenas elimina a definição da tabela.

Para criar um **interno** tabela em vez de externo, utilize o HiveQL seguinte:

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

As declarações de efetuar as seguintes ações:

* `CREATE TABLE IF NOT EXISTS`: Se a tabela não existir, crie-a. Porque o **externo** palavra-chave não é utilizada, esta instrução cria uma tabela interna. A tabela é armazenada no armazém de dados do Hive e for gerida completamente o Hive.

* `STORED AS ORC`: Armazena os dados no formato otimizada linha Columnar (ORC). ORC é um formato altamente otimizado e eficiente para armazenar dados do Hive.

* `INSERT OVERWRITE ... SELECT`: Seleciona as linhas do **log4jLogs** tabela que contém **[erro]**e, em seguida, insere os dados para o **foram** tabela.

> [!NOTE]
> Ao contrário das tabelas externas, remover uma tabela interna também elimina os dados subjacentes.

## <a name="improve-hive-query-performance"></a>Melhorar o desempenho das consultas do Hive

### <a id="usetez"></a>O Apache Tez

[O Apache Tez](http://tez.apache.org) é uma arquitetura que permite que aplicações intensivas de dados, tais como o Hive, sejam executados de forma muito mais eficiente com dimensionamento. Tez está ativado por predefinição para clusters do HDInsight baseado em Linux.

> [!NOTE]
> Tez está atualmente desativada por predefinição para os clusters do HDInsight baseados em Windows e tem de estar ativada. Para tirar partido das Tez, o seguinte valor tem de ser definido para uma consulta do Hive:
>
> `set hive.execution.engine=tez;`
>
> Tez é o motor de predefinido para clusters do HDInsight baseado em Linux.

O [do Hive no Tez criar documentos](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contém detalhes sobre as opções de implementação e configurações de otimização.

Para ajudar a depurar tarefas foi executada com Tez, o HDInsight fornece o seguinte web UIs que lhe permitem ver detalhes das tarefas de Tez:

* [Utilize a vista Ambari Tez no HDInsight baseado em Linux](../hdinsight-debug-ambari-tez-view.md)

* [Utilizar a IU do Tez no HDInsight baseado em Windows](../hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Processamento analítico de latência baixa (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (por vezes conhecida como em direto longo e o processo) é uma funcionalidade nova do ramo de registo 2.0, que permite a colocação em cache de memória de consultas. LLAP torna muito mais rápidas, até de consultas do Hive [26 x mais rápida do que 1. x, em alguns casos de ramo de registo](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

O HDInsight fornece LLAP no tipo de cluster de consulta interativo. Para obter mais informações, consulte o [começar a utilizar consultas interativas](../interactive-query/apache-interactive-query-get-started.md) documento.

## <a name="hive-jobs-and-sql-server-integration-services"></a>Tarefas do Hive e o SQL Server Integration Services

Pode utilizar o SQL Server Integration Services (SSIS) para executar uma tarefa do Hive. O pacote de funcionalidades do Azure para SSIS fornece os seguintes componentes que funcionam com tarefas do Hive no HDInsight.

* [Tarefas do Hive do HDInsight do Azure][hivetask]

* [Gestor de ligações de subscrição do Azure][connectionmanager]

Saiba mais sobre o pacote de funcionalidades do Azure para SSIS [aqui][ssispack].

## <a id="nextsteps"></a>Passos seguintes

Agora que aprendeu o que é o ramo de registo e como utilizá-la com o Hadoop no HDInsight, utilize as hiperligações seguintes para explorar as outras formas de trabalhar com o Azure HDInsight.

* [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data]
* [Use Pig with HDInsight (Utilizar o Pig com o HDInsight)][hdinsight-use-pig]
* [Utilizar tarefas de MapReduce com o HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
