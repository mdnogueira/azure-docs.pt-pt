---
title: Criar e implementar um modelo de machine learning utilizar o SQL Server numa VM do Azure | Microsoft Docs
description: "Processo de análise avançada e tecnologia em ação"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6066b083-262c-4453-a712-a5c05acc3df8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: fashah;bradsev
ms.openlocfilehash: d42377a55b1decc0918932b3ecc13cf575f934a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>O processo de ciência de dados de equipa em ação: utilizar o SQL Server
Neste tutorial, a guiá-lo durante o processo de criar e implementar um modelo de machine learning com o SQL Server e um conjunto de dados publicamente disponível – o [NYC Taxi viagens](http://www.andresmh.com/nyctaxitrips/) conjunto de dados. O procedimento segue um fluxo de trabalho de ciências de dados padrão: ingestão e explorar os dados, engenheiro de funcionalidades para o facilitar a aprendizagem, em seguida, criar e implementar um modelo.

## <a name="dataset"></a>NYC Taxi viagens descrição do conjunto de dados
Os dados de NYC Taxi viagem cerca de 20GB de ficheiros CSV comprimidos (GB de ~ 48 descomprimido), que inclui mais de 173 milhões de viagens individuais e os fares paga para cada viagem. Cada registo viagem inclui a localização de recolha e drop-off e a hora, acesso anónimos (controlador) número de licença e número de medallion (id exclusivo do taxi). Os dados abrange todos os viagens no ano 2013 e são fornecidos nos seguintes dois conjuntos de dados de cada mês:

1. O 'trip_data CSV contém detalhes viagem, tais como o número de passageiros, recolha e dropoff pontos, duração de viagem e comprimento viagem. Seguem-se alguns registos de exemplo:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. O trip_fare CSV contém detalhes de fare paga para cada viagem, tais como o tipo de pagamento, a quantidade de fare, surcharge e, sugestões e impostos tolls e a quantidade total paga. Seguem-se alguns registos de exemplo:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para associar a viagem\_dados e viagem\_fare é composto por campos: medallion, acesso\_licenciamento e a recolha\_datetime.

## <a name="mltasks"></a>Exemplos de predição de tarefas
Iremos irá formular três problemas de predição com base no *sugestão\_quantidade*, nomeadamente:

1. Classificação do binária: prever ou não uma sugestão foi paga para uma viagem, ou seja, um *sugestão\_quantidade* que é superior ao $0 é um exemplo positivo, enquanto um *sugestão\_quantidade* $ 0 é um exemplo negativo.
2. Classificação de várias classes: prever o intervalo de sugestão paga para a viagem. Iremos dividir os *sugestão\_quantidade* em cinco intervalos binários ou classes:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Tarefa de regressão: para prever a quantidade de sugestão paga para uma viagem.  

## <a name="setup"></a>Ambiente de ciência de dados de definição cópias de segurança do Azure para análise avançada
Como pode o [planear o ambiente](plan-your-environment.md) guia, existem várias opções para trabalhar com o conjunto de dados NYC Taxi viagens no Azure:

* Trabalhar com os dados de blobs do Azure e o modelo no Azure Machine Learning
* Carregar os dados para uma base de dados do SQL Server e o modelo no Azure Machine Learning

Neste tutorial, iremos demonstrar importação paralelo em massa de dados para um SQL Server, exploração de dados, a funcionalidade de engenharia e para baixo amostragem utilizando o SQL Server Management Studio, bem como utilizar IPython bloco de notas. [Scripts de exemplo](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [IPython blocos de notas](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) são partilhados no GitHub. Um bloco de notas do exemplo IPython para trabalhar com os dados de blobs do Azure também está disponível na mesma localização.

Para configurar o ambiente de ciência de dados do Azure:

1. [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md)
2. [Criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md)
3. [Aprovisionar uma máquina de Virtual de ciência de dados](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), que fornece um SQL Server e um servidor de IPython bloco de notas.
   
   > [!NOTE]
   > Os scripts de exemplo e blocos de notas IPython serão transferidos para a máquina virtual de ciência de dados durante o processo de configuração. Quando o script de pós-instalação VM estiver concluída, os exemplos estarão disponíveis na biblioteca de documentos da VM:  
   > 
   > * Scripts de exemplo:`C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Blocos de notas do exemplo IPython:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   onde `<user_name>` é o nome de início de sessão do Windows da VM. Iremos dar as pastas de exemplo como **Scripts de exemplo** e **blocos de notas do exemplo IPython**.
   > 
   > 

Com base no tamanho do conjunto de dados, localização da origem de dados e o ambiente de destino do Azure selecionado, este cenário é semelhante à [cenário \#5: SQL Server numa VM do Azure de destino do conjunto de dados grande nos ficheiros de locais,](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Obter os dados a partir da origem pública
Para obter o [NYC Taxi viagens](http://www.andresmh.com/nyctaxitrips/) conjunto de dados da sua localização pública, poderá utilizar qualquer um dos métodos descritos em [mover dados para e do armazenamento de Blobs do Azure](move-azure-blob.md) para copiar os dados para a nova máquina virtual.

Para copiar os dados utilizando o AzCopy:

1. Inicie sessão na sua máquina virtual (VM)
2. Criar um novo diretório num disco de dados da VM (Nota: não utilize o disco temporário que é fornecido com a VM como um disco de dados).
3. Na janela de linha de comandos, execute a seguinte linha de comandos do Azcopy substituindo < path_to_data_folder > com a sua pasta de dados que criou no (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    Quando tiver concluído o AzCopy, um total de 24 zipado ficheiros CSV (12 para viagem\_dados e 12 para viagem\_fare) deve estar na pasta de dados.
4. Descomprimir os ficheiros transferidos. Tenha em atenção a pasta onde residem os ficheiros descomprimidos. Esta pasta irá ser referida como o < caminho\_para\_dados\_ficheiros\>.

## <a name="dbload"></a>Dados de importação em massa na base de dados do SQL Server
O desempenho de carregamento/transferência de grandes quantidades de dados para uma base de dados do SQL Server e consultas subsequentes pode ser melhorado utilizando *particionada tabelas e vistas*. Nesta secção, iremos segui as instruções descritas em [paralelo em massa importar utilizando o SQL Server partição tabelas de dados](parallel-load-sql-partitioned-tables.md) para criar uma nova base de dados e carregar os dados para as tabelas particionadas em paralelo.

1. Enquanto tem sessão iniciada sua VM, iniciar **SQL Server Management Studio**.
2. Estabelecer ligação utilizando a autenticação do Windows.
   
    ![Ligação SSMS][12]
3. Se ainda não tiver alterado o modo de autenticação do SQL Server e criado um novo utilizador de início de sessão do SQL Server, abra o ficheiro de script com o nome **alterar\_auth.sql** no **Scripts de exemplo** pasta. Altere o nome de utilizador predefinido e a palavra-passe. Clique em **! Executar** na barra de ferramentas para executar o script.
   
    ![Executar o Script][13]
4. Certifique-se de e/ou alterar os SQL Server da base de dados e registo de pastas predefinidas para se certificar de que serão armazenadas no disco de dados recentemente criado bases de dados. A imagem de VM do SQL Server que está otimizada para cargas de datawarehousing está pré-configurada com discos de dados e de registo. Se a VM não inclui um disco de dados e adicionar novos discos rígidos virtuais durante o processo de configuração VM, altere as pastas predefinidas da seguinte forma:
   
   * Faça duplo clique o nome do servidor de SQL no painel esquerdo e clique em **propriedades**.
     
       ![Propriedades do SQL Server][14]
   * Selecione **definições de base de dados** do **selecionar uma página** lista à esquerda.
   * Certifique-se de e/ou alterar o **localizações predefinidas de base de dados** para o **disco de dados** localizações à sua escolha. Este é onde novas bases de dados residem se criado com as predefinições de localização.
     
       ![Predefinições de base de dados do SQL Server][15]  
5. Para criar uma nova base de dados e um conjunto de grupos de ficheiros para armazenar as tabelas particionadas, abra o script de exemplo **criar\_db\_default.sql**. O script irá criar uma nova base de dados com o nome **TaxiNYC** e 12 grupos de ficheiros na localização de dados predefinida. Cada grupo de ficheiros irá conter um mês de viagem\_dados e viagem\_fare dados. Modificar o nome de base de dados, se assim o desejar. Clique em **! Executar** para executar o script.
6. Em seguida, crie duas tabelas de partição, um para a viagem\_dados e outra para a viagem\_fare. Abra o script de exemplo **criar\_particionada\_table.sql**, que irá:
   
   * Crie uma função de partição para dividir os dados por mês.
   * Crie um esquema de partição para mapear dados de cada mês para um grupo de ficheiros diferente.
   * Criar duas tabelas particionadas mapeadas para o esquema de partição: **nyctaxi\_viagem** vai conter a viagem\_dados e **nyctaxi\_fare** vai conter a viagem\_fare dados.
     
     Clique em **! Executar** para executar o script e criar as tabelas particionadas.
7. No **Scripts de exemplo** pasta, existem dois scripts do PowerShell de exemplo fornecidos para demonstrar importa paralela em massa de dados de tabelas do SQL Server.
   
   * **BCP\_paralelas\_generic.ps1** é um script genérico para importar dados para uma tabela de paralelo em massa. Modificar este script para definir as variáveis de entrada e de destino, conforme indicado nas linhas de comentário no script.
   * **BCP\_paralelas\_nyctaxi.ps1** é uma versão de pré-configurada do script genérico e pode ser utilizado para carregar ambas as tabelas para os dados de NYC Taxi viagens.  
8. Clique com botão direito do **bcp\_paralelas\_nyctaxi.ps1** nome do script e clique em **editar** para abri-lo no PowerShell. Reveja as variáveis predefinidas e modificar, de acordo com o nome de base de dados selecionada, a pasta de dados de entrada, pasta de registo de destino e caminhos para os ficheiros de formato de exemplo **nyctaxi_trip.xml** e **nyctaxi\_fare.xml** (fornecidas a **Scripts de exemplo** pasta).
   
    ![Dados de importação em volume][16]
   
    Também pode selecionar o modo de autenticação, a predefinição é a autenticação do Windows. Clique na seta verde na barra de ferramentas para executar. O script irá iniciar 24 operações de importação de em massa em paralelo, 12 para cada tabela particionada. Pode monitorizar o progresso da importação de dados, abrindo a pasta de dados do SQL Server predefinida como conjunto acima.
9. O script do PowerShell relatórios o inicial e final vezes. Quando todos os em massa importações concluída, a hora de fim é comunicada. Verifique a pasta de registo de destino para verificar se as importações em massa foram bem-sucedidas, ou seja, não existem erros reportados na pasta de registo de destino.
10. A base de dados está agora pronto para exploração, engenharia da funcionalidade e outras operações conforme pretendido. Uma vez que as tabelas são divididos em partições em conformidade com a **recolha\_datetime** campo, consultas, que incluem **recolha\_datetime** condições no **onde** cláusula será vantajoso contar com o esquema de partição.
11. No **SQL Server Management Studio**, explore o script de exemplo fornecidos **exemplo\_queries.sql**. Para executar qualquer uma das consultas de exemplo, realce as linhas de consulta, em seguida, clique em **! Executar** na barra de ferramentas.
12. Os dados de NYC Taxi viagens são carregados em duas tabelas separadas. Para melhorar a operações de associação, recomenda vivamente para as tabelas de índice. O script de exemplo **criar\_particionada\_index.sql** cria índices particionados na chave composta associação **medallion, acesso\_licença e recolha\_ DateTime**.

## <a name="dbexplore"></a>Exploração de dados e de engenharia da funcionalidade no SQL Server
Nesta secção, iremos efetuar geração de exploração e funcionalidade de dados através da execução de consultas SQL diretamente no **SQL Server Management Studio** utilizando a base de dados do SQL Server criado anteriormente. Um script de exemplo com o nome **exemplo\_queries.sql** é fornecido no **Scripts de exemplo** pasta. Modificar o script para alterar o nome de base de dados, se for diferente da predefinição: **TaxiNYC**.

Neste exercício, iremos:

* Ligar ao **SQL Server Management Studio** utilizando a autenticação do Windows ou utilizando a autenticação do SQL Server e o nome de início de sessão do SQL Server e a palavra-passe.
* Explore as distribuições de dados de alguns campos no variando intervalos de tempo.
* Investigue a qualidade dos dados dos campos de latitude e longitude.
* Gerar etiquetas de classificação de várias classes e binária com base no **sugestão\_quantidade**.
* Gerar funcionalidades e computação/comparar as distâncias de viagem.
* Associar as duas tabelas e extrair amostra aleatória que será utilizada para criar modelos.

Quando estiver pronto para continuar para o Azure Machine Learning, o utilizador pode optar por:  

1. Guardar a consulta SQL final para extrair os dados de exemplo e copiar-colar a consulta diretamente para um [importar dados] [ import-data] módulo no Azure Machine Learning, ou
2. Manter a amostragem e foi desenvolvidos dados que pretende utilizar para criar uma nova base de dados do modelo de tabela e utilizam a tabela de novo no [importar dados] [ import-data] módulo no Azure Machine Learning.

Nesta secção, irá guardar a consulta final para extrair e os dados de exemplo. O segundo método é demonstrado a [exploração de dados e funcionalidade de engenharia no bloco de notas do IPython](#ipnb) secção.

Para uma verificação rápida do número de linhas e colunas nas tabelas preenchido anteriormente utilizando a importação de paralelo em massa,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion
Neste exemplo identifica o medallion (taxi números) com mais do que 100 viagens dentro de um determinado período de tempo. A consulta seria beneficiar o acesso de tabela particionada necessitar, uma vez que este é ter pelo esquema de partição de **recolha\_datetime**. Consultar o conjunto de dados completo também faz com que utilize a tabela particionada e/ou a análise de índice.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploração: Distribuição de viagem medallion e hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: Verificar os registos com incorreto longitude e/ou latitude
Neste exemplo investiga se qualquer um dos campos de longitude e/ou latitude ou contém um valor inválido (radian graus devem ser entre -90 e 90), ou ter (0, 0) coordenadas.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Vs Tipped. Distribuição de Tipped viagens não
Neste exemplo localiza o número de viagens foram tipped vs tipped não num determinado momento período (ou no conjunto de dados completo se que abrangem o ano completo). Esta distribuição reflete a distribuição de etiqueta de binário para posteriormente, ser utilizada para a modelação de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Exploração: Distribuição de classe/intervalo de sugestão
Neste exemplo calcula a distribuição de intervalos de sugestão de um determinado período de tempo (ou no conjunto de dados completo se que abrangem o ano completo). Esta é a distribuição das classes de etiqueta que será posteriormente utilizada para a modelação de classificação de várias classes.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Computação e comparar distância viagem
Neste exemplo converte a longitude de recolha e drop-off e pontos de latitude para geografia SQL, calcula a distância viagem utilizando a diferença de pontos de geografia SQL e devolve uma amostra aleatória de resultados para comparação. O exemplo limita os resultados para coordenadas válidos utilizando apenas a consulta de avaliação de qualidade de dados abrangida anteriormente.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Engenharia da funcionalidade em consultas SQL
As etiqueta geração geografia conversão exploração consultas e também podem ser utilizadas para gerar etiquetas/funcionalidades removendo a parte de contagem. Exemplos SQL de engenharia funcionalidades adicionais são fornecidos no [exploração de dados e funcionalidade de engenharia no bloco de notas do IPython](#ipnb) secção. É mais eficiente para executar consultas de geração da funcionalidade no conjunto de dados completo ou um subconjunto de grandes dimensões do mesmo através de consultas SQL que executam diretamente na instância de base de dados do SQL Server. As consultas que podem ser executadas na **SQL Server Management Studio**, IPython bloco de notas ou qualquer ferramenta/ambiente de desenvolvimento que pode aceder na base de dados localmente ou remotamente.

#### <a name="preparing-data-for-model-building"></a>A preparar os dados para a criação de modelo
A seguinte consulta associa a **nyctaxi\_viagem** e **nyctaxi\_fare** tabelas, gera uma etiqueta de classificação binária **tipped**, um etiqueta de classificação de classe Multi **sugestão\_classe**e extrai uma amostra aleatória de 1% do conjunto de dados associado ao total. Esta consulta pode ser copiada e colada diretamente no [Azure Machine Learning Studio](https://studio.azureml.net) [importar dados] [ import-data] módulo para a ingestão de dados direta da base de dados do SQL Server instância no Azure. A consulta exclui registos com incorreto (0, 0) coordenadas.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Exploração de dados e de engenharia da funcionalidade no bloco de notas do IPython
Nesta secção, iremos efetuar exploração de dados e a geração de funcionalidade através de consultas de Python e SQL na base de dados do SQL Server criado anteriormente. Um exemplo IPython bloco de notas com o nome **machine-Learning-data-science-process-sql-story.ipynb** é fornecido no **blocos de notas do exemplo IPython** pasta. Este bloco de notas também está disponível no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

A sequência recomendada quando trabalhar com macrodados é o seguinte:

* Ler um exemplo dos dados pequeno para um intervalo de dados em memória.
* Efetue algumas visualizações e explorations utilizando amostras de dados.
* Experimentar a engenharia da funcionalidade utilizando os amostras de dados.
* Para maior exploração de dados, a manipulação de dados e de engenharia da funcionalidade, utilize o Python para emitir consultas de SQL diretamente na base de dados do SQL Server na VM do Azure.
* Decida o tamanho da amostra a utilizar para a criação de modelo do Azure Machine Learning.

Quando estiver pronto para continuar para o Azure Machine Learning, o utilizador pode optar por:  

1. Guardar a consulta SQL final para extrair os dados de exemplo e copiar-colar a consulta diretamente para um [importar dados] [ import-data] módulo no Azure Machine Learning. Este método é demonstrado a [edifício modelos no Azure Machine Learning](#mlmodel) secção.    
2. Manter os dados da amostras e foi desenvolvidos planear utilizar o modelo de criação de uma nova tabela de base de dados, em seguida, utilize a tabela de novo no [importar dados] [ import-data] módulo.

Seguem-se alguns exploração de dados, visualização de dados e funcionalidade exemplos de engenharia. Para obter mais exemplos, consulte o bloco de notas de SQL IPython de exemplo no **blocos de notas do exemplo IPython** pasta.

#### <a name="initialize-database-credentials"></a>Inicializar as credenciais da base de dados
Inicializar as definições de ligação de base de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Criar a ligação de base de dados
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Número de relatório de linhas e colunas na tabela nyctaxi_trip
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Número total de linhas = 173179759  
* Número total de colunas = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Leia-in de uma amostra de dados de pequena da base de dados SQL Server
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tempo até ao ler que a tabela de exemplo é 6.492000 segundos  
Número de linhas e colunas obter = (84952, 21)

#### <a name="descriptive-statistics"></a>Estatísticas descritivas
Agora está pronto para explorar as amostras de dados. Vamos começar a utilizar ao procurar estatísticas descritivas para o **viagem\_distância** (ou qualquer outro) campo (s):

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualização: Exemplo de desenho de caixa
Em seguida, observar o desenho da caixa para a distância viagem visualizar o quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Desenhar #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo de desenho de distribuição
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Desenhar #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualização: Barra e Plots de linha
Neste exemplo, vamos bin a distância viagem em cinco intervalos binários e visualizar os resultados binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Iremos pode representar a distribuição de reciclagem acima na barra de uma ou linha desenho tal como indicado abaixo

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Desenhar #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Desenhar #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualização: Exemplo de Scatterplot
Vamos mostrar o desenho de gráfico de dispersão entre **viagem\_tempo\_no\_seg** e **viagem\_distância** para ver se existe qualquer correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Desenhar #6][6]

Da mesma forma, pode verificar a relação entre **taxa\_código** e **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Desenhar #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Amostragem subplano os dados no SQL Server
Quando preparar dados para o modelo de criação num [Azure Machine Learning Studio](https://studio.azureml.net), que pode optar por opte por utilizar o **consulta SQL a utilizar diretamente no módulo importar dados** ou manter os dados da amostras e foi desenvolvidos numa nova tabela, o que pode utilizar no [importar dados] [ import-data] módulo com um simples **SELECIONAR * FROM < sua\_novo\_tabela\_name >** .

Nesta secção, irá criar uma nova tabela para armazenar os dados da amostras e foi desenvolvidos. Um exemplo de uma consulta SQL direta para a criação de modelo é fornecido no [exploração de dados e funcionalidade de engenharia no SQL Server](#dbexplore) secção.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Criar um exemplo de tabela e preencher com % 1 das tabelas associadas. Remova a primeira tabela se existir.
Nesta secção, vamos associar as tabelas **nyctaxi\_viagem** e **nyctaxi\_fare**, a extrair uma amostra aleatória de 1% e manter as amostras de dados com um novo nome de tabela  **nyctaxi\_um\_por cento**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploração de dados através de consultas de SQL no bloco de notas do IPython
Nesta secção, vamos explorar as distribuições de dados utilizando os dados de 1% amostragem que são mantidos na nova tabela que criámos acima. Tenha em atenção que podem ser executadas explorations semelhantes utilizando as tabelas originais, utilizando opcionalmente **TABLESAMPLE** para limitar a exploração de exemplo ou ao limitar os resultados para um através do período de tempo indicado o **recolha\_datetime** partições, conforme ilustrado no [exploração de dados e funcionalidade de engenharia no SQL Server](#dbexplore) secção.

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária viagens
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Viagem distribuição por medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Geração de funcionalidade através de consultas SQL no bloco de notas do IPython
Nesta secção, irá gerar novas etiquetas e funcionalidades, utilizando diretamente as consultas SQL, utilizar a tabela de exemplo de 1% criámos na secção anterior.

#### <a name="label-generation-generate-class-labels"></a>Geração de etiqueta: Gerar etiquetas de classe
No exemplo seguinte, iremos gerar dois conjuntos de etiquetas a utilizar para a modelação de:

1. Binário de classe de etiquetas **tipped** (prever se uma sugestão terá)
2. Etiquetas de várias classes **sugestão\_classe** (prever a Reciclagem de sugestão ou intervalo)
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Engenharia da funcionalidade: Funcionalidades de contagem para colunas Categórico
Neste exemplo transforma um campo categórico num campo numérico ao substituir cada categoria com a contagem de respetivas ocorrências nos dados.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Engenharia da funcionalidade: Funcionalidades de reciclagem para colunas numérico
Neste exemplo transforma um campo numérico contínuo em intervalos de categoria predefinidas, ou seja, transformação campo numérico para um campo categórico.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Engenharia da funcionalidade: Extrair as funcionalidades de localização de Decimal Latitude/Longitude
Neste exemplo divide a representação de um campo de latitude e/ou longitude decimal em vários campos de região de granularidade diferente, tais como país, cidade, town, blocos, etc. Tenha em atenção que os campos de georreplicação novo não estão mapeados para localizações reais. Para obter informações sobre localizações de geocode de mapeamento, consulte [Bing Maps REST Services](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique o formato final da tabela featurized
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Iremos agora está prontos continuar a criação de modelo e implementação de modelo no [Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos para qualquer um dos problemas de predição identificados anteriormente, nomeadamente:

1. Classificação do binária: prever ou não uma sugestão foi paga para uma viagem.
2. Classificação de várias classes: prever o intervalo de sugestão paga, de acordo com as classes definidas anteriormente.
3. Tarefa de regressão: para prever a quantidade de sugestão paga para uma viagem.  

## <a name="mlmodel"></a>Criar modelos no Azure Machine Learning
Para iniciar o exercício modelação, inicie sessão sua área de trabalho do Azure Machine Learning. Se ainda não tiver criado uma área de trabalho de aprendizagem, consulte o artigo [criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md).

1. Para começar a utilizar com o Azure Machine Learning, consulte o artigo [que é o Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Inicie sessão no [do Azure Machine Learning Studio](https://studio.azureml.net).
3. A página inicial do Studio fornece uma variedade de informações, vídeos, tutoriais, hiperligações para a referência de módulos e outros recursos. Fore mais informações sobre o Azure Machine Learning, consulte o [Centro de documentação do Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Uma experimentação de preparação típico consiste no seguinte:

1. Criar um **+ novo** experimentação.
2. Obter os dados do Azure Machine Learning.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gere funcionalidades conforme necessário.
5. Dividir os dados em conjuntos de dados de formação / / testes validação (ou tem conjuntos de dados separados para cada).
6. Selecione um ou mais algoritmos do machine learning, consoante o problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação de várias classes, regressão.
7. Preparar um ou mais modelos utilizando o conjunto de dados de formação.
8. Pontuar o conjunto de dados de validação utilizando o model(s) preparado.
9. Avalie o model(s) para calcular as métricas relevantes para o problema de aprendizagem.
10. Ajustar a otimizar o model(s) e selecione o modelo melhor a implementar.

Neste exercício, vamos já explorou e foi desenvolvido os dados no SQL Server e decidir o tamanho da amostra para inserção no Azure Machine Learning. Para criar um ou mais dos modelos de previsão decidimos:

1. Obter os dados para utilizar o Azure Machine Learning a [importar dados] [ import-data] módulo, disponível no **dados de entrada e saída** secção. Para obter mais informações, consulte o [importar dados] [ import-data] página de referência do módulo.
   
    ![Importar dados de aprendizagem do Azure][17]
2. Selecione **SQL Database do Azure** como o **origem de dados** no **propriedades** painel.
3. Introduza o nome DNS da base de dados no **nome do servidor de base de dados** campo. Formato:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Introduza o **nome de base de dados** no campo correspondente.
5. Introduza o **nome de utilizador do SQL Server** no * * aqccount nome do servidor de utilizador e a palavra-passe a **palavra-passe de conta de utilizador do**.
6. Verifique **aceita qualquer certificado de servidor** opção.
7. No **consulta de base de dados** editar a área de texto, cole a consulta que extrai os campos de base de dados necessárias (incluindo quaisquer campos calculados, tais como as etiquetas) e baixo amostras de dados para o tamanho da amostra pretendido.

É um exemplo de uma experimentação de classificação binária ler os dados diretamente a partir da base de dados do SQL Server na imagem abaixo. Podem ser construídas experimentações semelhantes para classificação de várias classes e problemas de regressão.

![Azure Machine Learning formação][10]

> [!IMPORTANT]
> Nos dados de modelação de extração e fazendo a amostragem de exemplos de consultas fornecidos nas secções anteriores, **todas as etiquetas para os exercícios modelação três estão incluídas na consulta**. É um passo importante (obrigatório) em cada um dos exercícios modelação **excluir** etiquetas desnecessárias para outros dois problemas e quaisquer outros **fugas de destino**. Para por exemplo, quando utilizar a classificação binária, utilize a etiqueta **tipped** e excluir os campos **sugestão\_classe**, **sugestão\_quantidade**e **total\_quantidade**. A última opção é fugas de destino, desde que o implica a sugestão paga.
> 
> Para excluir colunas desnecessárias e/ou fugas de destino, pode utilizar o [selecionar colunas no conjunto de dados] [ select-columns] módulo ou a [Editar metadados][edit-metadata]. Para obter mais informações, consulte [selecionar colunas no conjunto de dados] [ select-columns] e [Editar metadados] [ edit-metadata] páginas de referência.
> 
> 

## <a name="mldeploy"></a>Modelos de implementação no Azure Machine Learning
Quando o modelo estiver pronto, pode facilmente implementá-lo como um serviço web diretamente a partir de experimentação. Para obter mais informações sobre a implementação de serviços web do Azure Machine Learning, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Para implementar um novo serviço web, tem de:

1. Crie uma experimentação de classificação.
2. Implemente o serviço web.

Para criar uma experimentação de classificação de um **concluído** experimentação de preparação, clique em **criar classificação experimentação** na barra de ação inferior.

![A classificação do Azure][18]

O Azure Machine Learning irá tentar criar uma experimentação de classificação com base nos componentes da experimentação de preparação. Em particular, irá:

1. Guardar o modelo treinado e remova os módulos de formação do modelo.
2. Identificar uma lógica **porta de entrada** para representar o esquema de dados de entrada esperado.
3. Identificar uma lógica **porta de saída** para representar o esquema de saída do serviço web esperado.

Quando é criada a experimentação de classificação, reveja-lo e ajustar conforme necessário. É um ajuste típico para substituir o conjunto de dados de entrada e/ou a consulta com um que exclui os campos de etiqueta, como estes não estarão disponíveis quando o serviço é chamado. Também é uma boa prática para reduzir o tamanho do conjunto de dados de entrada e/ou a consulta para alguns registos, apenas suficiente para indicar o esquema da entrada. A porta de saída, é comum para excluir a entrada de todos os campos e incluir apenas o **etiquetas classificadas** e **probabilidades classificadas** em resultado, utilizando o [selecionar colunas no conjunto de dados] [ select-columns] módulo.

É um classificação experimentação de exemplo na figura abaixo. Quando estiver pronto para implementar, clique em de **publicar o serviço de WEB** botão na barra de ação inferior.

![Publicar de aprendizagem do Azure][11]

Para recap, neste tutorial explicação passo a passo, criou um ambiente de ciência de dados do Azure, trabalhado com um grande conjunto de dados público até a partir de aquisição de dados para o modelo de preparação e implementação de um serviço web Azure Machine Learning.

### <a name="license-information"></a>Informações de licença
Estas instruções de exemplo e o respetivo que acompanha scripts e IPython notebook(s) são partilhados por Microsoft com a licença MIT. Verifique o ficheiro LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

### <a name="references"></a>Referências
• [Andrés Monroy NYC Taxi viagens página de transferência](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxi viagem dados por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi e Limousine Commission investigação e estatísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
