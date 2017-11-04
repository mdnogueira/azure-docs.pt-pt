---
title: Utilizar Beeline com Apache Hive - o Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar o cliente Beeline para executar consultas do Hive com o Hadoop no HDInsight. Beeline é um utilitário para trabalhar com HiveServer2 através de JDBC."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: ramo de registo do beeline beeline de ramo de registo
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: 7c582c81aac889b2b6f57777fab4531107e0fad3
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-the-beeline-client-with-apache-hive"></a>Utilize o cliente Beeline com Apache Hive

Saiba como utilizar [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para executar consultas do Hive no HDInsight.

Beeline for um cliente de ramo de registo que está incluído em nós principais do cluster do HDInsight. Beeline utiliza JDBC para ligar à HiveServer2, um serviço alojado num cluster do HDInsight. Também pode utilizar Beeline para aceder do Hive no HDInsight remotamente através da internet. Os exemplos seguintes fornecem as cadeias de ligação mais comuns utilizadas para estabelecer ligação ao HDInsight de Beeline:

* __Utilizando Beeline a partir de uma ligação SSH para um nó headnode ou o Microsoft edge__:`-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __Utilizar Beeline num cliente, a ligação ao HDInsight através de uma rede Virtual do Azure__:`-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __Utilizar Beeline num cliente, a ligação ao HDInsight através da internet pública__:`-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]
> Substitua `admin` com a conta de início de sessão do cluster para o cluster.
>
> Substitua `password` com a palavra-passe para a conta de início de sessão do cluster.
>
> Substitua `clustername` pelo nome do seu cluster do HDInsight.
>
> Quando ligar ao cluster através de uma rede virtual, substitua `<headnode-FQDN>` com o nome de domínio completamente qualificado de um cluster headnode.

## <a id="prereq"></a>Pré-requisitos

* Um Hadoop baseado em Linux num cluster do HDInsight.

  > [!IMPORTANT]
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um cliente SSH ou cliente Beeline local. A maior parte dos passos neste documento partem do princípio de que está a utilizar Beeline numa sessão SSH para o cluster. Para obter informações sobre a execução Beeline a partir de fora do cluster, consulte o [utilizar Beeline remotamente](#remote) secção.

    Para obter mais informações sobre como utilizar o SSH, consulte [utilizar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Utilizar Beeline

1. Ao iniciar Beeline, tem de fornecer uma cadeia de ligação de HiveServer2 no cluster do HDInsight:

    * Quando ligar através da internet pública, tem de fornecer o nome de conta de início de sessão do cluster (predefinido `admin`) e palavra-passe. Por exemplo, utilizando Beeline de um sistema de cliente para estabelecer ligação com o `<clustername>.azurehdinsight.net` endereço. Esta ligação é efetuada através da porta `443`e é encriptada utilizando SSL:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * Quando ligar a partir de uma sessão SSH para um cluster headnode, pode ligar para o `headnodehost` endereço na porta `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * Quando ligar através de uma rede Virtual do Azure, tem de fornecer o nome de domínio completamente qualificado (FQDN) de um nó principal do cluster. Uma vez que esta ligação é efetuada diretamente para os nós de cluster, a ligação utiliza a porta `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```

2. Comandos de beeline começam com uma `!` caráter, por exemplo `!help` apresenta a ajuda. No entanto, a `!` podem ser omitidas para alguns comandos. Por exemplo, `help` também funciona.

    Não existe um `!sql`, que é utilizada para executar declarações HiveQL. No entanto, HiveQL é por isso, normalmente utilizada que pode omitir precedente `!sql`. As seguintes declarações de dois são equivalentes:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Num cluster novo, apenas uma tabela está listada: **hivesampletable**.

3. Utilize o seguinte comando para apresentar o esquema para o hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Este comando devolve as seguintes informações:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Estas informações descrevem as colunas na tabela. Enquanto, pode efetuar algumas consultas contra estes dados, vamos em vez disso, crie uma nova tabela para demonstrar como carregar dados para o ramo e aplicar um esquema.

4. Introduza as seguintes instruções para criar uma tabela com o nome **log4jLogs** utilizando dados de exemplo fornecidos com o cluster do HDInsight:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    As declarações de efetuar as seguintes ações:

    * `DROP TABLE`-Se a tabela existe, é eliminado.

    * `CREATE EXTERNAL TABLE`-Cria um **externo** tabela do Hive. As tabelas externas apenas armazenam a definição da tabela no ramo de registo. Os dados for deixados na localização original.

    * `ROW FORMAT`-Como estão formatados corretamente os dados. Neste caso, os campos no registo de cada são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION`-Onde os dados são armazenados e o formato de ficheiro.

    * `SELECT`-Seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[erro]**. Esta consulta devolve um valor **3** porque existem três linhas que contêm este valor.

    * `INPUT__FILE__NAME LIKE '%.log'`-Hive tenta aplicar o esquema para todos os ficheiros no diretório. Neste caso, o diretório contém ficheiros que não corresponde ao esquema. Para impedir que os dados de libertação da memória nos resultados, esta instrução diz ao ramo de registo que iremos deverá devolver apenas dados de ficheiros que termina em. registo.

  > [!NOTE]
  > As tabelas externas devem ser utilizadas ao espera os dados subjacentes ser atualizados por uma origem externa. Por exemplo, um processo de carregamento de dados automática ou uma operação de MapReduce.
  >
  > Remover uma tabela externa **não** eliminar os dados, a definição de tabela.

    O resultado deste comando é semelhante ao seguinte texto:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

5. Para sair Beeline, utilize `!exit`.

## <a id="file"></a>Utilize Beeline para executar um ficheiro de HiveQL

Utilize os seguintes passos para criar um ficheiro, em seguida, execute-o utilizando Beeline.

1. Utilize o seguinte comando para criar um ficheiro denominado **query.hql**:

    ```bash
    nano query.hql
    ```

2. Utilize o seguinte texto como o conteúdo do ficheiro. Esta consulta cria uma nova tabela 'interna' com o nome **foram**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    As declarações de efetuar as seguintes ações:

    * **Criar tabela não se existe** -se a tabela já não existir, será criado. Uma vez que o **externo** palavra-chave não é utilizada, esta instrução cria uma tabela interna. Tabelas internas são armazenadas no armazém de dados do Hive e são geridas completamente pelo ramo de registo.
    * **ARMAZENADOS ORC de AS** -armazena os dados no formato otimizada linha Columnar (ORC). Formato ORC é um formato altamente otimizado e eficiente para armazenar dados do Hive.
    * **SUBSTITUIR INSERT... SELECIONE** -seleciona as linhas do **log4jLogs** tabela que contenham **[erro]**, insere, em seguida, os dados para o **foram** tabela.

    > [!NOTE]
    > Ao contrário das tabelas externas, remover uma tabela interna elimina dados subjacentes.

3. Para guardar o ficheiro, utilize **Ctrl**+**_X**, em seguida, introduza **Y**e, finalmente, **Enter**.

4. Utilize o seguinte para executar o ficheiro utilizando Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]
    > O `-i` parâmetro inicia Beeline, executa as instruções no ficheiro query.hql. Uma vez concluída a consulta, chegam a `jdbc:hive2://headnodehost:10001/>` linha. Também pode executar um ficheiro utilizando o `-f` parâmetro, que sai Beeline após a conclusão da consulta.

5. Para verificar se o **foram** tabela foi criada, utilize a seguinte instrução para devolver todas as linhas da **foram**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Três linhas de dados devem ser devolvidas, todos os contentora **[erro]** na coluna t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Utilizar Beeline remotamente

Se tiver Beeline instalado localmente e ligar através da internet pública, utilize os seguintes parâmetros:

* __Cadeia de ligação__:`-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Nome de início de sessão do cluster__:`-n admin`

* __Palavra-passe de início de sessão do cluster__`-p 'password'`

Substitua o `clustername` na cadeia de ligação com o nome do cluster do HDInsight.

Substitua `admin` com o nome do início de sessão do cluster e substitua `password` com a palavra-passe para o início de sessão do cluster.

Se tiver Beeline instalado localmente e ligar através de uma rede Virtual do Azure, utilize os seguintes parâmetros:

* __Cadeia de ligação__:`-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Para localizar o nome de domínio completamente qualificado de um headnode, utilize as informações de [gerir HDInsight utilizando a API de REST do Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) documento.

## <a id="sparksql"></a>Utilizar Beeline com o Spark

O Spark fornece a suas próprias implementação do HiveServer2, o que é frequentemente referido como o servidor Thrift de Spark. Este serviço utiliza o Spark SQL para resolver as consultas em vez do Hive e pode fornecer um melhor desempenho, dependendo da sua consulta.

Para ligar ao servidor Thrift de Spark de um Spark no HDInsight cluster, utilize porta `10002` em vez de `10001`. Por exemplo, `beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'`.

> [!IMPORTANT]
> O servidor Thrift de Spark não está acessível diretamente através da internet. Apenas poderá ligar ao mesmo a partir de uma sessão SSH ou dentro da mesma rede Virtual do Azure como o cluster do HDInsight.

## <a id="summary"></a><a id="nextsteps"></a>Passos seguintes

Para obter informações mais gerais sobre o Hive no HDInsight, consulte o documento seguinte:

* [Utilizar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter mais informações sobre outras formas pode trabalhar com o Hadoop no HDInsight, consulte os seguintes documentos:

* [Utilizar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se estiver a utilizar com o Hive no Tez, consulte os seguintes documentos:

* [Utilizar a IU do Tez no HDInsight baseado em Windows](../hdinsight-debug-tez-ui.md)
* [Utilize a vista Ambari Tez no HDInsight baseado em Linux](../hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
