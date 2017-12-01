---
title: "O processo de ciência de dados de equipa em ação - um Cluster de Hadoop do Azure HDInsight a utilizar um conjunto de dados de 1 TB | Microsoft Docs"
description: "O processo de ciência de dados do agrupamento a utilizar para um cenário ponto-a-ponto, a utilização de um cluster de Hadoop do HDInsight para criar e implementar um modelo com um conjunto de dados publicamente disponível grande do (1 TB)"
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 72d958c4-3205-49b9-ad82-47998d400d2b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev
ms.openlocfilehash: 760e08643fb3e71478fc899278591569da1d515b
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>O processo de ciência de dados de equipa em ação - um Cluster de Hadoop do Azure HDInsight a utilizar um conjunto de dados de 1 TB

Esta explicação passo a passo demonstra como utilizar o processo de ciência de dados de equipa num cenário de ponto a ponto com um [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explore, engenheiro de funcionalidade e pendente dados de exemplo a partir de um a publicamentedisponíveis[ Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) conjuntos de dados. Utiliza o Azure Machine Learning para criar um modelo de classificação binária estes dados. Também mostra como publicar um destes modelos como um serviço Web.

Também é possível utilizar um bloco de notas IPython para realizar as tarefas apresentadas nestas instruções. Os utilizadores que gostaria de tentar esta abordagem devem consultar a [instruções Criteo utilizando uma ligação de ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) tópico.

## <a name="dataset"></a>Descrição do conjunto de dados Criteo
Criteo dados são um conjunto de dados de predição de clique aproximadamente 370 GB dos ficheiros TSV gzip comprimido (~1.3TB descomprimidos), que inclui mais de mil milhões de 4.3 registos. -É obtida a partir de 24 dias clique dados disponibilizados pelo [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Para a conveniência de cientistas de dados, os dados disponíveis para nós para experimentar foi deszipados.

Cada registo este conjunto de dados contém 40 colunas:

* a primeira coluna é uma coluna de etiqueta que indica se um utilizador clica um **adicionar** (valor 1) ou não clique em um (o valor 0)
* em seguida 13 colunas são numéricas, e
* última 26 são colunas categórico

As colunas são anónimas e utilizar uma série de nomes enumerados: "Col1" (para a coluna de etiqueta) para ' Col40 "(para a última coluna categórico).            

Eis um excerpt das primeiro 20 colunas de duas as observações (linhas) deste conjunto de dados:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Existem valores em falta nas colunas numérico e categórico este conjunto de dados. Um método simples para processar os valores em falta é descrito. Detalhes adicionais dos dados são explorou quando armazene-os para as tabelas do Hive.

**Definição:** *Clickthrough taxa (CTR):* esta é a percentagem de cliques nos dados. Este conjunto de dados do Criteo o CTR é cerca de 3.3% ou 0.033.

## <a name="mltasks"></a>Exemplos de tarefas de predição
Dois problemas de predição de exemplo são tratados esta explicação passo a passo:

1. **Classificação binária**: prevê se um utilizador clica um adicionar:
   
   * Classe de 0: Nenhum clique
   * Classe 1: clique em
2. **Regressão**: prevê a probabilidade de um clique ad das funcionalidades do utilizador.

## <a name="setup"></a>Definir se um cluster do HDInsight Hadoop para ciência de dados
**Nota:** isto é normalmente um **Admin** tarefas.

Configure o ambiente de ciência de dados do Azure para criar soluções de Análise Preditiva com clusters do HDInsight em três passos:

1. [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md): esta conta de armazenamento é utilizada para armazenar dados no Blob Storage do Azure. Os dados utilizados nos clusters do HDInsight são armazenados aqui.
2. [Personalizar Clusters do Hadoop do Azure HDInsight para ciência de dados](customize-hadoop-cluster.md): este passo cria um cluster de Hadoop do Azure HDInsight com 64-bit Anaconda Python 2.7 instalado em todos os nós. Existem dois passos importantes (descritos neste tópico) para concluir ao personalizar o cluster do HDInsight.
   
   * Tem de associar a conta de armazenamento criada no passo 1 com o cluster do HDInsight quando é criado. Esta conta de armazenamento é utilizada para aceder aos dados que podem ser processados no cluster.
   * Tem de ativar o acesso remoto para o nó principal do cluster depois de criado. Lembrar as credenciais de acesso remoto que especificar aqui (diferentes das especificadas para o cluster durante a criação do respetivo): necessário para concluir os procedimentos seguintes.
3. [Criar uma área de trabalho do Azure ML](../studio/create-workspace.md): área de trabalho este Azure Machine Learning é utilizada para a criação de modelos de machine learning após uma exploração de dados inicial e para baixo amostragem no cluster do HDInsight.

## <a name="getdata"></a>Obter e consumir dados a partir de uma fonte públicos
O [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) conjunto de dados pode ser acedido ao clicar na ligação, aceitar os termos de utilização e fornecer um nome. Um instantâneo deste aspeto é mostrado aqui:

![Aceitar os termos de Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Clique em **continuar para transferência** para ler mais sobre o conjunto de dados e a disponibilidade do mesmo.

Os dados residem num público [blob storage do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) localização: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" refere-se a localização de armazenamento de Blobs do Azure. 

1. Os dados neste armazenamento de BLOBs público é constituído por três subpastas dos dados deszipadas.
   
   1. A subpasta *não processados/contagem/* contém os primeiro 21 dias de dados - do dia\_00 ao dia\_20
   2. A subpasta *não processados/formação/* consiste num único dia de dados, dia\_21
   3. A subpasta *não processados/teste/* consiste em dois dias de dados, dia\_22 e dia\_23
2. Para quem pretende começar a utilizar os dados de gzip não processados, estes também estão disponíveis na pasta principal *não processados /* como day_NN.gz, onde NN ficar de 00 e 23.

Uma abordagem alternativa para aceder, explore e o modelo de dados que não requerem quaisquer transferências locais são explicados posteriormente nestas instruções quando criamos as tabelas do Hive.

## <a name="login"></a>Inicie sessão no headnode o cluster
Para iniciar sessão headnode do cluster, utilize o [portal do Azure](https://ms.portal.azure.com) para localizar o cluster. Clique no ícone de elephant HDInsight no lado esquerdo e, em seguida, faça duplo clique o nome do cluster. Navegue para o **configuração** separador, faça duplo clique no ícone de ligação na parte inferior da página e introduza as suas credenciais de acesso remoto quando lhe for pedido. Isto leva-o para o headnode do cluster.

Eis o aspeto um típico sessão pela primeira vez para o cluster headnode:

![Inicie sessão no cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

À esquerda é "Hadoop linha de comandos", que é a nossa workhorse para a exploração de dados. Tenha em atenção dois URLs útil - "Hadoop Yarn Status" e "Hadoop nome de nó". O URL de estado yarn mostra o progresso da tarefa e o URL do nó de nome fornece detalhes sobre a configuração do cluster.

Agora que estão configurados e prontos para começar a primeira parte de instruções: exploração de dados utilizando o Hive e preparar dados do Azure Machine Learning.

## <a name="hive-db-tables"></a>Criar tabelas e a base de dados do Hive
Para criar as tabelas do Hive para o nosso conjunto de dados Criteo, abra o ***linha de comandos do Hadoop*** no ambiente de trabalho de nó principal e introduza o diretório de ramo de registo, introduzindo o comando

    cd %hive_home%\bin

> [!NOTE]
> Executar todos os comandos de ramo de registo esta explicação passo a passo da Reciclagem do ramo de registo / linha de comandos do diretório. Esta ação trata da quaisquer problemas de caminho automaticamente. Pode utilizar os termos "Linha de diretório de ramo de registo", "Hive bin / linha de comandos do diretório" e "linha de comandos do Hadoop"-no alternadamente.
> 
> [!NOTE]
> Para executar qualquer consulta do Hive, sempre um pode utilizar os seguintes comandos:
> 
> 

        cd %hive_home%\bin
        hive

Depois de REPL de ramo de registo é apresentada com um "ramo de registo >"iniciar sessão, basta cortar e colar a consulta para executá-lo.

O código seguinte cria uma base de dados "criteo" e, em seguida, gera 4 tabelas:

* um *tabela para gerar contagens* incorporado no dia dias\_00 ao dia\_20,
* um *tabela para utilização como o conjunto de dados de formação* incorporado no dia\_21, e
* dois *tabelas para utilização como os conjuntos de dados de teste* incorporado no dia\_22 e dia\_23, respetivamente.

Divida o conjunto de dados de teste em duas tabelas diferentes porque um dos dias é um feriado. O objetivo consiste em determinar se o modelo pode detetar as diferenças entre um feriado e não feriado da taxa de clique.

O script [exemplo &#95; hive &#95; criar &#95; criteo &#95; base de dados &#95; e &#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) é apresentado aqui para sua comodidade:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Todas estas tabelas estão externas para simplesmente pode apontar para as respetivas localizações de armazenamento de Blobs do Azure (wasb).

**Existem duas formas de executar a consulta do Hive qualquer:**

1. **Utilizando a linha de comandos de REPL de ramo de registo**: O primeiro consiste em emitir um comando "ramo de registo" e copie e cole uma consulta em REPL o ramo de registo da linha de comandos. Para tal, execute:
   
        cd %hive_home%\bin
        hive
   
     Agora no REPL da linha de comandos, cortando e colando a consulta executa-lo.
2. **Guardar consultas para um ficheiro e executar o comando**: A segundo é guardar consultas para um ficheiro de .hql ([exemplo &#95; hive &#95; criar &#95; criteo &#95; base de dados &#95; e &#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e em seguida, emita o comando seguinte para executar a consulta:
   
        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Confirmar a criação da base de dados e a tabela
Em seguida, confirmar a criação da base de dados com o comando seguinte a partir da Reciclagem do Hive / linha de diretório:

        hive -e "show databases;"

Isto fornece:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Isto confirma que a criação da nova base de dados, "criteo".

Para ver as tabelas foram criadas, basta emitir o comando da Reciclagem do ramo de registo / linha de diretório:

        hive -e "show tables in criteo;"

Em seguida, deverá ver o seguinte resultado:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a>Exploração de dados no ramo de registo
Agora, está pronto para efetuar algumas exploração de dados básica no Hive. Comece por contando o número de exemplos de formação e testar as tabelas de dados.

### <a name="number-of-train-examples"></a>Número de exemplos de formação
O conteúdo do [exemplo &#95; hive &#95; contagem &#95; formação &#95; tabela &#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) são mostradas aqui:

        SELECT COUNT(*) FROM criteo.criteo_train;

Isto gera:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Em alternativa, uma também pode emitir o comando seguinte a partir da Reciclagem do Hive / linha de diretório:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Número de exemplos de teste nos dois conjuntos de dados de teste
Agora contabilizar o número de exemplos nos dois conjuntos de dados de teste. O conteúdo do [exemplo &#95; hive &#95;contagem &#95; criteo &#95; teste &#95; dia &#95; 22 &#95; tabela &#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) são aqui:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Isto gera:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Normalmente, também pode chamar o script da Reciclagem do ramo de registo / diretório linha por emissão do comando:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Por fim, é examinar o número de exemplos de teste no conjunto de dados de teste com base no dia\_23.

O comando para efetuar este procedimento é semelhante à apenas mostradas (consulte [exemplo &#95; hive &#95; contagem &#95; criteo &#95; teste &#95; dia &#95; 23 &#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Isto fornece:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribuição de etiqueta no conjunto de dados de formação
É a distribuição de etiqueta no conjunto de dados de formação de interesse. Para ver isto, Mostrar conteúdo do [exemplo &#95; hive &#95; criteo &#95; etiqueta &#95; distribuição &#95; formação &#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Isto gera a distribuição de etiqueta:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Tenha em atenção que a percentagem de etiquetas positivas cerca de 3.3% (consistente com o conjunto de dados original).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuições de histograma de algumas variáveis numérico no conjunto de dados de formação
Pode utilizar nativo do ramo de registo "histograma\_numérico" função para descobrir aspeto a distribuição das variáveis numérico. Seguem-se os conteúdos do [exemplo &#95; hive &#95; criteo &#95; histograma &#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Isto gera o seguinte:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

O LATERAL vista - explode combinação no ramo de registo funciona para produzir um resultado como o SQL Server em vez da lista habitual. Tenha em atenção que nesta tabela, a primeira coluna corresponde ao centro de bin e a segunda para a frequência de reciclagem.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentiles aproximados de algumas variáveis numérico no conjunto de dados de formação
Também de interesse com variáveis numérico é o cálculo de percentiles aproximados. Ramo de registo do nativo "percentil\_aprox" fazê-lo para-nos. O conteúdo do [exemplo &#95; hive &#95; criteo &#95; aproximado &#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) são:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Isto gera:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

A distribuição percentiles é estritamente relacionadas com a distribuição histograma qualquer variável numérico normalmente.         

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Localizar o número de valores exclusivos para algumas colunas no conjunto de dados de formação categórico
Continuar a exploração de dados, obter, de para algumas colunas categórico, o número de valores exclusivos que tomar. Para tal, Mostrar conteúdo do [exemplo &#95; hive &#95; criteo &#95; exclusivo &#95; valores &#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Isto gera:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Tenha em atenção que Col15 tem valores exclusivos milhão 19! Utilização naïve técnicas, como "acesso frequente uma codificação" codificar essas variáveis categórico alta dimensional não for viável. Em particular, denominada uma técnica de elevado desempenho, robusta [Learning com contagens](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para tackling eficientemente este problema é explicado e demonstrou.

Por fim, observe o número de valores exclusivos para algumas outras categórico colunas bem. O conteúdo do [exemplo &#95; hive &#95; criteo &#95; exclusivo &#95;valores &#95; vários &#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) são:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Isto gera:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Novamente, lembre-se, exceto Col20, todas as outras colunas tem vários valores exclusivos.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Ocorrência conjunta contagens de pares de categórico variáveis no conjunto de dados de formação

O número de ocorrência conjunta de pares de variáveis categórico também é de interesse. Isto pode ser determinado utilizando o código no [exemplo &#95; hive &#95; criteo &#95; emparelhado &#95; categórico &#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Inversa o número de ordem pelo respetiva ocorrência e observe a parte superior 15 neste caso. Isto dá-na:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a>Os conjuntos de dados de exemplo para baixo para o Azure Machine Learning
Ter explorou os conjuntos de dados e demonstrado como fazê-lo este tipo de exploração das eventuais variáveis (incluindo combinações), para baixo de exemplo os conjuntos de dados para que os modelos no Azure Machine Learning podem ser criados. Devolução de chamada que é o foco do problema: num determinado conjunto de atributos de exemplo (valores de funcionalidade da Col2 - Col40), prever se Col1 é 0 (sem clicar) ou 1 (clique).

Para reduzir os conjuntos de dados de formação e teste 1 de % do tamanho original de exemplo, utilize a função RAND() nativa do ramo de registo. O script seguinte, [exemplo &#95; hive &#95; criteo &#95; downsample &#95; formação &#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) fazê-lo para o conjunto de dados de formação:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Isto gera:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

O script [exemplo &#95; hive &#95; criteo &#95; downsample &#95; teste &#95; dia &#95; 22 &#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) faz-lo por dados de teste, dia\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Isto gera:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Por fim, o script [exemplo &#95; hive &#95; criteo &#95; downsample &#95; teste &#95; dia &#95; 23 &#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) faz-lo por dados de teste, dia\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Isto gera:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Com esta opção, está pronto para utilizar o nosso formação de amostragem para baixo e testar conjuntos de dados para a criação de modelos no Azure Machine Learning.

Não há um componente importante final antes de passar para o Azure Machine Learning, seja relativo a tabela contagem. Na secção seguinte secundárias, a tabela contagem é abordada em detalhe.

## <a name="count"></a>Ver um debate breve na tabela de contagem
Conforme mostrado, várias variáveis categórico tem uma dimensionalidade muito elevada. Instruções, denominada uma técnica de elevado desempenho [Learning com contagens de](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) de codificar estas variáveis num eficiente, forma robusta é apresentada. Obter mais informações sobre esta técnica são na ligação fornecida.

[!NOTE]
>Esta explicação passo a passo, o foco é utilizar tabelas de contagem para produzir representações compact das funcionalidades categórico alta dimensional. Não é a única forma de codificar categórico funcionalidades; Para obter mais informações sobre outras técnicas, podem consultar os utilizadores interessados [um acesso frequente-codificação](http://en.wikipedia.org/wiki/One-hot) e [funcionalidade hash](http://en.wikipedia.org/wiki/Feature_hashing).
>

Para criar tabelas de contagem nos dados de contagem, utilize os dados na pasta não processados/contagem de. Na secção de modelação, os utilizadores são apresentados como criar estas tabelas de contagem de funcionalidades categórico a partir do zero, ou em alternativa a utilizar uma tabela de contagem previamente concebidos para as respetivas explorations. No que forma, quando "contagem tabelas criadas previamente" são referidas, iremos significa utilizar as tabelas de contagem foram fornecidas. São fornecidas instruções detalhadas sobre como aceder a estas tabelas na secção seguinte.

## <a name="aml"></a>Criar um modelo com o Azure Machine Learning
Nosso modelo criação de processo no Azure Machine Learning segue estes passos:

1. [Obter os dados de tabelas do Hive no Azure Machine Learning](#step1)
2. [Criar a experimentação: limpar os dados e featurize com tabelas de contagem](#step2)
3. [Criar, dar formação e Pontuar o modelo](#step3)
4. [Avalie o modelo](#step4)
5. [Publicar o modelo como um serviço web](#step5)

Agora, está pronto para criar modelos no Azure Machine Learning studio. A nossa baixo amostras de dados são guardadas como as tabelas do Hive no cluster. Utilizar o Azure Machine Learning **importar dados** módulo para ler estes dados. As credenciais para aceder à conta de armazenamento deste cluster são fornecidas no que se segue.

### <a name="step1"></a>Passo 1: Obter dados a partir de tabelas do Hive no Azure Machine Learning utilizando o módulo de importar dados e selecione-o para uma experimentação do machine learning
Comece por selecionar uma **+ novo** -> **experimentação** -> **experimentação em branco**. Em seguida, a partir de **pesquisa** caixa na parte superior esquerda, procure "Importar dados". Arrastar e largar o **importar dados** módulo para a experimentação tela (a parte central do ecrã) para utilizar o módulo para acesso a dados.

Este é o que o **importar dados** parece ao obter dados da tabela do Hive:

![Importar dados obtém dados](./media/hive-criteo-walkthrough/i3zRaoj.png)

Para o **importar dados** módulo, os valores de parâmetros que são fornecidos no gráfico são apenas exemplos da ordenação dos valores tem de fornecer. Eis algumas orientações gerais sobre a preencher o parâmetro definido para o **importar dados** módulo.

1. Escolher "Consulta do Hive" para **origem de dados**
2. No **consulta de base de dados do Hive** caixa, SELECIONE um simple * FROM < o\_base de dados\_name.your\_tabela\_name >-é suficiente.
3. **URI do servidor de Hcatalog**: se o cluster é "abc", em seguida, isto é simplesmente: https://abc.azurehdinsight.net
4. **Nome de conta de utilizador do Hadoop**: O nome de utilizador escolhido no momento da commissioning o cluster. (Não o acesso remoto nome de utilizador!)
5. **Palavra-passe de conta de utilizador Hadoop**: A palavra-passe para o nome de utilizador escolhido no momento da commissioning o cluster. (Não o acesso remoto palavra-passe!)
6. **Localização dos dados de saída**: escolha "Azure"
7. **Nome da conta de armazenamento do Azure**: A conta de armazenamento associada com o cluster
8. **Chave de conta de armazenamento do Azure**: A chave da conta de armazenamento associados com o cluster.
9. **Nome do contentor do Azure**: se o nome do cluster é "abc", em seguida, isto é simplesmente "abc", normalmente.

Uma vez a **importar dados** depois de concluída a obtenção de dados (vir marcas de escala verde no módulo), guardar estes dados, como um conjunto de dados (com um nome à sua escolha). Este aspeto:

![Importar dados guardam dados](./media/hive-criteo-walkthrough/oxM73Np.png)

Faça duplo clique na porta de saída do **importar dados** módulo. Com isto são um **guardar como conjunto de dados** opção e um **visualizar** opção. O **visualizar** opção, se clicar, apresenta 100 linhas de dados, juntamente com um painel à direita que é útil para algumas estatísticas de resumidas. Para guardar os dados, basta selecionar **guardar como conjunto de dados** e siga as instruções.

Para selecionar o conjunto de dados guardado para utilização numa experimentação do machine learning, localize a conjuntos de dados utilizando o **pesquisa** caixa mostrada na figura seguinte. Em seguida, escreva simplesmente terminar o nome deu o conjunto de dados parcialmente para aceder à mesma e arraste o conjunto de dados para o painel principal. Removê-lo para o painel principal seleciona para utilização num modelação do machine learning.

![Drage conjunto de dados para o painel principal](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Fazê-lo para a formação e os conjuntos de dados de teste. Além disso, lembre-se utilizar o nome de base de dados e os nomes de tabela que forneceu para esta finalidade. Os valores utilizados na figura são apenas para ilustração purposes.* *
> 
> 

### <a name="step2"></a>Passo 2: Criar uma experimentação simples no Azure Machine Learning para prever cliques / nenhum cliques
A nossa experimentação do Azure ML tem o seguinte aspeto:

![Experimentação do Machine Learning](./media/hive-criteo-walkthrough/xRpVfrY.png)

Agora, examine os componentes principais desta fase experimental. Arraste nosso guardada formação e teste primeiro a conjuntos de dados para o nosso tela de experimentação.

#### <a name="clean-missing-data"></a>Apagar dados em falta
O **apagar dados em falta** módulo o respetivo nome sugere:-cleans dados em falta de formas que podem ser especificado para um utilizador. Procure para este módulo para ver isto:

![Apagar dados em falta](./media/hive-criteo-walkthrough/0ycXod6.png)

Aqui, optar por substituir todos os valores em falta com um 0. Existem outras opções de bem, que podem ser vistas observando as dropdowns no módulo.

#### <a name="feature-engineering-on-the-data"></a>Engenharia da funcionalidade nos dados
Podem existir milhões de valores exclusivos para algumas funcionalidades categórico de grandes conjuntos de dados. A utilização de métodos de naïve como acesso frequente uma codificação para representar essas funcionalidades categórico alta dimensional é inteiramente unfeasible. Esta explicação passo a passo demonstra como utilizar funcionalidades de contagem utilizar módulos incorporados do Azure Machine Learning para gerar representações compact destas variáveis categórico alta dimensional. O resultado final é um tamanho mais pequeno do modelo, tempos mais rápidos de formação e métricas de desempenho que são bastante comparáveis à utilizar outras técnicas.

##### <a name="building-counting-transforms"></a>Criar as transformações de contagem
Para compilar funcionalidades do contagem, utilize o **criar contando transformar** módulo que está disponível no Azure Machine Learning. O módulo tem o seguinte aspeto:

![Criar o módulo de contagem de transformação](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![criar contando transformar módulo](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT] 
> No **contagem de colunas** box, introduza essas colunas que pretende efetuar contagens. Normalmente, estes são (tal como mencionado) elevado dimensional categórico colunas. Lembre-se de que o conjunto de dados Criteo tem 26 colunas categórico: de Col15 para Col40. Aqui, contagem em todos eles e atribua os índices (a partir de 15 para 40 separados por vírgulas, conforme mostrado).
> 

Para utilizar o módulo no modo de MapReduce (adequado para grandes conjuntos de dados), precisa de acesso a um cluster do HDInsight Hadoop (utilizada para a exploração de funcionalidade que pode ser reutilizada para esta finalidade, bem como) e as suas credenciais. As anteriores figuras mostram que o preenchidos na valores aspeto como (substituir os valores fornecidos para fins de ilustração com os que são relevantes para o suas próprias caso de utilização).

![Parâmetros do módulo](./media/hive-criteo-walkthrough/05IqySf.png)

A ilustração anterior mostra como introduza a localização de blob de entrada. Esta localização tem os dados reservados para criação de tabelas de contagem no.

Quando este módulo termina em execução, guarde a transformação para posterior clicar o módulo e selecionando a **guardar como transformação** opção:

![Opção "Guardar como transformação"](./media/hive-criteo-walkthrough/IcVgvHR.png)

No nosso arquitetura experimentação mostrada acima, o conjunto de dados "ytransform2" corresponde ao precisamente uma transformação de contagem guardado. Para o resto desta fase experimental, presume-se que o leitor utilizado um **criar contando transformar** módulo no alguns dados para gerar contagens e pode, em seguida, utilizar esses contagens para gerar a contagem de funcionalidades nos conjuntos de dados de formação e teste.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Escolher o número de funcionalidades para incluir como parte dos conjuntos de dados de formação e teste
Uma vez preparado de transformação uma contagem, o utilizador pode escolher quais as funcionalidades para incluir no respetiva formação e testar a conjuntos de dados utilizando o **modificar parâmetros de tabela de contagem** módulo. Por questões de exaustividade, este módulo é mostrado aqui. Mas nos interesses simplicidade, na verdade, utiliza na nossa experiência.

![Modifique os parâmetros de tabela de contagem](./media/hive-criteo-walkthrough/PfCHkVg.png)

Neste caso, como podem ser vistos, os registo-odds estão a ser utilizado e cópia de segurança a coluna é ignorada. Também pode definir parâmetros, tais como o limiar de reciclagem de libertação da memória, quantos exemplos pseudo-anteriores para adicionar suavização e se deve utilizar qualquer ruído Laplacian ou não. Todas estas funcionalidades avançadas e é de salientar que os valores predefinidos são um ponto de partida para os utilizadores que são novos para este tipo de geração de funcionalidade.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformação de dados antes de gerar as funcionalidades de contagem
Agora o foco incide num importante ponto sobre transformar o nosso formação e testar dados antes de gerar, na verdade, as funcionalidades de contagem. Tenha em atenção que existem duas **executar Script do R** módulos utilizados antes da transformação de contagem é aplicada aos nossos dados.

![Executar os módulos de R Script](./media/hive-criteo-walkthrough/aF59wbc.png)

Eis o script R primeiro:

![Primeiro script R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Este script do R muda o nome nosso colunas para nomes de "Col1" a "Col40". Isto acontece porque a transformação de contagem espera nomes este formato.

O script R segundo equilibra a distribuição entre classes positivos e negativos (classes de 1 e 0 respetivamente) por baixo-amostragem a classe negativa. O script do R aqui mostra como efetuar este procedimento:

![Segundo script R](./media/hive-criteo-walkthrough/91wvcwN.png)

Este script do R simples, o "pos\_neg\_rácio" é utilizado para definir o período de equilíbrio entre o positivos e negativos classes. Isto é importante fazer uma vez que melhorar desequilíbrio de classe tem, geralmente, os benefícios de desempenho para classificação problemas em que a distribuição de classe skewed (devolução de chamada que neste caso, tem classe positivo 3.3% e classe negativo 96.7%).

##### <a name="applying-the-count-transformation-on-our-data"></a>Aplicar a transformação de contagem nos nossos dados
Por fim, pode utilizar o **Aplicar transformação** módulo para aplicar as transformações de contagem na nossa formação e testar conjuntos de dados. Este módulo utiliza a transformação de contagem guardada como uma entrada e os conjuntos de dados de formação ou de teste como outro entrada e devolve dados com funcionalidades de contagem. É mostrada aqui:

![Aplicar o módulo de transformação](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Parece ser um excerpt das funcionalidades que contagem
É instructive para ver o aspeto as funcionalidades de contagem no nosso caso. Eis um excerpt isto:

![Funcionalidades de contagem](./media/hive-criteo-walkthrough/FO1nNfw.png)

Este excerpt mostra que para as colunas contadas, obter o número e iniciar sessão odds para além de qualquer backoffs relevantes.

Agora está pronto para criar um modelo do Azure Machine Learning utilizando estes conjuntos de dados transformados. Na próxima secção mostra como isto pode ser feito.

### <a name="step3"></a>Passo 3: Criar, dar formação e Pontuar o modelo

#### <a name="choice-of-learner"></a>Escolha de learner
Em primeiro lugar, tem de escolher um learner. Utilize uma árvore de decisões elevada de duas classes como nosso learner. Seguem-se as opções predefinidas para este learner:

![Parâmetros de árvore de decisões elevada de duas classes](./media/hive-criteo-walkthrough/bH3ST2z.png)

Para a experimentação, escolha os valores predefinidos. Tenha em atenção que as predefinições são normalmente significativas e uma boa forma de obter linhas de base rápidas no desempenho. Pode melhorar no desempenho ao varrimento parâmetros se optar por assim que tiver uma linha de base.

#### <a name="train-the-model"></a>Dar formação sobre o modelo
Para formação, basta invocar um **preparar modelo** módulo. As duas entradas para a mesma são learner a árvore de decisões elevada de duas classes e o nosso conjunto de dados de formação. Isto é mostrado aqui:

![Módulo do modelo de formação](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Pontuar o modelo
Depois de ter um modelo preparado, está pronto para pontuar o conjunto de dados de teste e avaliar o desempenho dele. Fazê-lo utilizando o **Pontuar modelo** módulo mostrado na figura seguinte, juntamente com um **avaliar modelo** módulo:

![Módulo do modelo de pontuação](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a>Passo 4: Avaliar o modelo
Por fim, deve analisar o desempenho do modelo. Normalmente, para problemas de classificação (binário) de classe dois, uma boa medida é o AUC. Para visualizar este, ligue o **Pontuar modelo** módulo para um **avaliar modelo** módulo para este. Ao clicar em **visualizar** no **avaliar modelo** módulo gera um gráfico como o seguinte:

![Avaliar modelo BDT de módulo](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Binário (ou classe dois) problemas de classificação, uma boa medida de precisão de predição é a área na curva (AUC). A secção seguinte mostra a nossa resultados utilizando este modelo no nosso conjunto de dados de teste. Para obter esta, faça duplo clique na porta de saída do **avaliar modelo** módulo e, em seguida, **visualizar**.

![Visualizar o módulo de modelo de avaliação](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a>Passo 5: Publicar o modelo como um serviço Web
A capacidade de publicar um modelo do Azure Machine Learning como serviços web com um mínimo de fuss é uma funcionalidade útil para efetuar amplamente disponível. Depois de o fazer, qualquer pessoa pode efetuar chamadas para o serviço web com dados de entrada que têm predições para e de que o serviço web utiliza o modelo para devolver as predições.

Para tal, guarde primeiro o nosso modelo treinado como um objeto de modelo treinado. Isto é feito clicando com o **preparar modelo** módulo e utilizar o **guardar como modelo treinado** opção.

Em seguida, Criar entrada e saída portas para o nosso serviço web:

* uma porta de entrada utiliza dados no mesmo formulário como os dados que precisa de predições para
* uma porta de saída devolve as etiquetas classificadas e as probabilidades associadas.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecione algumas linhas de dados para a porta de entrada
É conveniente utilizar um **Aplicar transformação do SQL Server** módulo para selecionar apenas 10 linhas para servir como os dados de porta de entrada. Selecione apenas a estes linhas de dados para o nosso porta de entrada utilizando a consulta SQL mostrada aqui:

![Dados de porta de entrada](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Serviço Web
Agora, está pronto para executar uma experimentação pequena que pode ser utilizada para publicar o nosso serviço web.

#### <a name="generate-input-data-for-webservice"></a>Gerar dados de entrada do serviço Web
Como passo zeroth, uma vez que a tabela contagem grande, demorar algumas linhas de dados de teste e gerar dados de saída do mesmo com funcionalidades de contagem. Isto pode servir como o formato de dados de entrada do nosso serviço Web. Isto é mostrado aqui:

![Criar dados de entrada BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Para o formato de dados de entrada, utilize o resultado a **contagem Featurizer** módulo. Depois de esta experimentação depois de concluída a executar, guardar o resultado do **contagem Featurizer** módulo como um conjunto de dados. Este conjunto de dados é utilizado para os dados de entrada, o serviço Web.
> 
> 

#### <a name="scoring-experiment-for-publishing-webservice"></a>A classificação de experimentação do serviço de publicação Web
Em primeiro lugar, é apresentado este aspeto. A estrutura essencial é um **Pontuar modelo** módulo que aceita o nosso objeto de modelo treinado e algumas linhas de dados de entrada que foram geradas nos passos anteriores utilizando o **contagem Featurizer** módulo. Utilize "Selecionar colunas no conjunto de dados" para o projeto Scored etiquetas e as probabilidades de pontuação.

![Selecionar colunas no conjunto de dados](./media/hive-criteo-walkthrough/kRHrIbe.png)

Repare como o **selecionar colunas no conjunto de dados** módulo pode ser utilizado para 'Filtrar' dados de um conjunto de dados. Os conteúdos são mostrados aqui:

![Com a selecionar colunas no conjunto de dados módulo de filtragem](./media/hive-criteo-walkthrough/oVUJC9K.png)

Para obter as portas de saída e entrada azul, basta clicar em **preparar webservice** na parte inferior direita. Executar esta fase experimental também lhe permite-nos publicar o serviço web: clique o **publicar o serviço de WEB** ícone no aqui na parte inferior direita, apresentado:

![Publicar o serviço Web](./media/hive-criteo-walkthrough/WO0nens.png)

Assim que o serviço Web é publicado, obter redirecionado para uma página que procura, por conseguinte:

![Dashboard de serviço Web](./media/hive-criteo-walkthrough/YKzxAA5.png)

Tenha em atenção as duas ligações para webservices no lado esquerdo:

* O **pedido/resposta** (ou serviço RRS) destina-se para as predições único e é que tem sido utilizado neste workshop.
* O **de execução de lote** serviço (BES) é utilizado para predições do batch e requer que os dados de entrada utilizada para fazer predições residem no armazenamento de Blobs do Azure.

Ao clicar na ligação **pedido/resposta** demora-nos para uma página que dá-na pré-encontra código em c#, python e R. Este código pode ser comodamente utilizado para efetuar chamadas para o serviço Web. Tenha em atenção que a chave de API nesta página tem de ser utilizado para autenticação.

É prático copiar este código python através a uma célula de novo, o bloco de notas IPython.

Eis um segmento de código de python com a chave de API correta.

![Código de Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

Tenha em atenção que a chave de API predefinida foi substituída com a chave de API do nosso webservices. Ao clicar em **executar** nesta célula num IPython bloco de notas gera a seguinte resposta:

![Resposta de IPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Para os dois exemplos mais frequentes sobre (no framework JSON do script de python) de teste, regressar respostas no formato "Scored etiquetas, Scored probabilidades". Neste caso, os valores predefinidos foram escolheu que o código de pré-encontra fornece (0 para todas as colunas numéricas e a cadeia "valor" para todas as colunas categórico).

Isto conclui a nossa instruções sobre como lidar com o conjunto de dados em grande escala, utilizando o Azure Machine Learning. Começar a utilizar um terabyte de dados, construir um modelo de previsão e implementado como um serviço web na nuvem.

