---
title: "Dimensionável ciência de dados com o Azure Data Lake: uma instruções ponto a ponto | Microsoft Docs"
description: "Como utilizar o Azure Data Lake para efetuar tarefas de classificação de exploração e binary dados num conjunto de dados."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 91a8207f-1e57-4570-b7fc-7c5fa858ffeb
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev; weig
ms.openlocfilehash: b18b454d1fcdfb2b6e8ea77508f779aeabdc87a0
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Dimensionável ciência de dados com o Azure Data Lake: uma instruções ponto a ponto
Estas instruções mostram como utilizar o Azure Data Lake exploração de dados e tarefas de classificação binária de uma amostra de viagem de taxi NYC e fare conjunto de dados para prever ou não uma sugestão é paga por um fare. -Explica os passos para o [o processo de ciência de dados de equipa](http://aka.ms/datascienceprocess)ponto-a- ponto, a partir da aquisição de dados para o modelo de formação e, em seguida, para a implementação de um serviço web que publica o modelo.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
O [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) tem todas as funcionalidades necessárias para facilitar a cientistas de dados para armazenar dados de qualquer tamanho, a forma e a velocidade e para realizar o processamento de dados, análise e aprendizagem de modelação com elevada escalabilidade de forma económica avançadas.   Paga numa base por tarefa, apenas quando os dados, na verdade, está a ser processados. Azure Data Lake Analytics inclui U-SQL, uma linguagem que mistura a natureza declarativa do SQL Server com o poder expressivo do c# para fornecer escalável distribuído a capacidade de consulta. Permite-lhe processar dados não estruturados aplicando esquema na leitura, inserir lógica personalizada e funções definidas pelo utilizador (UDFs) e inclui a extensibilidade para permitir o controlo detalhado sobre como executar à escala. Para saber mais sobre o philosophy design atrás U-SQL, consulte o artigo [mensagem de blogue do Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

O Data Lake Analytics também é uma parte essencial do Cortana Analytics Suite e funciona com o Azure SQL Data Warehouse, o Power BI e o Data Factory. Isto dá-lhe uma plataforma de análise avançada e macrodados completa de nuvem.

Estas instruções começa por que descrevem como instalar os pré-requisitos e os recursos que são necessárias para concluir tarefas do processo de ciência de dados. -Descreve os passos de processamento de dados, utilizando U-SQL e é concluída ao que mostra como utilizar o Python e o Hive com o Azure Machine Learning Studio para criar e implementar os modelos preditivos. 

### <a name="u-sql-and-visual-studio"></a>U-SQL e o Visual Studio
Estas instruções recomenda com o Visual Studio para editar scripts U-SQL para processar o conjunto de dados. Os scripts U-SQL são descritos aqui e fornecidos num ficheiro separado. O processo inclui ingestão relacionadas, explorar e fazendo a amostragem de dados. Também mostra como executar uma tarefa de criar um script U-SQL a partir do portal do Azure. As tabelas do Hive são criadas para os dados de um cluster de HDInsight associado para facilitar a criação e implementação de um modelo de classificação binária no Azure Machine Learning Studio.  

### <a name="python"></a>Python
Estas instruções também contém uma secção que mostra como criar e implementar um modelo preditivo com o Python no Azure Machine Learning Studio. Fornece um bloco de notas do Jupyter com os scripts do Python para obter os passos neste processo. O bloco de notas inclui código para algumas funcionalidades adicionais engenharia passos e modelos de construção como a classificação de várias classes e regressão modelação além do modelo de classificação binária aqui descrito. A tarefa de regressão é prever a quantidade de sugestão com base nas outras funcionalidades de sugestão. 

### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning Studio é utilizado para criar e implementar os modelos preditivos. Isto é feito utilizando duas abordagens: primeiro com Python scripts e, em seguida, com as tabelas do Hive num cluster do HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
Apenas os principais passos descritos nesta explicação passo a passo. Pode transferir o completo **script U-SQL** e **bloco de notas do Jupyter** de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar estes tópicos, tem de ter o seguinte:

* Uma subscrição do Azure. Se já tiver uma, consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Recomendado] Visual Studio 2013 ou posterior. Se já tiver um estas versões instaladas, pode transferir uma versão de Comunidade gratuita de [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Em vez do Visual Studio, também pode utilizar o portal do Azure para submeter consultas do Azure Data Lake. São fornecidas instruções sobre como efetuar, por isso, ambos com o Visual Studio e no portal na secção intitulada **processar os dados com o U-SQL**. 
> 
> 


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Preparar o ambiente de ciência de dados para o Azure Data Lake
Para preparar o ambiente de ciência de dados para esta explicação passo a passo, crie os seguintes recursos:

* O Azure Data Lake Store (ADLS) 
* Análise do Azure Data Lake (ADLA)
* Conta de armazenamento de Blobs do Azure
* Conta do Azure Machine Learning Studio
* Ferramentas do Azure Data Lake para Visual Studio (recomendado)

Esta secção fornece instruções sobre como criar cada um destes recursos. Se optar por utilizar as tabelas do Hive com o Azure Machine Learning, em vez de Python, para criar um modelo, também terá de aprovisionar um cluster de HDInsight (Hadoop). Este procedimento alternativo descrito na secção 2 opção.


> [!NOTE]
> O **Azure Data Lake Store** pode ser criado em separado ou ao criar o **Azure Data Lake Analytics** como armazenamento de predefinido. São referenciadas instruções para criar cada um destes recursos em separado, mas a conta de armazenamento do Data Lake não precisa de ser criada em separado.
>
> 

### <a name="create-an-azure-data-lake-store"></a>Criar um Azure Data Lake Store


Criar um ADLS do [portal do Azure](http://portal.azure.com). Para obter mais informações, consulte [criar um cluster do HDInsight com o Data Lake Store utilizando o portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se configurar a identidade do AAD de Cluster no **DataSource** painel do **configuração opcional** painel descrito não existe. 

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Criar uma conta do Azure Data Lake Analytics
Criar uma conta de ADLA do [portal do Azure](http://portal.azure.com). Para obter mais informações, consulte [Tutorial: introdução ao Azure Data Lake Analytics com o portal do Azure](../../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de Blobs do Azure
Criar uma conta de armazenamento de Blobs do Azure do [portal do Azure](http://portal.azure.com). Para obter detalhes, consulte a criar uma secção de conta de armazenamento no [contas do storage do Azure sobre](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Configure uma conta do Azure Machine Learning Studio
Inicie a cópia de segurança/no Azure Machine Learning Studio do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) página. Clique em de **começar agora** botão e, em seguida, escolha um "Área de trabalho gratuita" ou "Área de trabalho Standard". Agora a estão prontos para criar experimentações no Azure ML Studio.  

### <a name="install-azure-data-lake-tools-recommended"></a>Instale o ferramentas do Azure Data Lake [Recomendado]
Instalar o Azure Data Lake Tools para a sua versão do Visual Studio do [ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Após a conclusão da instalação com êxito, abra a cópia de segurança Visual Studio. Deverá ver o separador menu na parte superior do Data Lake. Os recursos do Azure devem aparecer no painel esquerdo, ao iniciar sessão na sua conta do Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>O conjunto de dados NYC Taxi viagens
O conjunto de dados utilizado aqui é um conjunto de dados publicamente disponível – o [NYC Taxi viagens dataset](http://www.andresmh.com/nyctaxitrips/). Os dados de NYC Taxi viagem constituem em cerca de 20 GB de ficheiros CSV comprimidos (GB de ~ 48 descomprimido), gravar viagens mais de 173 milhões de individuais e os fares paga para cada viagem. Cada registo viagem inclui as localizações de recolha e drop-off e vezes, o número de licença de acesso anónimos (controlador) e o número de medallion (ID exclusivo do taxi). Os dados abrange todos os viagens no ano 2013 e são fornecidos nos seguintes dois conjuntos de dados de cada mês:

O 'trip_data CSV contém detalhes viagem, tais como o número de passageiros, recolha e dropoff pontos, duração de viagem e comprimento viagem. Seguem-se alguns registos de exemplo:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



O trip_fare CSV contém detalhes de fare paga para cada viagem, tais como o tipo de pagamento, a quantidade de fare, surcharge e, sugestões e impostos tolls e a quantidade total paga. Seguem-se alguns registos de exemplo:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para associar a viagem\_dados e viagem\_fare é composto por três campos seguintes: medallion, acesso\_licença e recolha\_datetime. Os ficheiros CSV em bruto podem ser acedidos a partir de um blob de armazenamento do Azure público. O script U-SQL para esta associação é no [associar tabelas viagem e fare](#join) secção.

## <a name="process-data-with-u-sql"></a>Processar os dados com o U-SQL
As tarefas de processamento de dados ilustradas nesta secção incluem ingestão relacionadas, a verificação de qualidade, a explorar e os dados de amostragem. Como associar as tabelas viagem e fare também é apresentado. A secção final mostra executar uma tarefa de script U-SQL do portal do Azure. Seguem-se ligações para cada subsecção:

* [Ingestão de dados: ler dados a partir do blob público](#ingest)
* [Verificações de qualidade de dados](#quality)
* [Exploração de dados](#explore)
* [Associar viagem e fare tabelas](#join)
* [Amostragem de dados](#sample)
* [Executar tarefas U-SQL](#run)

Os scripts U-SQL são descritos aqui e fornecidos num ficheiro separado. Pode transferir o completo **scripts U-SQL** de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Para executar o U-SQL, abra Visual Studio, clique em **ficheiro--> novo--> projeto**, escolha **projeto U-SQL**, nomes e guarde-o para uma pasta.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> É possível utilizar o Portal do Azure para executar o U-SQL em vez do Visual Studio. Pode navegar para o recurso no portal do Azure Data Lake Analytics e submeter consultas diretamente como ilustradas na figura seguinte:
> 
> 

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Ingestão de dados: Ler dados a partir do blob público
A localização dos dados no blob do Azure é referenciada como  **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**  e pode ser extraída utilizando **Extractors.Csv()**. Substitua o seu nome de contentor e um nome de conta do storage em scripts seguintes para container_name@blob_storage_account_name no endereço wasb. Uma vez que os nomes de ficheiro no mesmo formato, é possível utilizar **viagem\_data_ {\*\}. csv** ler em todos os ficheiros de 12 viagem. 

    ///Read in Trip data
    @trip0 =
        EXTRACT 
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Uma vez que existem cabeçalhos na primeira linha, terá de remover os cabeçalhos e alterar os tipos de coluna para aqueles adequado. É possível guardar os dados processados para a utilização do armazenamento do Azure Data Lake **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ ou a utilização da conta de armazenamento de Blobs do Azure  **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

    // change data types
    @trip =
        SELECT 
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv(); 

    ////Output data to blob
    OUTPUT @trip   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();  

Da mesma forma, pode obter os conjuntos de dados fare. Contexto do Azure Data Lake Store, pode optar por observar os dados no **portal do Azure--> Explorador de dados** ou **Explorador de ficheiros** no Visual Studio. 

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Verificações de qualidade de dados
Depois de tem sido lida viagem e fare tabelas, as verificações de qualidade de dados podem ser efetuadas da seguinte forma. Os ficheiros resultantes de CSV podem ser resultado para o Blob storage do Azure ou do Azure Data Lake Store. 

Encontrar o número de medallions e o número exclusivo de medallions:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month, 
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv(); 

Localize esses medallions que tinha mais do que 100 viagens:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv(); 

Localize os registos inválidos em termos de pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv(); 

Localize os valores em falta para algumas variáveis:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT 
            vendor_id,
        SUM(missing_medallion) AS medallion_empty, 
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique  
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="explore"></a>Exploração de dados
Efetue algumas exploração de dados com os scripts seguintes para obter uma melhor compreensão dos dados.

Localize a distribuição viagens tipped e não tipped:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv(); 

Localizar a distribuição da quantidade de sugestão com valores de truncado: 0, 5, 10 e 20 utilizados no compromisso.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv(); 

Localize estatísticas básicas de distância viagem:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT 
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance 
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Localize percentiles de distância viagem:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv(); 


### <a name="join"></a>Associar viagem e fare tabelas
As tabelas viagem e fare podem ser associadas ao medallion, hack_license e pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 

    ////output data to ADL
    OUTPUT @model_data_full   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 


Para cada nível de contagem de passenger, calcule o número de registos, a quantidade de sugestão média, a variância a partir da quantidade de sugestão, percentagem de viagens tipped.

    // contigency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="sample"></a>Amostragem de dados
Em primeiro lugar, selecione aleatoriamente 0.1% dos dados da tabela associada:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv(); 

Em seguida, fazer a amostragem stratified binário tip_class variável:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 


### <a name="run"></a>Executar tarefas U-SQL
Quando concluir a edição scripts U-SQL, pode submetê-las para o servidor com a sua conta do Azure Data Lake Analytics. Clique em **Data Lake**, **submeter tarefa**, selecione o **conta Analytics**, escolha **paralelismo**e clique em **submeter** botão.  

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Quando a tarefa é complied com êxito, o estado da tarefa é apresentado no Visual Studio para monitorização. Após a tarefa em execução, pode ainda o processo de execução da tarefa de repetição e saber os passos de congestionamento para melhorar a eficiência da tarefa. Também pode aceder ao portal do Azure para verificar o estado das suas tarefas U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Agora pode verificar os ficheiros de saída no Blob storage do Azure ou no portal do Azure. Utilize os dados de exemplo stratified para a nossa modelação no próximo passo.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Criar e implementar modelos no Azure Machine Learning
Duas opções estão disponíveis extrair dados para o Azure Machine Learning para criar e 

* A primeira opção, utiliza as amostras de dados que foi escritos para um Blob do Azure (no **amostragem de dados** passo acima) e utilizar o Python para criar e implementar modelos do Azure Machine Learning. 
* Na segunda opção, consultar os dados no Azure Data Lake diretamente utilizando uma consulta do Hive. Esta opção exige que crie um novo cluster de HDInsight ou utilize um cluster do HDInsight existente onde as tabelas do Hive apontam para os dados de NY Taxi no armazenamento do Azure Data Lake.  Ambas estas opções são abordadas nas secções seguintes. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opção 1: Python utilizado para criar e implementar machine learning modelos
Para criar e implementar modelos de machine learning com o Python, crie um bloco de notas do Jupyter no seu computador local ou no Azure Machine Learning Studio. O bloco de notas do Jupyter fornecido no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) contém o código completo para explorar, visualizar dados, engenharia da funcionalidade, modelação e a implementação. São abrangidos neste artigo, apenas a modelação e a implementação. 

### <a name="import-python-libraries"></a>Importar bibliotecas de Python
Para executar o exemplo de notas do Jupyter ou o Python ficheiro de script, o Python seguintes pacotes são necessários. Se estiver a utilizar o serviço de bloco de notas do AzureML, estes pacotes tem sido previamente instalados.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Ler dados a partir do blob
* Cadeia de Ligação   
  
        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Lida como texto
  
        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  
  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)    
* Adicionar nomes de coluna e separe colunas
  
        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Alterar algumas colunas para numérico
  
        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Criar modelos de machine learning
Aqui pode cria um modelo de classificação binária para prever se um viagem é tipped ou não. O bloco de notas do Jupyter pode encontrar outros dois modelos: classificação várias classes e modelos de regressão.

* Primeiro tem de criar variáveis fictícias que podem ser utilizadas no scikit-saiba modelos
  
        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Criar a frame de dados para a modelação
  
        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
  
        X = data.iloc[:,1:]
        Y = data.tipped
* Formação e testar 60 40 dividido
  
        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Regressão logística no conjunto de preparação
  
        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)
  
       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Pontuar o conjunto de dados de teste
  
        Y_test_pred = logit_fit.predict(X_test)
* Calcular as métricas de avaliação
  
        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train
  
        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
        print fpr_test, tpr_test, thresholds_test
  
        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)
  
        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)
  
       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Criar API Web do serviço e consumi-lo no Python
Pretende operacionalizar o modelo de machine learning depois de ter sido concebido. O modelo de logística da binário é utilizado aqui como exemplo. Certifique-se de que o scikit-saber versão no seu computador local é 0.15.1. Não tem de preocupar com esta se utilizar o serviço do Azure ML studio.

* Localize as credenciais da sua área de trabalho do Azure ML studio de definições. No Azure Machine Learning Studio, clique em **definições** --> **nome** --> **Tokens de autorização**. 
  
    ![C3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Criar o serviço Web
  
        @services.publish(workspaceid, auth_token) 
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Obter credenciais do serviço web
  
        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
  
        print url
        print api_key
  
        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Chame a API do serviço Web. Tem de aguardar 5-10 segundos após o passo anterior.
  
        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  
       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opção 2: Criar e implementar modelos diretamente no Azure Machine Learning
Azure Machine Learning Studio pode ler os dados diretamente a partir do Azure Data Lake Store e, em seguida, ser utilizado para criar e implementar modelos. Esta abordagem utiliza uma tabela de Hive que aponta no Azure Data Lake Store. Isto requer que um cluster separado do Azure HDInsight ser aprovisionados, no qual a tabela de Hive é criada. As secções seguintes mostram como fazê-lo. 

### <a name="create-an-hdinsight-linux-cluster"></a>Criar um Cluster do HDInsight com Linux
Criar um Cluster de HDInsight (Linux) a partir de [portal do Azure](http://portal.azure.com). Para obter mais informações, consulte o **crie um cluster do HDInsight com acesso ao Azure Data Lake Store** secção [criar um cluster do HDInsight com o Data Lake Store utilizando o portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Criar tabela do Hive no HDInsight
Agora pode criar as tabelas do Hive a ser utilizado no Azure Machine Learning Studio no cluster do HDInsight com os dados armazenados no Azure Data Lake Store no passo anterior. Vá para o cluster de HDInsight criada. Clique em **definições** --> **propriedades** --> **identidade do AAD Cluster** --> **acesso ADLS**, certifique-se a sua conta do Azure Data Lake Store é adicionada na lista com leitura, escrita e direitos de execução. 

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Em seguida, clique em **Dashboard** junto a **definições** botão e uma janela aparece. Clique em **vista do Hive** no canto superior direito da página e deverá ver o **Editor de consultas**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Colar os scripts de ramo de registo seguintes para criar uma tabela. A localização da origem de dados está numa referência de Azure Data Lake Store desta forma: **adl://data_lake_store_name.azuredatalakestore.net:443/nome_da_pasta/file_name**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Quando a consulta estiver concluída em execução, deverá ver os resultados da seguinte forma:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Criar e implementar modelos no Azure Machine Learning Studio
Agora está pronto para criar e implementar um modelo que prevê ou não uma sugestão é paga com o Azure Machine Learning. Os dados de exemplo stratified estão prontos para ser utilizado nesta classificação binária (sugestão ou não) problema. Modelos preditivos através de várias classes classificação (tip_class) e regressão (tip_amount) também podem ser criados e implementados com o Azure Machine Learning Studio, mas aqui apenas é apresentado como lidar com as maiúsculas e minúsculas utilizando o modelo de classificação binária.

1. Obter os dados do Azure ML utilizando o **importar dados** módulo, disponível no **dados de entrada e saída** secção. Para obter mais informações, consulte o [módulo importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) página de referência.
2. Selecione **consulta do Hive** como o **origem de dados** no **propriedades** painel.
3. Cole o seguinte script de ramo de registo no **consulta de base de dados do Hive** editor
   
        select * from nyc_stratified_sample;
4. Introduza o URI do HDInsight cluster (Isto pode ser foi encontrado no portal do Azure), as credenciais do Hadoop, localização de dados de saída e o nome de nome / / contentor da chave de conta do storage do Azure.
   
   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)  

Um exemplo de uma leitura de experimentação de classificação binária dados a partir da tabela do Hive são mostrados na figura seguinte:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Depois de criada a experimentação, clique em **segurança serviço Web** --> **preditiva serviço Web**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Executar automaticamente criado da classificação experimentação, quando terminar, clique em **implementar serviço Web**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Dashboard do serviço web apresenta em breve:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Resumo
Por concluir estas instruções criou um ambiente de ciência de dados para criar soluções de ponto a ponto dimensionáveis no Azure Data Lake. Este ambiente foi utilizado para analisar um conjunto de dados público grande, colocá-lo através dos passos canónicos do processo de ciência de dados, de aquisição de dados através de formação do modelo e, em seguida, para a implementação do modelo como um serviço web. U-SQL foi utilizado para processar, explore e os dados de exemplo. Foram utilizadas Python e o Hive com o Azure Machine Learning Studio para criar e implementar modelos preditivos.

## <a name="whats-next"></a>Passos seguintes?
O percurso de aprendizagem para o [processo de ciência de dados de equipa (TDSP)](http://aka.ms/datascienceprocess) fornece ligações para tópicos que descrevem cada passo no processo de análise avançadas. Há uma série de instruções descritas no [instruções do processo de ciência de dados de equipa](walkthroughs.md) página que demonstram como utilizar os serviços e recursos em vários cenários de Análise Preditiva:

* [O processo de ciência de dados de equipa em ação: utilizar o SQL Data Warehouse](sqldw-walkthrough.md)
* [O processo de ciência de dados de equipa em ação: com clusters do HDInsight Hadoop](hive-walkthrough.md)
* [O processo de ciência de dados do agrupamento: utilizar o SQL Server](sql-walkthrough.md)
* [Descrição geral do processo de ciência de dados com o Spark no Azure HDInsight](spark-overview.md)
