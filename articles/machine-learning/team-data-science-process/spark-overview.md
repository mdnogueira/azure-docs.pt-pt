---
title: "Descrição geral de ciência de dados com o Spark no Azure HDInsight | Microsoft Docs"
description: "O toolkit de Spark MLlib coloca considerável de aprendizagem modelação capacidades para o ambiente distribuído do HDInsight."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a4e1de99-a554-4240-9647-2c6d669593c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
ms.openlocfilehash: be3bbad11632f3af257239f6b10b1b22951073f7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Descrição geral de ciência de dados com o Spark no Azure HDInsight
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Este conjunto de tópicos mostra como utilizar o Spark do HDInsight para realizar tarefas comuns de ciência de dados, tais como a ingestão de dados, funcionalidade engenharia, modelação e avaliação de modelo. Os dados utilizados são uma amostra de 2013 NYC taxi viagem e fare conjunto de dados. Os modelos criados incluem regressão linear e logística da, florestas aleatórias e gradação árvores elevadas. Os tópicos também mostram como armazenar estes modelos no blob storage do Azure (WASB) e como Pontuar e avaliar o seu desempenho preditivo. Tópicos mais avançados abrangem a forma como os modelos podem ser preparado varrimento de validação cruzada e hyper parâmetro a utilizar. Este tópico de descrição geral referencia também os tópicos que descrevem como configurar o cluster Spark que precisa para concluir os passos nas instruções fornecidas. 

## <a name="spark-and-mllib"></a>O Spark e o MLlib
[O Spark](http://spark.apache.org/) está a processar de uma arquitetura de processamento paralelo open source que suporta dentro da memória para melhorar o desempenho de aplicações de análise de macrodados. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e efetuar análises sofisticadas. Capacidades de computação distribuída na memória do Spark tornam uma boa opção para algoritmos iterativos utilizado no computações de machine learning e do graph. [MLlib](http://spark.apache.org/mllib/) é dimensionável de machine learning biblioteca do Spark que coloca o usam modelação capacidades para este ambiente distribuído. 

## <a name="hdinsight-spark"></a>Spark do HDInsight
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) é a oferta alojada do Azure do Spark open source. Também inclui suporte para **blocos de notas do Jupyter PySpark** no cluster Spark que pode executar consultas interativas do Spark SQL para transformar, filtragem e visualizar dados armazenados em Blobs do Azure (WASB). PySpark é a API do Python para Spark. Os fragmentos de código que fornecem as soluções e mostram rastreia relevante para visualizar os dados aqui executados em blocos de notas do Jupyter instalados nos clusters do Spark. Os passos de modelação nos seguintes tópicos contenham código que mostra como preparar, avaliar, guardar e consumir cada tipo de modelo. 

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Configuração: Os clusters do Spark e blocos de notas do Jupyter
Passos de configuração e de código são fornecidos esta explicação passo a passo para utilizar um 1.6 Spark do HDInsight. Mas blocos de notas do Jupyter são fornecidos para os clusters do HDInsight Spark 1.6 e 2.0 do Spark. Uma descrição dos blocos de notas e ligações para os mesmos são fornecidos no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório do GitHub que contém-los. Além disso, o código aqui e em blocos de notas ligados é genérico e deve trabalhar em qualquer cluster do Spark. Se não estiver a utilizar o Spark do HDInsight, os passos de configuração e gestão de cluster podem ser ligeiramente diferentes das que é mostrado aqui. Para sua comodidade, seguem-se as ligações de blocos de notas do Jupyter para 1.6 Spark (para ser executada no kernel do pySpark do servidor de notas do Jupyter) e 2.0 do Spark (para ser executada na kernel pySpark3 do servidor de notas do Jupyter):

### <a name="spark-16-notebooks"></a>Blocos de notas do Spark 1.6
Estes blocos de notas estão a ser executados no kernel do pySpark do servidor de bloco de notas do Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): fornece informações sobre como efetuar a exploração de dados, modelação e a classificação com vários algoritmos diferentes.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): inclui os tópicos no bloco de notas #1 e desenvolvimento de modelo com otimização de hyperparameter e validação cruzada.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): mostra como operacionalizar um modelo guardado com o Python nos clusters do HDInsight.

### <a name="spark-20-notebooks"></a>Blocos de notas do Spark 2.0
Estes blocos de notas estão a ser executados do kernel pySpark3 do servidor de bloco de notas do Jupyter.

- [Spark2.0-pySpark3-Machine-Learning-data-Science-spark-Advanced-Data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): este ficheiro fornece informações sobre como efetuar a exploração de dados, modelação de e classificação no Spark 2.0 clusters com a viagem NYC Taxi e fare-conjunto de dados descrito [aqui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Este bloco de notas pode ser um ponto de partida para explorar rapidamente o código que forneceu para Spark 2.0. Para um bloco de notas mais detalhado analisa os dados de NYC Taxi, consulte o seguinte bloco de notas nesta lista. Consulte as notas desta lista a seguir e comparam estes blocos de notas. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): este ficheiro mostra como efetuar dados wrangling (operações de Spark SQL e dataframe), exploração, modelação e a classificação utilizando a viagem NYC Taxi e fare conjunto de dados descritos [aqui ](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): este ficheiro mostra como efetuar dados wrangling (operações de Spark SQL e dataframe), exploração, modelação e a classificação utilizando o distanciamento de tempo companhia aérea bem conhecido conjunto de dados de 2011 e 2012. É integrado o conjunto de dados companhia aérea com os dados de Meteorologia airport (por exemplo, windspeed temperatura, altitude etc.) antes da modelação de para que estas funcionalidades Meteorologia podem ser incluídas no modelo.

<!-- -->

> [!NOTE]
> O conjunto de dados companhia aérea foi adicionado a blocos de notas do Spark 2.0 para ilustrar melhor a utilização de algoritmos de classificação. Consulte as seguintes ligações para informações sobre companhia aérea em tempo de conjunto de dados de partida e meteorologia conjunto de dados:

>- Dados de partida de tempo companhia aérea: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Dados de Meteorologia AIRport: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Os blocos de notas do Spark 2.0 os NYC taxi e companhia aérea voo atraso-conjuntos de dados podem demorar 10 minutos ou mais para ser executada (dependendo do tamanho do seu cluster HDI). O bloco de notas primeiro na lista acima mostra muitos aspetos da exploração de dados, formação num bloco de notas demora menos tempo para executar com o conjunto de dados do NYC amostragem em baixo, na qual os ficheiros taxi e fare tem sido previamente associado a um modelo de visualização e ML: [ Spark2.0-pySpark3-Machine-Learning-data-Science-spark-Advanced-Data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) este bloco de notas assume um tempo muito mais curto (2 a 3 minutos) e pode ser um bom ponto de partida para explorar rapidamente o código fornecemos para o Spark 2.0. 

<!-- -->

Para obter orientações sobre a operationalization de um modelo de Spark 2.0 e o consumo de modelo de classificação, consulte o [Spark 1.6 documento no consumo](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) para obter um exemplo definido que estipule os passos necessários. Para utilizar isto no Spark 2.0, substitua o ficheiro de código do Python com [este ficheiro](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Pré-requisitos
Os procedimentos seguintes relacionados com o Spark 1.6. Para a versão 2.0 do Spark, utilizam blocos de notas descrito e ligada ao anteriormente. 

1. tem de ter uma subscrição do Azure. Se já tiver uma, consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2 precisa de um cluster do Spark 1.6 para concluir estas instruções. Para criar um, consulte as instruções fornecidas [introdução: criar o Apache Spark no Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). O tipo de cluster e a versão é especificado do **selecionar tipo de Cluster** menu. 

![Configurar o cluster](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Para um tópico mostra como utilizar Scala em vez do Python para concluir as tarefas de um processo de ciência de dados ponto-a-ponto, consulte o [ciência de dados com o Scala com o Spark no Azure](scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>Os dados de NYC Taxi de 2013
Os dados de NYC Taxi viagem cerca de 20 GB de ficheiros comprimidos valores separados por vírgulas (CSV) (GB de ~ 48 descomprimido), que inclui mais de 173 milhões de viagens individuais e os fares paga para cada viagem. Cada registo viagem inclui a escolha de cópia de segurança e localização drop-off e a hora, acesso anónimos (controlador) número de licença e número de medallion (id exclusivo do taxi). Os dados abrange todos os viagens no ano 2013 e são fornecidos nos seguintes dois conjuntos de dados de cada mês:

1. Os ficheiros CSV 'trip_data' contém detalhes viagem, tais como o número de passageiros, recolher e viagem dropoff pontos, duração e o comprimento de viagem. Seguem-se alguns registos de exemplo:
   
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

Iremos ter decorrido um exemplo de 0.1% destes ficheiros e associados a viagem\_dados e viagem\_fare ficheiros CSV para um único conjunto de dados a utilizar como o conjunto de dados de entrada para estas instruções. A chave exclusiva para associar a viagem\_dados e viagem\_fare é composto por campos: medallion, acesso\_licenciamento e a recolha\_datetime. Cada registo do conjunto de dados contém os seguintes atributos que representa uma viagem NYC Taxi:

| Campo | Breve descrição |
| --- | --- |
| Medallion |Medallion taxi anónimos (id exclusivo taxi) |
| hack_license |Número de licença de avanço Hackney anónimos |
| vendor_id |Id do fornecedor Taxi |
| rate_code |Taxa de taxi NYC de fare |
| store_and_fwd_flag |Arquivo e sinalizador reencaminhar |
| pickup_datetime |Escolha se a data e hora |
| dropoff_datetime |Dropoff data e hora |
| pickup_hour |Recolherá hora |
| pickup_week |Escolha se a semana do ano |
| dia da semana |Dia da semana (intervalo de 1 a 7) |
| passenger_count |Número de passageiros em viagem um taxi |
| trip_time_in_secs |Tempo de viagem em segundos |
| trip_distance |Percorrer em quilómetros de distância de viagem |
| pickup_longitude |Recolherá longitude |
| pickup_latitude |Recolherá latitude |
| dropoff_longitude |Longitude Dropoff |
| dropoff_latitude |Latitude Dropoff |
| direct_distance |Direcionar a distância entre escolha cópias de segurança e dropoff localizações |
| payment_type |Tipo de pagamento (ACs, cartão de crédito etc.) |
| fare_amount |Quantidade de fare no |
| Surcharge |Surcharge |
| mta_tax |Impostos MTA |
| tip_amount |Quantidade de sugestão |
| tolls_amount |Quantidade de tolls |
| total_amount |Quantidade total |
| tipped |Tipped (0/1 para não ou Sim) |
| tip_class |Sugestão classe (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Executar código a partir de um bloco de notas do Jupyter no cluster do Spark
Pode iniciar o bloco de notas do Jupyter do portal do Azure. Localizar o cluster do Spark no seu dashboard e clique nele para introduzir a página de gestão para o cluster. Para abrir o bloco de notas associado ao cluster do Spark, clique em **Dashboards de clusters** -> **bloco de notas do Jupyter** .

![Dashboards de clusters](./media/spark-overview/spark-jupyter-on-portal.png)

Também pode navegar para ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** para aceder a blocos de notas do Jupyter. Substitua o nome do seu próprio cluster a parte CLUSTERNAME deste URL. Terá da palavra-passe para a sua conta de administrador aceder os blocos de notas.

![Procurar blocos de notas do Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Selecione PySpark para ver um diretório que contém alguns exemplos de blocos de notas pré-empacotadas que utilizam a API do PySpark. Os blocos de notas que contêm os exemplos de código para este conjunto de tópico de Spark estão disponíveis em [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Pode carregar os blocos de notas diretamente a partir do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) para o servidor de bloco de notas do Jupyter no cluster do Spark. Na página inicial do seu Jupyter, clique em de **carregar** botão na parte direita do ecrã. É aberto um Explorador de ficheiros. Aqui pode colar o URL de GitHub (conteúdo em bruto) do bloco de notas e clique em **abra**. 

Ver o nome de ficheiro na sua lista de ficheiros do Jupyter com um **carregar** botão novamente. Clique em isto **carregar** botão. Agora que importou o bloco de notas. Repita estes passos para carregar os outros blocos de notas destas instruções.

> [!TIP]
> Pode faça duplo clique nas hiperligações no seu browser e selecione **copiar ligação** para obter o URL de conteúdo não processados do github. Pode colar este URL na caixa de diálogo Explorador de ficheiros Jupyter carregar.
> 
> 

Agora, pode:

* Consulte o código clicando o bloco de notas.
* executar cada célula premindo **SHIFT-INTRODUZA**.
* Executar o bloco de notas completo clicando no **célula** -> **executar**.
* Utilize a visualização automática de consultas.

> [!TIP]
> O kernel do PySpark automaticamente visualiza o resultado das consultas SQL (HiveQL). É-lhe dada a opção para selecionar entre os vários tipos diferentes de visualizações (tabela, circular, linha, área ou barra) utilizando o **tipo** botões de menu no bloco de notas:
> 
> 

![Curva ROC de regressão logística da abordagem genérico](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Passos seguintes?
Agora que já está configurados com um cluster do HDInsight Spark e carregou os blocos de notas do Jupyter, está pronto para completar os tópicos que correspondem a três blocos de notas do PySpark. Estes mostram como explore os seus dados e, em seguida, como criar e consumir modelos. O bloco de notas do exploração e modelação de dados avançadas mostra como a incluir validação cruzada, hyper varrimento, parâmetro e modelo de avaliação. 

**Exploração de dados e modelação com o Spark:** explorar o conjunto de dados e criar, Pontuar e avaliar os modelos de machine learning ao trabalhar com o [criar modelos de classificação e regressão binários de dados com o Spark MLlib Toolkit](spark-data-exploration-modeling.md) tópico.

**Consumo de modelo:** para saber como Pontuar os modelos de classificação e regressão criados neste tópico, consulte [classificação e avaliar os modelos de incorporadas do Spark machine learning](spark-model-consumption.md).

**Validação cruzada e hyperparameter varrimento**: consulte [avançada de exploração de dados e modelação com o Spark](spark-advanced-data-exploration-modeling.md) na forma como os modelos podem ser preparado varrimento de validação cruzada e hyper parâmetro a utilizar

