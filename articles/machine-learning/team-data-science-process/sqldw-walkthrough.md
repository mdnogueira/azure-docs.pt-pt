---
title: "O processo de ciência de dados de equipa em ação: utilizar o SQL Data Warehouse | Microsoft Docs"
description: "Processo de análise avançada e tecnologia em ação"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 88ba8e28-0bd7-49fe-8320-5dfa83b65724
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: bradsev;weig
ms.openlocfilehash: 73517a8d58700e987ce80889dadf8791e53170a3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>O processo de ciência de dados de equipa em ação: utilizar o SQL Data Warehouse
Neste tutorial, iremos guiá-lo através de criar e implementar um modelo de machine learning utilizar o SQL Data Warehouse (armazém de dados do SQL Server) para um conjunto de dados publicamente disponível – o [NYC Taxi viagens](http://www.andresmh.com/nyctaxitrips/) conjunto de dados. O modelo de classificação binária construído prevê ou não uma sugestão é paga para uma viagem e modelos para classificação de várias classes e regressão também são abordados que prever a distribuição para as quantidades de sugestão pagas.

Segue o procedimento a [processo de ciência de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) fluxo de trabalho. Vamos mostrar como configurar um ambiente de ciência de dados, como carregar dados para o armazém de dados do SQL Server e como utilizar o armazém de dados do SQL Server ou um bloco de notas IPython para explorar os dados e o engenheiro de funcionalidades do modelo. Em seguida, mostramos como criar e implementar um modelo com o Azure Machine Learning.

## <a name="dataset"></a>O conjunto de dados NYC Taxi viagens
Os dados de NYC Taxi viagem constituem em cerca de 20GB de ficheiros CSV comprimidos (GB de ~ 48 descomprimido), gravar viagens mais de 173 milhões de individuais e os fares paga para cada viagem. Cada registo viagem inclui as localizações de recolha e drop-off e vezes, número de licença acesso anónimos (controlador) e o número de medallion (id exclusivo do taxi). Os dados abrange todos os viagens no ano 2013 e são fornecidos nos seguintes dois conjuntos de dados de cada mês:

1. O **trip_data.csv** ficheiro contém detalhes viagem, tais como o número de passageiros, recolha e dropoff pontos, duração de viagem e comprimento viagem. Seguem-se alguns registos de exemplo:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. O **trip_fare.csv** ficheiro contém detalhes de fare paga para cada viagem, tais como o tipo de pagamento, a quantidade de fare, surcharge e, sugestões e impostos tolls e a quantidade total paga. Seguem-se alguns registos de exemplo:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

O **chave exclusiva** utilizada para associar a viagem\_dados e viagem\_fare é composto por três campos seguintes:

* Medallion,
* Hack\_licença e
* recolha\_datetime.

## <a name="mltasks"></a>Três tipos de tarefas de predição de endereços
Iremos formular três problemas de predição com base no *sugestão\_quantidade* para ilustrar três tipos de modelação tarefas:

1. **Classificação binária**: prever ou não uma sugestão foi paga para uma viagem, ou seja, um *sugestão\_quantidade* que é superior ao $0 é um exemplo positivo, enquanto um *sugestão\_quantidade* $ 0 é um exemplo negativo.
2. **Classificação de várias classes**: prever o intervalo de sugestão paga para a viagem. Iremos dividir os *sugestão\_quantidade* em cinco intervalos binários ou classes:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Tarefa de regressão**: para prever a quantidade de sugestão paga para uma viagem.  

## <a name="setup"></a>Configurar o ambiente de ciência de dados do Azure para análise avançada
Para configurar o ambiente de ciência de dados do Azure, siga estes passos.

**Criar a sua própria conta de armazenamento de Blobs do Azure**

* Quando aprovisionar o seu próprio armazenamento de Blobs do Azure, escolha uma localização de georreplicação para o armazenamento de Blobs do Azure em ou mais perto possível **Sul Central nos**, que é onde os dados de NYC Taxi são armazenados. Os dados serão copiados utilizando o AzCopy a partir do contentor de armazenamento de BLOBs público para um contentor na sua própria conta do storage. Quanto mais próximo do armazenamento de Blobs do Azure é sul Central dos EUA, quanto mais depressa será possível concluir esta tarefa (passo 4).
* Para criar a sua própria conta de armazenamento do Azure, siga os passos descritos em [contas do storage do Azure sobre](../../storage/common/storage-create-storage-account.md). Lembre-se de que faça notas nos valores para as seguintes credenciais de conta de armazenamento, conforme irá ser necessário posteriormente nestas instruções.
  
  * **Nome da conta de armazenamento**
  * **Chave de conta de armazenamento**
  * **Nome do contentor** (o que pretende que os dados sejam armazenados no blob storage do Azure)

**Aprovisione a instância de armazém de dados do Azure SQL.**
Siga a documentação em [criar um SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para Aprovisionar uma instância do SQL Data Warehouse. Certifique-se de que efetua as notações nas seguintes credenciais de armazém de dados do SQL Server que serão utilizadas em passos posteriores.

* **Nome do servidor**: <server Name>. database.windows.net
* **Nome SQLDW (base de dados)**
* **Nome de Utilizador**
* **Palavra-passe**

**Instale Visual Studio e ferramentas de dados do SQL Server.** Para obter instruções, consulte [instalar o Visual Studio 2015 e/ou o SSDT (SQL Server Data Tools) para o SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Ligar ao seu armazém de dados SQL do Azure com o Visual Studio.** Para obter instruções, consulte os passos 1 e 2 no [ligar ao Azure SQL Data Warehouse com o Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Executar a seguinte consulta SQL na base de dados que criou no SQL Data Warehouse (em vez da consulta fornecida no passo 3 do tópico connect) **criar uma chave mestra**.
> 
> 

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Crie uma área de trabalho do Azure Machine Learning na sua subscrição do Azure.** Para obter instruções, consulte [criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md).

## <a name="getdata"></a>Carregar os dados para o SQL Data Warehouse
Abra uma consola de comandos do Windows PowerShell. Executar o PowerShell seguinte comandos para transferir o exemplo SQL script ficheiros partilhamos no GitHub para um diretório local que especificou com o parâmetro *- DestDir*. Pode alterar o valor do parâmetro *- DestDir* para qualquer diretório local. Se *- DestDir* não existir, será criado pelo script do PowerShell.

> [!NOTE]
> Poderá ter de **executar como administrador** ao executar o seguinte script do PowerShell se o *DestDir* diretório tem o privilégio de administrador para criar ou de escrita.
> 
> 

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Após a execução com êxito, o atual diretório de trabalho é alterado para *- DestDir*. Deve ser capaz de ver o ecrã, conforme mostrado abaixo:

![][19]

No seu *- DestDir*, execute o seguinte script do PowerShell no modo de administrador:

    ./SQLDW_Data_Import.ps1

Quando o script do PowerShell é executado pela primeira vez, será pedido para introduzir as informações do seu armazém de dados de SQL do Azure e a sua conta de armazenamento de Blobs do Azure. Quando tiver concluído este script do PowerShell em execução para a primeira vez, as credenciais de entrada será escrita para um ficheiro de configuração SQLDW.conf no diretório de trabalho presente. A execução futura deste ficheiro de script do PowerShell tem a opção para ler que necessário todos os parâmetros deste ficheiro de configuração. Se precisar de alterar alguns parâmetros, pode escolher para os parâmetros no ecrã após a linha de entrada por eliminar este ficheiro de configuração e inputting os valores de parâmetros, conforme solicitado ou para alterar os valores de parâmetro editando o ficheiro de SQLDW.conf no seu *- DestDir* diretório.

> [!NOTE]
> Para evitar conflitos de nome de esquema com aqueles que já existem no armazém de dados do SQL do Azure, quando a ler parâmetros diretamente a partir do ficheiro SQLDW.conf, 3 dígitos aleatório é adicionado um número para o nome do esquema do ficheiro SQLDW.conf como o nome de esquema predefinido para cada execução. O script do PowerShell pode solicitar-lhe um nome de esquema: pode ser especificado o nome de critério de utilizador.
> 
> 

Isto **script do PowerShell** ficheiro conclui as seguintes tarefas:

* **Transfere e instala o AzCopy**, se o AzCopy já não está instalado
  
        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Copia dados para a sua conta de armazenamento de BLOBs privada** do blob público com AzCopy
  
        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Carrega dados utilizando o Polybase (executando LoadDataToSQLDW.sql) para o armazém de dados de SQL do Azure** da sua conta de armazenamento de BLOBs privada com os seguintes comandos.
  
  * Criar um esquema
    
          EXEC (''CREATE SCHEMA {schemaname};'');
  * Criar uma credencial com âmbito de base de dados
    
          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Criar uma origem de dados externo para um blob de armazenamento do Azure
    
          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
    
          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * Crie um formato de ficheiro externo para um ficheiro csv. Os dados são descomprimidos e os campos são separados com o caráter de pipe.
    
          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (   
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS  
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Crie tabelas de viagem para o conjunto de dados do NYC taxi e fare externo no armazenamento de Blobs do Azure.
    
          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12     
          )  

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - Carregar dados de tabelas externas no armazenamento de Blobs do Azure ao SQL Data Warehouse

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Criar uma tabela de dados de exemplo (NYCTaxi_Sample) e inserir dados de selecionar as consultas SQL nas tabelas viagem e fare. (Alguns passos destas instruções tem de utilizar esta tabela de exemplo.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

A localização geográfica das suas contas de armazenamento afeta tempos de carregamento.

> [!NOTE]
> Consoante a localização geográfica da conta de armazenamento de BLOBs privada, o processo de cópia de dados de um blob público à sua conta de armazenamento privada pode demorar cerca de 15 minutos, ou mesmo tempo e o processo de carregamento dos dados da sua conta de armazenamento para o Azure Armazém de dados do SQL Server pode demorar de 20 minutos ou mais.  
> 
> 

Terá de decidir que efetue se tiver ficheiros de origem e de destino duplicados.

> [!NOTE]
> Se o. csv de ficheiros copiados do armazenamento de BLOBs público à sua conta de armazenamento de BLOBs privada já existe na sua conta de armazenamento de BLOBs privada, o AzCopy irá pedir-lhe se pretende substituí-los. Se não pretende substituí-los, entrada  **n**  quando lhe for pedido. Se pretender substituir **todos os** dos mesmos, de entrada **um** quando lhe for pedido. Também pode introduzir **y** para substituir os ficheiros. csv individualmente.
> 
> 

![Desenhar #21][21]

Pode utilizar os seus próprios dados. Se os dados na sua máquina no local na sua aplicação de vida real, pode utilizar o AzCopy para carregar os dados no local para o armazenamento de Blobs do Azure privado. Só precisa de alterar o **origem** localização, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, no comando AzCopy do ficheiro de script do PowerShell para o diretório local que contém os seus dados.

> [!TIP]
> Se os dados já existir no seu armazenamento de Blobs do Azure privada na sua aplicação de vida real, pode ignorar o passo AzCopy o script do PowerShell e diretamente carregar os dados para o armazém de dados do Azure SQL. Isto irá necessitar edições adicionais do script ao personalizá-lo para o formato dos seus dados.
> 
> 

Este script do Powershell também plugs as informações do armazém de dados do Azure SQL para os ficheiros de exemplo de exploração de dados SQLDW_Explorations.sql, SQLDW_Explorations.ipynb e SQLDW_Explorations_Scripts.py para que estes três ficheiros, estará pronto ser tentou instantaneamente após conclui o script do PowerShell.

Após uma execução bem sucedida, verá o ecrã, conforme mostrado abaixo:

![][20]

## <a name="dbexplore"></a>Exploração de dados e de engenharia da funcionalidade no Azure SQL Data Warehouse
Nesta secção, iremos executar geração de exploração e funcionalidade de dados através da execução de consultas SQL no armazém de dados do SQL do Azure diretamente utilizando **ferramentas de dados do Visual Studio**. Todas as consultas de SQL Server utilizadas nesta secção podem ser encontradas no script de exemplo com o nome *SQLDW_Explorations.sql*. Este ficheiro já tenha sido transferido para o diretório no local pelo script do PowerShell. Também pode obter a [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Mas o ficheiro no GitHub, não tem as informações do armazém de dados do Azure SQL Server ligadas.

Ligar ao seu armazém de dados de SQL do Azure com o Visual Studio com o nome de início de sessão do armazém de dados do SQL Server e a palavra-passe e abrir o **SQL Server Object Explorer** para confirmar a base de dados e tabelas foram importadas. Obter o *SQLDW_Explorations.sql* ficheiro.

> [!NOTE]
> Para abrir um editor de consultas do armazém de dados paralelo (PDW), utilize o **nova consulta** comando enquanto o PDW está selecionado no **SQL Server Object Explorer**. O editor de consultas standard do SQL Server não é suportado pelo PDW.
> 
> 

Seguem-se o tipo de dados efetuar tarefas de geração de exploração e funcionalidade nesta secção:

* Explore as distribuições de dados de alguns campos no variando intervalos de tempo.
* Investigue a qualidade dos dados dos campos de latitude e longitude.
* Gerar etiquetas de classificação de várias classes e binária com base no **sugestão\_quantidade**.
* Gerar funcionalidades e computação/comparar as distâncias de viagem.
* Associar as duas tabelas e extrair amostra aleatória que será utilizada para criar modelos.

### <a name="data-import-verification"></a>Verificação de importação de dados
Estas consultas fornecem uma verificação rápida do número de linhas e colunas nas tabelas povoadas anteriormente utilizando em massa de paralela do Polybase importar,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Saída:** deve obter 173,179,759 linhas e colunas de 14.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion
Esta consulta de exemplo identifica medallions (taxi números) concluir mais do que 100 viagens dentro do período de tempo especificado. A consulta seria beneficiar o acesso de tabela particionada necessitar, uma vez que este é ter pelo esquema de partição de **recolha\_datetime**. Consultar o conjunto de dados completo também faz com que utilize a tabela particionada e/ou a análise de índice.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Saída:** a consulta deverá devolver uma tabela com linhas especificando os 13,369 medallions (taxis) e o número de viagem concluída no 2013. A última coluna contém a contagem do número de viagens foi concluída.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploração: Distribuição de viagem medallion e hack_license
Neste exemplo identifica os medallions (taxi números) e hack_license números (controladores do) que foram concluídos mais do que 100 viagens durante um período de tempo especificado.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Saída:** a consulta deverá devolver uma tabela com 13,369 linhas especificando os IDs de carro/controlador 13,369 que concluíram mais que viagens 100 em 2013. A última coluna contém a contagem do número de viagens foi concluída.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: verificar os registos com incorreto longitude e/ou latitude
Neste exemplo investiga se qualquer um dos campos de longitude e/ou latitude ou contém um valor inválido (radian graus devem ser entre -90 e 90), ou ter (0, 0) coordenadas.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Saída:** a consulta devolve 837,467 viagens com campos de longitude e/ou latitude inválidos.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Tipped vs distribuição viagens não tipped
Neste exemplo localiza o número de viagens foram tipped vs. o número que não foram tipped num período de tempo especificado (ou no conjunto de dados completo se que abrangem o ano completo, que está definido aqui). Esta distribuição reflete a distribuição de etiqueta de binário para posteriormente, ser utilizada para a modelação de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Saída:** a consulta deverá devolver as seguintes frequências de sugestão para o ano 2013: 90,447,622 tipped e 82,264,709 tipped em não.

### <a name="exploration-tip-classrange-distribution"></a>Exploração: Distribuição de classe/intervalo de sugestão
Neste exemplo calcula a distribuição de intervalos de sugestão de um determinado período de tempo (ou no conjunto de dados completo se que abrangem o ano completo). Esta é a distribuição das classes de etiqueta que será posteriormente utilizada para a modelação de classificação de várias classes.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Saída:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Computação e comparar distância viagem
Neste exemplo converte a longitude de recolha e drop-off e pontos de latitude para geografia SQL, calcula a distância viagem utilizando a diferença de pontos de geografia SQL e devolve uma amostra aleatória de resultados para comparação. O exemplo limita os resultados para coordenadas válidos utilizando apenas a consulta de avaliação de qualidade de dados abrangida anteriormente.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Engenharia da funcionalidade utilizando funções do SQL Server
Por vezes, as funções do SQL Server podem ser uma opção eficiente para engenharia da funcionalidade. Nestas instruções, iremos definida uma função SQL para calcular a distância entre as localizações de recolha e dropoff direta. Pode executar os seguintes scripts de SQL no **ferramentas de dados do Visual Studio**.

Eis o script SQL que define a função de distâncias.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Eis um exemplo para chamar esta função para gerar funcionalidades na sua consulta SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Saída:** esta consulta gera uma tabela (com 2,803,538 linhas) com a recolha e dropoff latitudes e longitudes e correspondente direcionam as distâncias em quilómetros. Seguem-se os resultados do primeiro 3 linhas:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Preparar a criação de modelo de dados
A seguinte consulta associa a **nyctaxi\_viagem** e **nyctaxi\_fare** tabelas, gera uma etiqueta de classificação binária **tipped**, um etiqueta de classificação de classe Multi **sugestão\_classe**e extrai um exemplo do conjunto de dados associado ao total. A amostragem é feita ao aceder a um subconjunto de viagens com base na hora de recolha.  Esta consulta pode ser copiada e colada diretamente no [Azure Machine Learning Studio](https://studio.azureml.net) [importar dados] [ import-data] módulo para a ingestão de dados direta de instância de base de dados SQL no Azure. A consulta exclui registos com incorreto (0, 0) coordenadas.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Quando estiver pronto para continuar para o Azure Machine Learning, o utilizador pode optar por:  

1. Guardar a consulta SQL final para extrair os dados de exemplo e copiar-colar a consulta diretamente para um [importar dados] [ import-data] módulo no Azure Machine Learning, ou
2. Manter os dados da amostras e foi desenvolvidos planear utilizar o modelo de criação de uma nova tabela de armazém de dados do SQL Server e utilizar a nova tabela a [importar dados] [ import-data] módulo no Azure Machine Learning. O script do PowerShell no passo anterior execute este procedimento para si. Pode ler diretamente a partir desta tabela no módulo importar dados.

## <a name="ipnb"></a>Exploração de dados e de engenharia da funcionalidade no bloco de notas do IPython
Nesta secção, iremos efetuar exploração de dados e a geração de funcionalidade utilizando ambos os Python e as consultas SQL com o armazém de dados do SQL Server criado anteriormente. Um exemplo IPython bloco de notas com o nome **SQLDW_Explorations.ipynb** e um ficheiro de script de Python **SQLDW_Explorations_Scripts.py** tenham sido transferidas para o diretório no local. Também estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Estes dois ficheiros são idênticos em Python scripts. O ficheiro de script de Python é fornecido no caso de não dispõe de um servidor de IPython bloco de notas. Estes dois exemplos Python ficheiros concebidos em **Python 2.7**.

As informações de armazém de dados do Azure SQL necessárias na amostra IPython bloco de notas e o ficheiro de script de Python transferidos para o computador local tiverem sido ligadas pelo script do PowerShell anteriormente. São executáveis sem qualquer modificação.

Se já tiver configurado uma área de trabalho do AzureML, pode carregar o bloco de notas do IPython de exemplo para o serviço de bloco de notas do AzureML IPython diretamente e iniciar a executá-lo. Eis os passos para carregar para o bloco de notas do AzureML IPython serviço:

1. Inicie sessão na sua área de trabalho do AzureML, clique em "Studio" na parte superior e clique em "PORTÁTEIS" no lado esquerdo da página web.
   
    ![Desenhar #22][22]
2. Clique em "Novo" no canto inferior esquerdo da página web e selecione "Python 2". Em seguida, forneça um nome para o bloco de notas e clique na marca de verificação para criar o bloco de notas IPython nova em branco.
   
    ![Desenhar #23][23]
3. Clique no símbolo de "Jupyter" no canto superior esquerdo do novo bloco de notas do IPython.
   
    ![Desenhar #24][24]
4. Arrastar e largar o bloco de notas do IPython de exemplo para o **árvore** página do seu serviço de bloco de notas do AzureML IPython e clique em **carregar**. Em seguida, o exemplo o bloco de notas do IPython será carregado para o serviço de bloco de notas do AzureML IPython.
   
    ![Desenhar #25][25]

Para executar o exemplo IPython bloco de notas ou o Python ficheiro de script, o Python seguintes pacotes são necessários. Se estiver a utilizar o serviço de bloco de notas do AzureML IPython, estes pacotes tem sido previamente instalados.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

A sequência recomendada quando criar soluções analíticas avançadas em AzureML com dados de grandes dimensões é o seguinte:

* Ler um exemplo dos dados pequeno para um intervalo de dados em memória.
* Efetue algumas visualizações e explorations utilizando amostras de dados.
* Experimentar a engenharia da funcionalidade utilizando os amostras de dados.
* Exploração de dados maior, manipulação de dados e de engenharia da funcionalidade, utilize Python para emitir consultas de SQL diretamente com o armazém de dados do SQL Server.
* Decida o tamanho da amostra a ser adequadas para a criação de modelo do Azure Machine Learning.

O seguinte é alguns exploração de dados, visualização de dados e funcionalidade exemplos de engenharia. Mais explorations de dados podem ser encontrados na amostra IPython bloco de notas e o ficheiro de script de Python de exemplo.

### <a name="initialize-database-credentials"></a>Inicializar as credenciais da base de dados
Inicializar as definições de ligação de base de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Criar a ligação de base de dados
Segue-se a cadeia de ligação que cria a ligação à base de dados.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Número de relatório de linhas e colunas na tabela < nyctaxi_trip >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Número total de linhas = 173179759  
* Número total de colunas = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Número de relatório de linhas e colunas na tabela < nyctaxi_fare >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Número total de linhas = 173179759  
* Número total de colunas = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Leia-in de uma amostra de dados de pequena da base de dados do armazém de dados do SQL Server
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tempo até ao ler que a tabela de exemplo é 14.096495 segundos.  
Número de linhas e colunas obter = (1000, 21).

### <a name="descriptive-statistics"></a>Estatísticas descritivas
Agora, está pronto para explorar as amostras de dados. Vamos começar a utilizar ao procurar algumas estatísticas descritivas para o **viagem\_distância** (ou outros campos de optar por especificar).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualização: Exemplo de desenho de caixa
Em seguida, observe o desenho da caixa para a distância viagem visualizar o quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Desenhar #1][1]

### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo de desenho de distribuição
Plots visualizar a distribuição e um histograma para os distâncias viagem de amostragem.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Desenhar #2][2]

### <a name="visualization-bar-and-line-plots"></a>Barra de visualização: E rastreia de linha
Neste exemplo, vamos bin a distância viagem em cinco intervalos binários e visualizar os resultados binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Iremos pode representar a distribuição de reciclagem acima na barra de uma ou linha desenho com:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Desenhar #3][3]

e

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Desenhar #4][4]

### <a name="visualization-scatterplot-examples"></a>Visualização: Exemplos de Scatterplot
Vamos mostrar o desenho de gráfico de dispersão entre **viagem\_tempo\_no\_seg** e **viagem\_distância** para ver se existe qualquer correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Desenhar #6][6]

Da mesma forma, pode verificar a relação entre **taxa\_código** e **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Desenhar #8][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Exploração de dados em amostras de dados através de consultas SQL no bloco de notas do IPython
Nesta secção, vamos explorar as distribuições de dados utilizando as amostras de dados que são mantidas na nova tabela que criámos acima. Tenha em atenção que podem ser executadas explorations semelhantes utilizando as tabelas originais.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploração: Número de linhas e colunas na tabela de amostras de relatório
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploração: Tipped/não tripped distribuição
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Exploração: Distribuição de classe de sugestão
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Exploração: Desenhar a distribuição de sugestão pela classe
    tip_class_dist['tip_freq'].plot(kind='bar')

![Desenhar #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária viagens
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Viagem distribuição por medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploração: Viagem distribuição através da licença medallion e acesso
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Exploração: Distribuição de tempo de viagem
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Exploração: Distribuição de distância viagem
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Exploração: Distribuição de tipo de pagamento
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique o formato final da tabela featurized
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Criar modelos no Azure Machine Learning
Iremos agora está prontos continuar a criação de modelo e implementação de modelo no [Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos a ser utilizado em qualquer um dos problemas de predição identificados anteriormente, nomeadamente:

1. **Classificação binária**: prever ou não uma sugestão foi paga para uma viagem.
2. **Classificação de várias classes**: prever o intervalo de sugestão paga, de acordo com as classes definidas anteriormente.
3. **Tarefa de regressão**: para prever a quantidade de sugestão paga para uma viagem.  

Para iniciar o exercício modelação, inicie sessão no seu **Azure Machine Learning** área de trabalho. Se ainda não tiver criado uma área de trabalho de aprendizagem, consulte o artigo [criar uma área de trabalho do Azure ML](../studio/create-workspace.md).

1. Para começar a utilizar com o Azure Machine Learning, consulte o artigo [que é o Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Inicie sessão no [do Azure Machine Learning Studio](https://studio.azureml.net).
3. A página inicial do Studio fornece uma variedade de informações, vídeos, tutoriais, hiperligações para a referência de módulos e outros recursos. Para obter mais informações sobre o Azure Machine Learning, consulte o [Centro de documentação do Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Uma experimentação de preparação típico consiste dos seguintes passos:

1. Criar um **+ novo** experimentação.
2. Obter os dados no Azure ML.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gere funcionalidades conforme necessário.
5. Dividir os dados em conjuntos de dados de formação / / testes validação (ou tem conjuntos de dados separados para cada).
6. Selecione um ou mais algoritmos do machine learning, consoante o problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação de várias classes, regressão.
7. Preparar um ou mais modelos utilizando o conjunto de dados de formação.
8. Pontuar o conjunto de dados de validação utilizando o model(s) preparado.
9. Avalie o model(s) para calcular as métricas relevantes para o problema de aprendizagem.
10. Ajustar a otimizar o model(s) e selecione o modelo melhor a implementar.

Neste exercício, vamos já explorou e foi desenvolvido os dados no armazém de dados do SQL Server e decidir o tamanho da amostra para a ingestão de Azure ML. Eis o procedimento para criar um ou mais dos modelos de previsão:

1. Obter os dados do Azure ML utilizando o [importar dados] [ import-data] módulo, disponível no **dados de entrada e saída** secção. Para obter mais informações, consulte o [importar dados] [ import-data] página de referência do módulo.
   
    ![Dados de importação do Azure ML][17]
2. Selecione **SQL Database do Azure** como o **origem de dados** no **propriedades** painel.
3. Introduza o nome DNS da base de dados no **nome do servidor de base de dados** campo. Formato:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Introduza o **nome de base de dados** no campo correspondente.
5. Introduza o *nome de utilizador do SQL Server* no **nome de conta de utilizador do servidor**e o *palavra-passe* no **palavra-passe de conta de utilizador do**.
6. Verifique o **aceita qualquer certificado de servidor** opção.
7. No **consulta de base de dados** editar a área de texto, cole a consulta que extrai os campos de base de dados necessárias (incluindo quaisquer campos calculados, tais como as etiquetas) e baixo amostras de dados para o tamanho da amostra pretendido.

Um exemplo de uma experimentação de classificação binária ler os dados diretamente a partir da base de dados do SQL Data Warehouse é na imagem abaixo (não se esqueça de substituir o nyctaxi_trip de nomes de tabela e nyctaxi_fare pelo nome de esquema e os nomes de tabela que utilizou a explicação passo a passo). Podem ser construídas experimentações semelhantes para classificação de várias classes e problemas de regressão.

![Formação do Azure ML][10]

> [!IMPORTANT]
> Nos dados de modelação de extração e fazendo a amostragem de exemplos de consultas fornecidos nas secções anteriores, **todas as etiquetas para os exercícios modelação três estão incluídas na consulta**. É um passo importante (obrigatório) em cada um dos exercícios modelação **excluir** etiquetas desnecessárias para outros dois problemas e quaisquer outros **fugas de destino**. Por exemplo, quando utilizar a classificação do binária, utilize a etiqueta **tipped** e excluir os campos **sugestão\_classe**, **sugestão\_quantidade**e **total\_quantidade**. A última opção é fugas de destino, desde que o implica a sugestão paga.
> 
> Para excluir quaisquer colunas desnecessárias ou fugas de destino, pode utilizar o [selecionar colunas no conjunto de dados] [ select-columns] módulo ou a [Editar metadados][edit-metadata]. Para obter mais informações, consulte [selecionar colunas no conjunto de dados] [ select-columns] e [Editar metadados] [ edit-metadata] páginas de referência.
> 
> 

## <a name="mldeploy"></a>Implementar modelos no Azure Machine Learning
Quando o modelo estiver pronto, pode facilmente implementá-lo como um serviço web diretamente a partir de experimentação. Para obter mais informações sobre a implementação de serviços web do Azure ML, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Para implementar um novo serviço web, tem de:

1. Crie uma experimentação de classificação.
2. Implemente o serviço web.

Para criar uma experimentação de classificação de um **concluído** experimentação de preparação, clique em **criar classificação experimentação** na barra de ação inferior.

![A classificação do Azure][18]

O Azure Machine Learning irá tentar criar uma experimentação de classificação com base nos componentes da experimentação de preparação. Em particular, irá:

1. Guardar o modelo treinado e remova os módulos de formação do modelo.
2. Identificar uma lógica **porta de entrada** para representar o esquema de dados de entrada esperado.
3. Identificar uma lógica **porta de saída** para representar o esquema de saída do serviço web esperado.

Quando é criada a experimentação de classificação, reveja-lo e efetue a ajustar conforme necessário. É um ajuste típico para substituir o conjunto de dados de entrada e/ou a consulta com um que exclui os campos de etiqueta, como estes não estarão disponíveis quando o serviço é chamado. Também é uma boa prática para reduzir o tamanho do conjunto de dados de entrada e/ou a consulta para alguns registos, apenas suficiente para indicar o esquema da entrada. A porta de saída, é comum para excluir a entrada de todos os campos e incluir apenas o **etiquetas classificadas** e **probabilidades classificadas** em resultado, utilizando o [selecionar colunas no conjunto de dados] [ select-columns] módulo.

Um classificação experimentação de exemplo é fornecido na imagem abaixo. Quando estiver pronto para implementar, clique em de **publicar o serviço de WEB** botão na barra de ação inferior.

![A publicação do Azure ML][11]

## <a name="summary"></a>Resumo
Para recap podemos ter efetuado neste tutorial explicação passo a passo, criou um ambiente de ciência de dados do Azure, trabalhado com um grande conjunto de dados público, colocá-lo durante o processo de ciência de dados de equipa, até a partir de aquisição de dados para formação de modelo e, em seguida, para o implementação de um serviço web Azure Machine Learning.

### <a name="license-information"></a>Informações de licença
Estas instruções de exemplo e o respetivo que acompanha scripts e IPython notebook(s) são partilhados por Microsoft com a licença MIT. Verifique o ficheiro LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

## <a name="references"></a>Referências
• [Andrés Monroy NYC Taxi viagens página de transferência](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxi viagem dados por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi e Limousine Commission investigação e estatísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
