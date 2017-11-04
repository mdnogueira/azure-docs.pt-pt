---
title: Analisar dados de atraso de voo com o Hive no HDInsight - Azure | Microsoft Docs
description: Saiba como utilizar o Hive para analisar dados de eventos no HDInsight baseado em Linux e, em seguida, exportar os dados para a base de dados SQL utilizando Sqoop.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 96a40753d87d49e9493e808da0294d682b2a19e5
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Analisar dados de atraso do voo utilizando o Hive no HDInsight baseado em Linux

Saiba como analisar dados de atraso do voo utilizando o Hive no HDInsight baseado em Linux e como exportar os dados para a SQL Database do Azure utilizando o Sqoop.

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. Linux é o único sistema operativo utilizado no Azure HDInsight versão 3.4 ou posterior. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster do HDInsight**. Consulte [começar a utilizar o Hadoop no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) para obter os passos sobre como criar um novo cluster do HDInsight baseado em Linux.

* **Base de Dados SQL do Azure**. Utilize uma base de dados SQL do Azure como um arquivo de dados de destino. Se não tiver uma base de dados do SQL Server, consulte [criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started.md).

* **CLI do Azure**. Se ainda não instalou a CLI do Azure, consulte [instalar a CLI do Azure 1.0](../cli-install-nodejs.md) para mais passos.

## <a name="download-the-flight-data"></a>Transferir os dados de eventos

1. Navegue até à [investigação e administração de tecnologia inovadora, Bureau das estatísticas de transportes][rita-website].

2. Na página, selecione os seguintes valores:

   | Nome | Valor |
   | --- | --- |
   | Filtrar ano |2013 |
   | Período de filtro |Janeiro |
   | Campos |Ano, FlightDate, UniqueCarrier, operadora, FlightNum, OriginAirportID, origem, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Limpe todos os outros campos. 

3. Selecione **transferir**.

## <a name="upload-the-data"></a>Carregar os dados

1. Utilize o seguinte comando para carregar o ficheiro. zip ao nó principal do cluster do HDInsight:

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    Substitua *FILENAME* com o nome do ficheiro. zip. Substitua *USERNAME* com o início de sessão SSH para o cluster do HDInsight. Substitua *CLUSTERNAME* com o nome do cluster do HDInsight.

   > [!NOTE]
   > Se utilizar uma palavra-passe para autenticar o início de sessão SSH, lhe for pedida a palavra-passe. Se utilizar uma chave pública, poderá ter de utilizar o `-i` parâmetro e especifique o caminho para a chave privada correspondente. Por exemplo, `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Após concluir o carregamento, ligar ao cluster através de SSH:

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    Para obter mais informações, veja [Ligar ao HDInsight (Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Utilize o seguinte comando para descomprimir o ficheiro. zip:

    ```
    unzip FILENAME.zip
    ```

    Este comando extrai um ficheiro. csv que é aproximadamente 60 MB.

4. Utilize o seguinte comando para criar um diretório no armazenamento do HDInsight e, em seguida, copie o ficheiro para o diretório:

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Criar e executar o HiveQL

Utilize os seguintes passos para importar dados a partir do ficheiro. csv para uma tabela do Hive com o nome **atrasos**.

1. Utilize o seguinte comando para criar e editar um novo ficheiro designado **flightdelays.hql**:

    ```
    nano flightdelays.hql
    ```

    Utilize o seguinte texto como o conteúdo deste ficheiro:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Para guardar o ficheiro, utilize Ctrl + X, então Y.

3. Para iniciar o ramo de registo e executar o **flightdelays.hql** ficheiro, utilize o seguinte comando:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Depois do __flightdelays.hql__ depois de concluída a executar o script, utilize o seguinte comando para abrir uma sessão interativa de Beeline:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Quando receber o `jdbc:hive2://localhost:10001/>` linha de comandos, utilize a seguinte consulta para obter dados a partir dos dados de atraso do voo importados:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Esta consulta obtém uma lista de cidades que Meteorologia experiente atrasa, juntamente com o tempo de atraso médio e guarda-os `/tutorials/flightdelays/output`. Mais tarde, Sqoop lê os dados a partir desta localização e exporta-a para a SQL Database do Azure.

6. Para sair Beeline, introduza `!quit` a linha.

## <a name="create-a-sql-database"></a>Criar uma base de dados SQL

Se já tiver uma base de dados do SQL Server, tem de obter o nome do servidor. Para localizar o nome de servidor no [portal do Azure](https://portal.azure.com), selecione **bases de dados SQL**e, em seguida, filtre o nome da base de dados que optar por utilizar. O nome do servidor está listado no **servidor** coluna.

Se ainda não tiver uma base de dados do SQL Server, utilize as informações em [criar uma base de dados SQL do Azure no portal do Azure](../sql-database/sql-database-get-started.md) para criar um. Guarde o nome do servidor que é utilizado para a base de dados.

## <a name="create-a-sql-database-table"></a>Criar uma tabela de base de dados do SQL Server

> [!NOTE]
> Existem várias formas para ligar à base de dados SQL e criar uma tabela. Os passos seguintes utilize [FreeTDS](http://www.freetds.org/) do HDInsight cluster.


1. Utilizar o SSH para ligar ao cluster do HDInsight baseado em Linux e execute os seguintes passos da sessão SSH.

2. Utilize o seguinte comando para instalar o FreeTDS:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Após a conclusão da instalação, utilize o seguinte comando para ligar ao servidor de base de dados SQL. Substitua **serverName** com o nome do servidor de base de dados SQL. Substitua **adminLogin** e **adminPassword** com o início de sessão da base de dados do SQL Server. Substitua **databaseName** com o nome de base de dados.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Poderá recebe um resultado semelhante ao seguinte texto:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Na `1>` solicitar, introduza as seguintes linhas:

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Quando o `GO` instrução é introduzida, são avaliadas as instruções anteriores. Cria uma tabela com o nome a esta consulta **atrasos**, com um índice em cluster.

    Utilize a seguinte consulta para verificar se a tabela foi criada:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    O resultado é semelhante ao seguinte texto:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. Introduza `exit` no `1>` linha de comandos para o utilitário de tsql de sair.

## <a name="export-data-with-sqoop"></a>Exportar dados com Sqoop

1. Utilize o seguinte comando para verificar se Sqoop pode ver a sua base de dados do SQL Server:

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Este comando devolve uma lista de bases de dados, incluindo a base de dados no qual criou a tabela de atrasos anteriormente.

2. Utilize o seguinte comando para exportar dados do hivesampletable para a tabela de atrasos:

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop estabelece ligação à base de dados que contém a tabela de atrasos e exporta dados a partir de `/tutorials/flightdelays/output` diretório para a tabela de atrasos.

3. Depois de concluir o comando sqoop, utilize o utilitário de tsql para ligar à base de dados:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Utilize as instruções seguintes para verificar que os dados foram exportados para a tabela de atrasos:

    ```
    SELECT * FROM delays
    GO
    ```

    Deverá ver uma lista dos dados da tabela. Tipo `exit` para sair do utilitário de tsql.

## <a name="next-steps"></a>Passos seguintes

Para saber mais formas de trabalhar com dados no HDInsight, consulte os artigos seguintes:

* [Use Hive with HDInsight (Utilizar o Hive com o HDInsight)][hdinsight-use-hive]
* [Utilizar o Oozie com o HDInsight][hdinsight-use-oozie]
* [Utilizar o Sqoop com o HDInsight][hdinsight-use-sqoop]
* [Use Pig with HDInsight (Utilizar o Pig com o HDInsight)][hdinsight-use-pig]
* [Desenvolver programas de Java MapReduce para o Hadoop no HDInsight][hdinsight-develop-mapreduce]
* [Desenvolver Python MapReduce programas transmissão em fluxo para o HDInsight][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
