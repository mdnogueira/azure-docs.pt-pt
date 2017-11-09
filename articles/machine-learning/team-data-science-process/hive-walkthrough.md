---
title: Explore os dados num cluster de Hadoop e criar modelos no Azure Machine Learning | Microsoft Docs
description: "O processo de ciência de dados do agrupamento a utilizar para um cenário ponto-a-ponto, a utilização de um cluster de Hadoop do HDInsight para criar e implementar um modelo com um conjunto de dados publicamente disponível."
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e9e76c91-d0f6-483d-bae7-2d3157b86aa0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 1be39ab258235740c7e0875a5c0c29ee4a665a71
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>O processo de ciência de dados de equipa em ação: utilização do Azure HDInsight Hadoop clusters
Esta explicação passo a passo, utilizamos o [processo de ciência de dados de equipa (TDSP)](overview.md) um cenário ponto-a-ponto utilizando um [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explore e funcionalidade dados engenheiro do publicamente disponível [NYC Taxi viagens](http://www.andresmh.com/nyctaxitrips/) conjunto de dados e para os dados de exemplo para baixo. Modelos dos dados são criados com o Azure Machine Learning para processar várias classes e binárias classificação e regressão preditivos tarefas.

Para instruções que mostra como lidar com um conjunto de dados (1 terabyte) superior para uma semelhante cenário utilizando clusters do HDInsight Hadoop para o processamento de dados, consulte [processo de ciência de dados de equipa - utilizando o Azure HDInsight Clusters do Hadoop um conjunto de dados de 1 TB](hive-criteo-walkthrough.md) .

Também é possível utilizar um bloco de notas IPython para realizar as tarefas apresentadas as instruções utilizando o conjunto de dados de 1 TB. Os utilizadores que gostaria de tentar esta abordagem devem consultar a [instruções Criteo utilizando uma ligação de ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) tópico.

## <a name="dataset"></a>Descrição do conjunto de dados do NYC Taxi viagens
Os dados de NYC Taxi viagem cerca de 20GB de ficheiros comprimidos valores separados por vírgulas (CSV) (GB de ~ 48 descomprimido), que inclui mais de 173 milhões de viagens individuais e os fares paga para cada viagem. Cada registo viagem inclui a localização de recolha e drop-off e a hora, acesso anónimos (controlador) número de licença e número de medallion (id exclusivo do taxi). Os dados abrange todos os viagens no ano 2013 e são fornecidos nos seguintes dois conjuntos de dados de cada mês:

1. Os ficheiros CSV 'trip_data' contém detalhes de viagem, tais como o número de passageiros, recolha e dropoff pontos, duração de viagem e comprimento viagem. Seguem-se alguns registos de exemplo:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Os ficheiros CSV 'trip_fare' contém detalhes de fare paga para cada viagem, tais como o tipo de pagamento, a quantidade de fare, surcharge e, sugestões e impostos tolls e a quantidade total paga. Seguem-se alguns registos de exemplo:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para associar a viagem\_dados e viagem\_fare é composto por campos: medallion, acesso\_licenciamento e a recolha\_datetime.

Para obter todos os detalhes relevantes para uma determinado viagem, é suficiente juntar com três chaves: "medallion", "hack\_licença" e "recolha\_datetime".

Iremos descrevem algumas mais detalhes sobre os dados quando armazenamos-los para as tabelas do Hive em breve.

## <a name="mltasks"></a>Exemplos de tarefas de predição
Quando os dados, determinar o tipo de predições que pretende efetuar a aproximar-se com base na respetiva análise ajuda a clarificar as tarefas que precisa de incluir o processo.
Seguem-se exemplos de três dos problemas de predição que iremos abordar esta explicação passo a passo cuja formulação baseia-se no *sugestão\_quantidade*:

1. **Classificação binária**: prever ou não uma sugestão foi paga para uma viagem, ou seja, um *sugestão\_quantidade* que é superior ao $0 é um exemplo positivo, enquanto um *sugestão\_quantidade* $ 0 é um exemplo negativo.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
2. **Classificação de várias classes**: prever o intervalo de quantidades de sugestão paga para a viagem. Iremos dividir os *sugestão\_quantidade* em cinco intervalos binários ou classes:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
3. **Tarefa de regressão**: para prever a quantidade de sugestão paga para uma viagem.  

## <a name="setup"></a>Configurar um cluster do HDInsight Hadoop para análise avançada
> [!NOTE]
> Isto é normalmente um **Admin** tarefas.
> 
> 

Pode configurar um ambiente do Azure para análise avançada que utiliza um cluster do HDInsight em três passos:

1. [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md): esta conta de armazenamento é utilizada para armazenar dados no Blob Storage do Azure. Os dados utilizados nos clusters do HDInsight também residem aqui.
2. [Personalizar clusters do Hadoop do Azure HDInsight para o processo de análise avançada e tecnologia](customize-hadoop-cluster.md). Este passo cria um Hadoop do HDInsight Azure cluster com o Python 2.7 Anaconda 64 bits instalado em todos os nós. Existem dois passos importantes lembrar-se ao personalizar o cluster do HDInsight.
   
   * Não se esqueça de ligação criada no passo 1 com o cluster do HDInsight ao criar a conta de armazenamento. Esta conta de armazenamento é utilizada para aceder aos dados que são processados no cluster.
   * Depois do cluster for criado, ative o acesso remoto para o nó principal do cluster. Navegue para o **configuração** separador e clique em **ativar remoto**. Este passo Especifica as credenciais de utilizador utilizadas para início de sessão remoto.
3. [Criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md): área de trabalho este Azure Machine Learning é utilizada para criar modelos de machine learning. Esta tarefa é tratada depois de concluir uma exploração de dados inicial e para baixo amostragem utilizando o cluster do HDInsight.

## <a name="getdata"></a>Obter os dados a partir de uma origem pública
> [!NOTE]
> Isto é normalmente um **Admin** tarefas.
> 
> 

Para obter o [NYC Taxi viagens](http://www.andresmh.com/nyctaxitrips/) conjunto de dados da sua localização pública, poderá utilizar qualquer um dos métodos descritos em [mover dados para e do armazenamento de Blobs do Azure](move-azure-blob.md) para copiar os dados ao seu computador.

Aqui iremos descrevem como utilizar o AzCopy para transferir os ficheiros que contêm dados. Para transferir e instalar o AzCopy siga as instruções apresentadas em [introdução com o utilitário de linha de comandos do AzCopy](../../storage/common/storage-use-azcopy.md).

1. A partir de uma janela da linha de comandos, emita os seguintes comandos do AzCopy, substituindo *< path_to_data_folder >* com o destino pretendido:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Quando a cópia estiver concluída, um total de 24 ficheiros zipped estão na pasta dados escolhida. Descomprimir os ficheiros transferidos para o mesmo diretório no seu computador local. Tome nota da pasta onde residem os ficheiros descomprimidos. Esta pasta irá ser referida como o *< caminho\_para\_unzipped_data\_ficheiros\>*  é o que se segue.

## <a name="upload"></a>Carregar os dados para o contentor predefinido do cluster de Hadoop do Azure HDInsight
> [!NOTE]
> Isto é normalmente um **Admin** tarefas.
> 
> 

Nos seguintes comandos do AzCopy, substitua os seguintes parâmetros com os valores reais que especificou ao criar o cluster do Hadoop e unzipping os ficheiros de dados.

* ***&#60; path_to_data_folder >*** o diretório (juntamente com o caminho) no seu computador que contêm os ficheiros de dados deszipada  
* ***&#60; nome da conta de armazenamento de cluster do Hadoop >*** a conta de armazenamento associada cluster do HDInsight
* ***&#60; contentor predefinido do cluster de Hadoop >*** contentor predefinido utilizado pelo cluster. Tenha em atenção que o nome do contentor predefinido é, normalmente, o mesmo nome de cluster em si. Por exemplo, se o cluster é chamado "abc123.azurehdinsight.net", o contentor predefinido é abc123.
* ***&#60; a chave de conta de armazenamento >*** a chave para a conta de armazenamento utilizada por cluster

A partir de uma linha de comandos ou uma janela do Windows PowerShell no seu computador, execute os seguintes comandos do AzCopy dois.

Este comando carrega os dados viagem ***nyctaxitripraw*** diretório no contentor predefinido do cluster de Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Este comando carrega os dados fare ***nyctaxifareraw*** diretório no contentor predefinido do cluster de Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Os dados devem agora no Blob Storage do Azure e pronto a ser consumidos dentro do cluster do HDInsight.

## <a name="#download-hql-files"></a>Iniciar sessão no nó principal do cluster do Hadoop e e preparar para análises exploratórias dados
> [!NOTE]
> Isto é normalmente um **Admin** tarefas.
> 
> 

Para aceder ao nó principal do cluster para análises exploratórias dados e para baixo amostragem dos dados, siga o procedimento descrito no [aceder a Head nó de Cluster do Hadoop](customize-hadoop-cluster.md).

Esta explicação passo a passo, utilizamos principalmente consultas escritas em [Hive](https://hive.apache.org/), uma linguagem de consulta como o SQL Server, para efetuar explorations dados preliminar. As consultas do Hive são armazenadas em ficheiros .hql. Vamos, em seguida, para baixo de exemplo estes dados a utilizar no Azure Machine Learning para criar modelos.

Para preparar o cluster para análise de dados exploratórias, iremos transferir os ficheiros de .hql que contém os scripts de ramo de registo relevantes do [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) para um diretório local (C:\temp) no nó principal. Para tal, abra o **linha de comandos** de dentro do nó principal do cluster e emitir os seguintes dois comandos:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Estes dois comandos irão transferir todos os ficheiros de .hql necessários esta explicação passo a passo para o diretório local ***C:\temp &#92;*** no nó principal.

## <a name="#hive-db-tables"></a>Criar base de dados do Hive e tabelas particionadas por mês
> [!NOTE]
> Isto é normalmente um **Admin** tarefas.
> 
> 

Iremos agora está prontos para criar as tabelas do Hive para o nosso conjunto de dados de taxi NYC.
No nó principal do cluster de Hadoop, abra o ***linha de comandos do Hadoop*** no ambiente de trabalho de nó principal e introduza o diretório de ramo de registo, introduzindo o comando

    cd %hive_home%\bin

> [!NOTE]
> **Executar todos os comandos de ramo de registo esta explicação passo a passo da Reciclagem de ramo de registo acima / linha de comandos do diretório. Isto tratará de quaisquer problemas de caminho automaticamente. Utilizamos os termos "Linha de diretório de ramo de registo", "Hive bin / linha de comandos do diretório" e "Hadoop linha de comandos"-no alternadamente nestas instruções.**
> 
> 

Na linha de comandos do diretório da ramo de registo, introduza o seguinte comando no Hadoop linha de comandos do nó principal para submeter a consulta do Hive para criar a base de dados do Hive e tabelas:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Eis o conteúdo do ***C:\temp\sample\_hive\_criar\_db\_e\_tables.hql*** ficheiro que cria a base de dados do Hive ***nyctaxidb*** e tabelas ***viagem*** e ***fare***.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Este script de ramo de registo cria duas tabelas:

* a tabela de "viagem" contém detalhes viagem de cada substituir (detalhes do controlador, recolha tempo, distância viagem e)
* a tabela de "fare" contém detalhes fare (quantidade fare, a quantidade de sugestão, tolls e surcharges).

Se necessitar de qualquer assistência com estes procedimentos adicional ou pretende investigar aqueles alternativos, consulte a secção [consultas submeter Hive diretamente partir da linha de comandos do Hadoop ](move-hive-tables.md#submit).

## <a name="#load-data"></a>Carregar dados para as tabelas do Hive, partições
> [!NOTE]
> Isto é normalmente um **Admin** tarefas.
> 
> 

O conjunto de dados de taxi NYC tem uma criação de partições natural por mês, o que utilizamos para ativar a tempos mais rápidos de processamento e a consulta. Os comandos do PowerShell abaixo (emitidos a partir do diretório de ramo de registo utilizando o **linha de comandos do Hadoop**) carregar dados para as tabelas do Hive "viagem" e "fare" particionadas por mês.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

O *exemplo\_hive\_carregar\_dados\_por\_partitions.hql* ficheiro contém o seguinte **carregar** comandos.

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Tenha em atenção que um número de consultas do Hive, que iremos utilizar aqui no processo de exploração envolve procura num apenas uma única partição ou em apenas alguns partições. Mas estas consultas poderá ser executadas em todos os dados de todos.

### <a name="#show-db"></a>Mostrar as bases de dados do cluster do HDInsight Hadoop
Para mostrar as bases de dados criados no cluster do HDInsight Hadoop dentro da janela de linha de comandos do Hadoop, execute o seguinte comando na linha de comandos do Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Mostrar as tabelas de ramo de registo na base de dados nyctaxidb
Para mostrar as tabelas na base de dados nyctaxidb, execute o seguinte comando na linha de comandos do Hadoop:

    hive -e "show tables in nyctaxidb;"

Iremos pode confirmar que as tabelas são particionadas por emissão do comando abaixo:

    hive -e "show partitions nyctaxidb.trip;"

O resultado esperado é mostrado abaixo:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

Da mesma forma, podemos garantir que o se a tabela de fare particionada emitir o comando abaixo:

    hive -e "show partitions nyctaxidb.fare;"

O resultado esperado é mostrado abaixo:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Exploração de dados e de engenharia da funcionalidade no ramo de registo
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

A exploração de dados e a funcionalidade de tarefas para os dados carregados para as tabelas do Hive de engenharia podem ser conseguidos através de consultas do Hive. Seguem-se exemplos dessas tarefas, que iremos guiá-lo nesta secção:

* Ver os registos de 10 principais em ambas as tabelas.
* Explore as distribuições de dados de alguns campos no variando intervalos de tempo.
* Investigue a qualidade dos dados dos campos de latitude e longitude.
* Gerar etiquetas de classificação de várias classes e binária com base no **sugestão\_quantidade**.
* Gere funcionalidades pela computação as distâncias viagem direta.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Exploração: Ver os registos de 10 principais em viagem de tabela
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

Para ver o aspeto de dados, vamos examinar 10 registos de cada tabela. Execute as seguintes consultas de dois separadamente a partir da linha de diretório de ramo de registo na consola de linha de comandos do Hadoop para inspecionar os registos.

Para obter os registos de 10 principais na tabela "viagem" a partir do primeiro mês:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Para obter os registos de 10 principais na tabela "fare" do primeiro mês:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Muitas vezes, é útil guardar os registos para um ficheiro para uma visualização conveniente. Uma alteração de pequenas a consulta acima executa esta operação:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Exploração: Ver o número de registos em cada uma das 12 partições
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

De interesse é a forma como o número de viagens varia durante o ano de calendário. Agrupamento por mês permite-nos ver o aspeto esta distribuição viagens.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Isto dá-na saída:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

Aqui, a primeira coluna é mês e o segundo é o número de viagens para esse mês.

Também iremos pode contagem o número total de registos no nosso conjunto de dados viagem ao emitir o comando seguinte na linha de comandos do diretório do ramo de registo.

    hive -e "select count(*) from nyctaxidb.trip;"

Isto gera:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Utilizar comandos semelhantes às mostrado para o conjunto de dados viagem, iremos podem emitir consultas do Hive de linha de diretório de ramo de registo para o conjunto de dados fare validar o número de registos.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Isto dá-na saída:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Tenha em atenção que o mesmo número exato de viagens por mês é devolvido para ambos os conjuntos de dados. Isto fornece a validação primeiro que os dados foram carregados corretamente.

O número total de registos no conjunto de dados de fare de contagem pode ser feito utilizando o comando abaixo da linha de comandos do diretório do ramo de registo:

    hive -e "select count(*) from nyctaxidb.fare;"

Isto gera:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

O número total de registos em ambas as tabelas também é o mesmo. Esta opção fornece uma segundo validação que os dados foram carregados corretamente.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

Neste exemplo identifica o medallion (taxi números) com mais do que 100 viagens dentro de um determinado período de tempo. A consulta é vantajosa contra o acesso de tabela particionada necessitar, uma vez que este é ter pela variável de partição **mês**. Os resultados da consulta são escritos queryoutput.tsv um ficheiro local na `C:\temp` no nó principal.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Eis o conteúdo de *exemplo\_hive\_viagem\_contagem\_por\_medallion.hql* ficheiro para inspecção.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medallion no conjunto de dados taxi NYC identifica um ficheiro cab exclusivo. Podermos identificar os ficheiros CAB é "ocupado" ao solicitar aqueles efetuadas mais do que um determinado número de viagens num período de tempo específico. O exemplo seguinte identifica os ficheiros. cab que efetuou a mais do que uma centena viagens na primeira durante três meses e guarda a consulta resulta num ficheiro local, C:\temp\queryoutput.tsv.

Eis o conteúdo de *exemplo\_hive\_viagem\_contagem\_por\_medallion.hql* ficheiro para inspecção.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Na linha de comandos do diretório da Hive, emita o comando abaixo:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploração: Distribuição de viagem medallion e hack_license
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

Quando a explorar a um conjunto de dados, queremos frequentemente examinar o número de co-occurences dos grupos de valores. Esta secção fornece um exemplo de como fazê-lo para controladores e ficheiros CAB.

O *exemplo\_hive\_viagem\_contagem\_por\_medallion\_license.hql* o conjunto de dados fare "medallion" e "hack_license" grupos de ficheiros e Devolve o número de cada combinação. Seguem-se o respetivo conteúdo.

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Esta consulta devolve combinações de controlador específico ordenadas pelo número descendente de viagens e de ficheiros cab.

A partir da linha de comandos do diretório da ramo de registo, execute:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Os resultados da consulta são escritos num ficheiro local C:\temp\queryoutput.tsv.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitudelatitude-records"></a>Exploração: Avaliar a qualidade dos dados verificando os registos de longitude/latitude inválido
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

Um objetivo comuns de análise de dados exploratórias é weed saída registos inválidos ou danificados. O exemplo nesta secção determina se os campos de longitude ou latitude contém um valor até que ponto fora da área de NYC. Uma vez que é provável que esses registos têm valores de latitude de longitude errada, queremos eliminá-los a partir de quaisquer dados que está a ser utilizado para a modelação.

Eis o conteúdo de *exemplo\_hive\_qualidade\_assessment.hql* ficheiro para inspecção.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


A partir da linha de comandos do diretório da ramo de registo, execute:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

O *-S* argumento incluído neste comando suprime printout de ecrã do estado das tarefas de mapa/reduza ramo de registo. Isto é útil porque faz o ecrã de impressão do resultado de consulta do Hive mais legível.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploração: Distribuições de classe binário das sugestões viagem
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

Para o problema de classificação binária descrito no [exemplos das tarefas de predição](hive-walkthrough.md#mltasks) secção, é útil para saber se uma sugestão foi fornecida ou não. Esta distribuição das sugestões é binária:

* Sugestão fornecido (1 de classe, sugestão\_culmina > $0)  
* nenhuma sugestão (classe 0, sugestão\_quantidade = $0).

O *exemplo\_hive\_tipped\_frequencies.hql* ficheiro mostrado abaixo efetua este procedimento.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

A partir da linha de comandos do diretório da ramo de registo, execute:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Exploração: Distribuições na definição de várias classes de classe
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

Para o problema de classificação de várias classes descrito no [exemplos das tarefas de predição](hive-walkthrough.md#mltasks) secção este conjunto de dados também presta-se a uma classificação natural onde gostaríamos de prever a quantidade de sugestões fornecido. Podemos utilizar intervalos binários para definir os intervalos de sugestão na consulta. Para obter as distribuições de classe para a vários intervalos de sugestão, utilizamos o *exemplo\_hive\_sugestão\_intervalo\_frequencies.hql* ficheiro. Seguem-se o respetivo conteúdo.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Execute o seguinte comando a partir da consola de linha de comandos do Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-direct-distance-between-two-longitude-latitude-locations"></a>Exploração: Direta distância entre duas localizações de Longitude Latitude de computação
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

Ter uma medida da distância direta permite-nos descobrir a discrepância entre ele e a distância viagem real. Esta funcionalidade é motivate mostrando que um passenger poderá ser menos provável é que a sugestão se estes descobrir que o controlador tem intencionalmente colocada-los por uma rota muito mais.

Para ver a comparação entre distância viagem real e o [Haversine distância](http://en.wikipedia.org/wiki/Haversine_formula) entre dois pontos de longitude latitude (a distância "ótimo círculo"), vamos utilizar as funções de trigonometric disponíveis dentro do Hive, assim:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

A consulta acima, R é radius terrestre em quilómetros e pi é convertida em radianos. Tenha em atenção que os pontos de longitude latitude são "filtrados" Remover os valores que são longe da área de NYC.

Neste caso, iremos escrever os nossos resultados para um diretório denominado "queryoutputdir". A sequência de comandos mostrados abaixo cria pela primeira vez este diretório de saída e, em seguida, executa o comando de ramo de registo.

A partir da linha de comandos do diretório da ramo de registo, execute:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Os resultados da consulta são escritos para 9 blobs do Azure ***queryoutputdir/000000\_0*** para ***queryoutputdir/000008\_0*** sob o contentor predefinido do cluster de Hadoop.

Para ver o tamanho dos blobs individuais, iremos executar o seguinte comando na linha de comandos do diretório da ramo de registo:

    hdfs dfs -ls wasb:///queryoutputdir

Para ver os conteúdos de um determinado ficheiro, diga 000000\_0, podemos utilizar do Hadoop `copyToLocal` assim, o comando.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal`pode ser muito lento para ficheiros grandes e não é recomendado para utilização com as mesmas.  
> 
> 

Uma vantagem de ter estes dados residem num blob do Azure é que podemos pode explorar os dados no Azure Machine Learning utilizando o [importar dados] [ import-data] módulo.

## <a name="#downsample"></a>Para baixo de modelos de compilação e dados de exemplo no Azure Machine Learning
> [!NOTE]
> Isto é normalmente um **dados Scientist** tarefas.
> 
> 

Após a fase de análise de dados exploratórias, iremos agora está prontos para pendente exemplo os dados para a criação de modelos no Azure Machine Learning. Nesta secção, mostramos como utilizar uma consulta do Hive para pendente exemplo os dados, que, em seguida, são acedidos a partir de [importar dados] [ import-data] módulo no Azure Machine Learning.

### <a name="down-sampling-the-data"></a>Para baixo de amostragem de dados
Existem dois passos neste procedimento. Primeiro vamos associar o **nyctaxidb.trip** e **nyctaxidb.fare** tabelas em três chaves que estão presentes em todos os registos: "medallion", "hack\_licença", e "recolha\_datetime". Em seguida, geramos uma etiqueta de classificação binária **tipped** e uma etiqueta de classificação de classe Multi **sugestão\_classe**.

Para poder utilizar a baixo amostragem dados diretamente a partir de [importar dados] [ import-data] módulo no Azure Machine Learning, é necessário armazenar os resultados da consulta acima para uma tabela do Hive interna. O que se segue, iremos criar uma tabela do Hive interna e preencher o respetivo conteúdo com o associados e para baixo amostras de dados.

A consulta aplica-se o padrão do Hive funções diretamente para gerar a hora do dia, semana do ano, dia da semana (1 consegue para segunda-feira e 7 consegue para Domingo) da "recolha\_datetime" campo e a distância entre as localizações de recolha e dropoff direta. Os utilizadores podem fazer referência aos [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) para uma lista completa dessas funções.

A consulta, em seguida, para baixo amostras de dados para que os resultados da consulta podem ajustar à Azure Machine Learning Studio. Apenas cerca de 1% do conjunto de dados original é importado para o Studio.

Seguem-se os conteúdos do *exemplo\_hive\_preparar\_para\_aml\_full.hql* ficheiro prepara dados para o modelo a criar no Azure Machine Learning.

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Para executar esta consulta, a partir da linha de diretório de ramo de registo:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Agora, temos uma tabela interna "nyctaxidb.nyctaxi_downsampled_dataset", que pode ser acedido através de [importar dados] [ import-data] módulo do Azure Machine Learning. Além disso, podemos utilizar este conjunto de dados para a criação de modelos de Machine Learning.  

### <a name="use-the-import-data-module-in-azure-machine-learning-to-access-the-down-sampled-data"></a>Utilizar o módulo de importar dados no Azure Machine Learning para aceder as baixo amostras de dados
Como pré-requisitos para emitir Hive as consultas no [importar dados] [ import-data] módulo do Azure Machine Learning, precisamos de acesso a uma área de trabalho de aprendizagem do Azure e o acesso às credenciais do cluster e a respetiva conta do storage associada.

Alguns detalhes sobre o [importar dados] [ import-data] módulo e os parâmetros de entrada:

**URI do servidor de HCatalog**: se o nome do cluster é abc123, isto é simplesmente: https://abc123.azurehdinsight.net

**Nome de conta de utilizador do Hadoop**: O nome de utilizador escolhido para o cluster (**não** o nome de utilizador de acesso remoto)

**Palavra-passe de conta do Hadoop ser**: A palavra-passe escolhida para o cluster (**não** a palavra-passe de acesso remoto)

**Localização dos dados de saída**: Isto é escolhido para ser o Azure.

**Nome da conta de armazenamento do Azure**: nome da conta do storage predefinida associada com o cluster.

**Nome do contentor do Azure**: Este é o nome do contentor predefinido para o cluster e, normalmente é o mesmo que o nome do cluster. Para um cluster chamado "abc123", o que é abc123 apenas.

> [!IMPORTANT]
> **Qualquer tabela pretendemos consultar através de [importar dados] [ import-data] módulo no Azure Machine Learning tem de ser uma tabela interna.** Segue-se uma sugestão para determinar se uma tabela T numa base de dados D.db é uma tabela interna.
> 
> 

Na linha de comandos do diretório da Hive, emita o comando:

    hdfs dfs -ls wasb:///D.db/T

Se a tabela é uma tabela interna e é preenchida, o seu conteúdo deve mostrar aqui. Outra forma de determinar se uma tabela é uma tabela interna consiste em utilizar o Explorador de armazenamento do Azure. Utilizá-la para navegar para o nome de contentor predefinido do cluster e, em seguida, filtre pelo nome de tabela. Se a tabela e o respetivo conteúdo apresentada, confirma que o se se trata de uma tabela interna.

Eis um instantâneo da consulta do Hive e o [importar dados] [ import-data] módulo:

![Consulta do Hive para o módulo de importar dados](./media/hive-walkthrough/1eTYf52.png)

Tenha em atenção que, desde a nossa baixo amostras de dados residem num contentor predefinido, a consulta do Hive resultante do Azure Machine Learning é muito simple e é apenas uma "SELECIONAR * de nyctaxidb.nyctaxi\_downsampled\_dados".

O conjunto de dados pode agora ser utilizado como ponto de partida para a criação de modelos de Machine Learning.

### <a name="mlmodel"></a>Criar modelos no Azure Machine Learning
Agora é possível continuar a criação de modelo e implementação de modelo no [Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos para a utilização na corrigir os problemas de predição identificados acima:

**1. Classificação binária**: prever ou não uma sugestão foi paga para uma viagem.

**Learner utilizada:** regressão logística da duas classes

a. Para este problema, nossa etiqueta de destino (ou classe) é "tipped". Nosso conjunto de dados original amostragem em baixo tem algumas colunas que são fugas de destino para esta fase experimental classificação. Em particular: Sugestão\_classe, sugestão\_quantidade e o total\_culmina revelam informações sobre a etiqueta de destino que não está disponível em tempo de teste. Remover a estas colunas consideração utilizando o [selecionar colunas no conjunto de dados] [ select-columns] módulo.

O instantâneo abaixo mostra a nossa experiência para prever ou não uma sugestão foi paga para uma determinado viagem.

![Instantâneo de experimentação](./media/hive-walkthrough/QGxRz5A.png)

b. Para esta fase experimental, nosso distribuições de etiqueta de destino foram aproximadamente 1:1.

O instantâneo abaixo mostra a distribuição de sugestão etiquetas de classe para o problema de classificação binária.

![Distribuição das etiquetas de classe de sugestão](./media/hive-walkthrough/9mM4jlD.png)

Como resultado, obtemos um AUC de 0.987 conforme mostrado na imagem abaixo.

![Valor AUC](./media/hive-walkthrough/8JDT0F8.png)

**2. Classificação de várias classes**: prever o intervalo de quantidades de sugestão paga para viagem, utilizando as classes definidas anteriormente.

**Learner utilizada:** regressão logística da várias classes

a. Para este problema, é a nossa etiqueta de destino (ou classe) "sugestão\_classe" que pode efetuar uma das cinco valores (0,1,2,3,4). Como no caso de classificação binária, temos algumas colunas que são fugas de destino para esta fase experimental. Em particular: tipped, sugestão\_quantidade, total\_culmina revelam informações sobre a etiqueta de destino que não está disponível em tempo de teste. Removemos estas colunas utilizando o [selecionar colunas no conjunto de dados] [ select-columns] módulo.

O instantâneo abaixo mostra a nossa experiência para prever no qual bin é provável que enquadra-se uma sugestão (classe 0: sugestão = $0, 1 de classe: Sugestão > $0 e sugestão < = $5, classe 2: Sugestão > $5 e sugestão < = $10, 3 da classe: Sugestão > $10 e sugestão < = $20 Classe 4: Sugestão > $20)

![Instantâneo de experimentação](./media/hive-walkthrough/5ztv0n0.png)

Agora vamos mostrar aspeto distribuição de classe nosso teste real. Vemos que, enquanto classe 0 e 1 de classe são prevalente atualmente, as outras classes são raras.

![Distribuição de classe de teste](./media/hive-walkthrough/Vy1FUKa.png)

b. Para esta fase experimental, utilizamos uma matriz de confusão para observar os nossos accuracies de predição. Isto é mostrado abaixo.

![Matriz de confusão](./media/hive-walkthrough/cxFmErM.png)

Tenha em atenção que ao nosso accuracies de classe em classes de prevalente atualmente são bastante boas, o modelo não fazer um bom trabalho de "learning" nas classes rarer.

**3. Tarefa de regressão**: para prever a quantidade de sugestão paga para uma viagem.

**Learner utilizada:** Boosted árvore de decisões

a. Para este problema, é a nossa etiqueta de destino (ou classe) "sugestão\_quantidade". Neste caso são nosso fugas de destino: tipped, sugestão\_classe, total\_quantidade; todas estas variáveis revelar informações sobre a quantidade de sugestão está normalmente disponível em tempo de teste. Removemos estas colunas utilizando o [selecionar colunas no conjunto de dados] [ select-columns] módulo.

O instantâneo belows mostra nosso experimentação para prever a quantidade da sugestão indicada.

![Instantâneo de experimentação](./media/hive-walkthrough/11TZWgV.png)

b. Para problemas de regressão, iremos medir accuracies do nosso predição observando o erros no serão as predições, o coeficiente de determinação e o tipo. Vamos mostrar estes abaixo.

![Estatísticas de predição](./media/hive-walkthrough/Jat9mrz.png)

Vemos que sobre o coeficiente de determinação é 0.709, implying cerca de 71% da variância é explicado por nossa coefficients de modelo.

> [!IMPORTANT]
> Para saber mais sobre o Azure Machine Learning e como aceder e utilizá-lo, consulte [o que é o Machine Learning](../studio/what-is-machine-learning.md). Um recurso muito útil para reproduzir com um bunch de experimentações de Machine Learning no Azure Machine Learning é o [galeria da Cortana Intelligence](https://gallery.cortanaintelligence.com/). A Galeria abrange uma gama de experimentações e fornece uma introdução de detalhado para o leque de funcionalidades do Azure Machine Learning.
> 
> 

## <a name="license-information"></a>Informações de licença
Estas instruções de exemplo e os respetivos scripts associadas são partilhados com a licença MIT pela Microsoft. Verifique o ficheiro de LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

## <a name="references"></a>Referências
• [Andrés Monroy NYC Taxi viagens página de transferência](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxi viagem dados por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi e Limousine Commission investigação e estatísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
