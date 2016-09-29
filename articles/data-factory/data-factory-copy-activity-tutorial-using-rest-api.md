<properties 
    pageTitle="Tutorial: Criar um pipeline com a Atividade de Cópia com a API REST | Microsoft Azure" 
    description="Neste tutorial, vai criar um pipeline do Azure Data Factory com uma Atividade de Cópia com a API REST." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/16/2016" 
    ms.author="spelluru"/>


# Tutorial: Criar um pipeline com a Atividade de Cópia com a API REST
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)

Este tutorial mostra-lhe como criar e monitorizar uma fábrica de dados do Azure com a API REST. O pipeline na fábrica de dados utiliza uma Atividade de Cópia para copiar dados do Armazenamento de Blobs do Azure para uma Base de Dados SQL do Azure.

> [AZURE.NOTE] 
> Este artigo não abrange toda a API REST do Data Factory. Veja [Referência da API REST do Data Factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) para obter a documentação completa sobre os cmdlets do Data Factory.
  

## Pré-requisitos

- Leia o artigo [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua os passos de **pré-requisitos**.
- Instale o [Curl](https://curl.haxx.se/dlwiz/) no seu computador. Utilize a ferramenta Curl com comandos REST para criar uma fábrica de dados. 
- Siga as instruções [neste artigo](../resource-group-create-service-principal-portal.md) para: 
    1. Criar uma aplicação Web com o nome **ADFCopyTutorialApp** no Azure Active Directory.
    2. Obter o **ID de cliente** e a **chave secreta**. 
    3. Obter o **ID de inquilino**. 
    4. Atribuir a aplicação **ADFCopyTutorialApp** à função **Contribuinte do Data Factory**.  
- Instale o [Azure PowerShell](../powershell-install-configure.md).  
- Inicie o **PowerShell** e execute o seguinte comando. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.
    1. Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no portal do Azure.
    
            Login-AzureRmAccount   
    2. Execute o comando seguinte para ver todas as subscrições para esta conta.

            Get-AzureRmSubscription 
    3. Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua subscrição do Azure. 

            Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    1. Crie um grupo de recursos do Azure com o nome **ADFTutorialResourceGroup**, executando o comando seguinte no PowerShell.  

            New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

        Se o grupo de recursos já existir, especifique se pretende atualizá-lo (Y) ou mantê-lo como está (N). 

        Alguns dos passos deste tutorial pressupõe que utiliza o grupo de recursos com o nome ADFTutorialResourceGroup. Se utilizar um grupo de recursos diferente, terá de utilizar o nome do grupo de recursos em vez de ADFTutorialResourceGroup neste tutorial.
  
## Criar definições JSON
Crie os seguintes ficheiros JSON na pasta onde está localizado curl.exe. 

### datafactory.json 
> [AZURE.IMPORTANT] O Nome tem de ser globalmente exclusivo, pelo que poderá querer atribuir um prefixo/sufixo ao ADFCopyTutorialDF para torná-lo um nome exclusivo. 

    {  
        "name": "ADFCopyTutorialDF",  
        "location": "WestUS"
    }  

### azurestoragelinkedservice.json
> [AZURE.IMPORTANT] Substitua **accountname** e **accountkey** pelo nome e chave da sua conta de armazenamento do Azure. Para saber como obter a chave de acesso ao armazenamento, veja [Ver, copiar e regenerar as chaves de acesso ao armazenamento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

### azuersqllinkedservice.json
> [AZURE.IMPORTANT] Substitua **servername**, **databasename**, **username** e **password** pelos nomes do seu servidor SQL do Azure, nome da base de dados SQL, conta de utilizador e palavra-passe da conta.  

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }


### inputdataset.json

    {
      "name": "AzureBlobInput",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

A definição JSON define um conjunto de dados com o nome **AzureBlobInput**, que representa dados de entrada para uma atividade no pipeline. Além disso, especifica que os dados de entrada estão localizados no ficheiro **emp.txt** que está no do contentor de blobs **adftutorial**. 

 Tenha em atenção os seguintes pontos: 

- O **type** do conjunto de dados está definido como **AzureBlob**.
- O **linkedServiceName** está definido como **AzureStorageLinkedService**. 
- O **folderPath** está definido como o contentor de **adftutorial** e o **fileName** está definido como **emp.txt**.  
- O **type** do formato está definido como **TextFormat**
- Existem dois campos no ficheiro de texto – **FirstName** e **LastName** – separados por vírgula (**columnDelimiter**) 
- A **disponibilidade** está definida como **de hora a hora** (a frequência está definida como hora e o intervalo está definido como 1). Por conseguinte, o Data Factory procura dados de entrada a cada hora na pasta raiz do contentor de blobs especificado (**adftutorial**). 

Se não especificar um **fileName** para um conjunto de dados de entrada, todos os ficheiros/blobs da pasta de entrada (**folderPath**) são considerados entradas. Se especificar um fileName no JSON, apenas o ficheiro/blob especificado é considerado uma entrada.

Se não especificar um **fileName** para uma **tabela de saída**, os ficheiros gerados no **folderPath** recebem um nome com o seguinte formato: Data.&lt;Guid&gt;.txt (example: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Para definir **folderPath** e **fileName** de forma dinâmica com base no tempo **SliceStart**, utilize a propriedade **partitionedBy**. No exemplo seguinte, o folderPath utiliza o Ano, o Mês e o Dia do SliceStart (hora de início do setor a ser processado) e o fileName utiliza a Hora do SliceStart. Por exemplo, se está a ser produzido um setor para 2014-10-20T08:00:00, folderName está definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName está definido como 08.csv. 

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],


### outputdataset.json
    
    {
      "name": "AzureSqlOutput",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "emp"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


A definição JSON define um conjunto de dados com o nome **AzureSqlOutput**, que representa os dados de saída para uma atividade no pipeline. Além disso, especifica que os resultados são armazenados na tabela: **emp** na base de dados representada pelo AzureSqlLinkedService. A secção **disponibilidade** especifica que o conjunto de dados de saída é produzido de hora a hora (frequência: hora e intervalo: 1).

Tenha em atenção os seguintes pontos: 

- O **type** do conjunto de dados está definido como **AzureSQLTable**.
- O **linkedServiceName** está definido como **AzureSqlLinkedService**.
- O **tablename** está definido como **emp**.
- Existem três colunas – **ID**, **FirstName**, e **LastName** – na tabela emp da base de dados. O ID é uma coluna de identidade, por isso terá de especificar apenas **FirstName** e **LastName** aqui.
- A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço Data Factory gera um setor de dados de saída a cada hora na tabela **emp** da base de dados SQL do Azure.

### pipeline.json

    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-08-12T00:00:00Z",
        "end": "2016-08-13T00:00:00Z"
      }
    }


Tenha em atenção os seguintes pontos:

- Na secção atividades, existe apenas uma atividade cujo **type** está definido como **CopyActivity**.
- A entrada da atividade está definida como **AzureBlobInput** e a saída da atividade está definida como **AzureSqlOutput**.
- Na secção **transformação**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink.

Substitua o valor da propriedade **start** com o dia atual e o valor **end** com o dia seguinte. Pode especificar apenas a parte da data e ignorar a parte da hora em data e hora. Por exemplo, “2015-02-03”, que é equivalente a “2015-02-03T00:00:00Z”

Tanto o datetime de início como de fim têm de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial. 

Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**.

Existem 24 setores de dados no exemplo, uma vez que estes são produzidos de hora a hora.
    
> [AZURE.NOTE] Veja [Anatomy of a Pipeline (Anatomia de um Pipeline)](data-factory-create-pipelines.md#anatomy-of-a-pipeline) para obter detalhes sobre as propriedades de JSON utilizadas no exemplo anterior.

## Definir variáveis globais

No Azure PowerShell, execute os seguintes comandos após substituir os valores pelos seus:

> [AZURE.IMPORTANT] Consulte a secção [Pré-requisitos](#prerequisites) para instruções sobre como obter o ID de cliente, o segredo do cliente, o ID de inquilino e o ID da subscrição.   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "ADFCopyTutorialDF"

## Autenticar com o AAD 
Execute o seguinte comando para autenticar com o Azure Active Directory (AAD). 

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 

## Criar fábrica de dados

Neste passo, irá criar uma fábrica de dados do Azure com o nome **ADFCopyTutorialDF**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino. Uma atividade do HDInsight Hive para executar um script do Hive, para transformar dados de entrada de modo a produzir dados de saída. Execute os seguintes comandos para criar a fábrica de dados: 

1. Atribua o comando à variável com o nome **cmd**. 

    Confirme que o nome da fábrica de dados que especificar aqui (ADFCopyTutorialDF) corresponde ao nome especificado em **datafactory.json**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. Execute o comando com **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Veja os resultados. Se a fábrica de dados tiver sido criada com êxito, irá ver o JSON da fábrica de dados em **resultados**; caso contrário, verá uma mensagem de erro.  

        Write-Host $results

Tenha em atenção os seguintes pontos:
 
- O nome do Azure Data Factory deve ser globalmente exclusivo. Se vir o erro nos resultados: **Nome da fábrica de dados “ADFCopyTutorialDF” não disponível**, realize os seguintes passos:  
    1. Altere o nome (por exemplo, oseunomeADFCopyTutorialDF) no ficheiro **datafactory.json**.
    2. No primeiro comando em que está atribuído um valor à variável **$cmd**, substitua ADFCopyTutorialDF pelo novo nome e execute o comando. 
    3. Execute os dois comandos seguintes para invocar a API REST, para criar a fábrica de dados e imprimir os resultados da operação. 
    
    Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
- Para criar instâncias do Data Factory, tem de ser um contribuidor/administrador da subscrição do Azure
- O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
- Se receber o erro: “**Esta subscrição não está registada para utilizar o espaço de nomes Microsoft.DataFactory**”, realize um dos seguintes procedimentos e tente publicar novamente: 

    - No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Pode executar o seguinte comando para confirmar que o fornecedor do Data Factory está registado. 
    
            Get-AzureRmResourceProvider
    - Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory (ou) crie uma fábrica de dados no Portal do Azure. Esta ação regista automaticamente o fornecedor por si.

Antes de criar um pipeline, deve primeiro criar algumas entidades do Data Factory. Primeiro, crie serviços ligados para ligar os arquivos de dados de origem e de destino ao arquivo de dados. Em seguida, defina conjuntos de dados de entrada e de saída para representar dados nos arquivos de dados ligados. Por fim, crie o pipeline com uma atividade que utiliza estes conjuntos de dados.

## Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Um arquivo de dados pode ser um Armazenamento do Azure, uma Base de Dados SQL do Azure ou uma base de dados SQL Server no local, que contém dados de entrada ou armazena dados de saída para um pipeline do Data Factory. Um serviço de computação é um serviço que processa dados de entrada e produz dados de saída. 

Neste passo, irá criar dois serviços ligados: **AzureStorageLinkedService** e **AzureSqlLinkedService**. O serviço ligado AzureStorageLinkedService liga uma Conta de Armazenamento do Azure e o AzureSqlLinkedService liga uma base de dados SQL do Azure à fábrica de dados: **ADFCopyTutorialDF**. Mais tarde neste tutorial, irá criar um pipeline que copia dados de um contentor de blobs no AzureStorageLinkedService para uma tabela SQL no AzureSqlLinkedService.

### Criar o serviço ligado do Storage do Azure
Neste passo, vai ligar a sua conta de Armazenamento do Azure à fábrica de dados. Com este tutorial, utilize a mesma conta de Armazenamento do Azure para armazenar dados de entrada. 

1. Atribua o comando à variável com o nome **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Execute o comando com **Invoke-Command**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Veja os resultados. Se o serviço ligado tiver sido criado com êxito, consulte o JSON do serviço ligado em **resultados**; caso contrário, verá uma mensagem de erro.
  
        Write-Host $results

### Criar serviço ligado SQL do Azure
Neste passo, vai ligar a sua base de dados SQL do Azure à fábrica de dados. Com este tutorial, utilize a mesma base de dados SQL do Azure para armazenar os dados de saída.

1. Atribua o comando à variável com o nome **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. Execute o comando com **Invoke-Command**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Veja os resultados. Se o serviço ligado tiver sido criado com êxito, consulte o JSON do serviço ligado em **resultados**; caso contrário, verá uma mensagem de erro.
  
        Write-Host $results

## Criar conjuntos de dados

No passo anterior, criou serviços ligados **AzureStorageLinkedService** e **AzureSqlLinkedService** para ligar uma conta de Armazenamento do Azure e a Base de Dados SQL do Azure à fábrica de dados: **ADFCopyTutorialDF**. Neste passo, irá criar conjuntos de dados que representam os dados de entrada e saída para a Atividade de Cópia no pipeline que vai criar no próximo passo. 

O conjunto de dados de entrada neste tutorial refere-se a um contentor de blobs no Armazenamento do Azure para o qual o AzureStorageLinkedService aponta. O conjunto de dados de saída refere-se a uma tabela do SQL na base de dados SQL do Azure para a qual o AzureSqlLinkedService aponta.  

### Preparar o Blob Storage do Azure e a SQL Database do Azure para o tutorial
Realize os seguintes passos para preparar o armazenamento de blobs do Azure e a base de dados SQL do Azure para este tutorial. 
 
* Crie um contentor de blobs com o nome **adftutorial** no armazenamento de blobs do Azure para o qual o **AzureStorageLinkedService** aponta. 
* Crie e carregue um ficheiro de texto, **emp.txt**, como um blob para o contentor **adftutorial**. 
* Crie uma tabela com o nome **emp** na SQL Database do Azure para a qual o **AzureSqlLinkedService** aponta.


1. Inicie o Bloco de notas, cole o seguinte texto e guarde-o como **emp.txt** na pasta **C:\ADFGetStartedPSH** no seu disco rígido. 

        John, Doe
        Jane, Doe
                
2. Utilize ferramentas, como o [Explorador do Storage do Azure](https://azurestorageexplorer.codeplex.com/), para criar o contentor **adftutorial** e carregar o ficheiro **emp.txt** para o contentor.

    ![Explorador do Storage do Azure](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. Utilize o seguinte script SQL para criar a tabela **emp** na SQL Database do Azure.  


        CREATE TABLE dbo.emp 
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

    Se tiver instalado no seu computador o SQL Server 2014: siga as instruções do [Passo 2: Ligar à Base de Dados SQL da SQL Database do Azure de Gestão com o artigo SQL Server Management Studio][sql-management-studio] para estabelecer ligação com o servidor SQL do Azure e executar o script de SQL.

    Se o cliente não tiver permissão para aceder ao servidor SQL do Azure, terá de configurar a firewall do seu servidor SQL do Azure para permitir o acesso a partir do seu computador (Endereço IP). Veja [este artigo](../sql-database/sql-database-configure-firewall-settings.md) para obter os passos para configurar a firewall para o seu servidor SQL do Azure.
        
### Criar conjunto de dados de entrada 
Neste passo, irá criar um conjunto de dados com o nome **AzureBlobInput** que aponta para um contentor de blobs no Armazenamento do Azure representado pelo serviço ligado **AzureStorageLinkedService**. Este contentor do blob (**adftutorial**) contém os dados de entrada no ficheiro: **emp.txt**. 

1. Atribua o comando à variável com o nome **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Execute o comando com **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Veja os resultados. Se o conjunto de dados tiver sido criado com êxito, consulte o JSON do conjunto de dados em **resultados**; caso contrário, verá uma mensagem de erro.
  
        Write-Host $results

### Criar conjunto de dados de saída
Neste passo, irá criar uma tabela de saída com o nome **AzureSqlOutput**. Este conjunto de dados aponta para uma tabela SQL (**emp**) na base de dados SQL do Azure, representada por **AzureSqlLinkedService**. O pipeline copia dados do blob de entrada para a tabela **emp**. 

1. Atribua o comando à variável com o nome **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. Execute o comando com **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Veja os resultados. Se o conjunto de dados tiver sido criado com êxito, consulte o JSON do conjunto de dados em **resultados**; caso contrário, verá uma mensagem de erro.
  
        Write-Host $results 

## Criar pipeline
Neste passo, irá criar um pipeline com uma **Atividade de Cópia** que utiliza **AzureBlobInput** como entrada e **AzureSqlOutput** como saída.

1. Atribua o comando à variável com o nome **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Execute o comando com **Invoke-Command**.

        $results = Invoke-Command -scriptblock $cmd;
3. Veja os resultados. Se o conjunto de dados tiver sido criado com êxito, consulte o JSON do conjunto de dados em **resultados**; caso contrário, verá uma mensagem de erro.  

        Write-Host $results

**Parabéns!** Criou com êxito uma fábrica de dados do Azure, com um pipeline que copia dados do armazenamento de blobs do Azure para a base de dados SQL do Azure.

## Monitorizar o pipeline
Neste passo, utilize a API REST do Data Factory para monitorizar os setores produzidos pelo pipeline.

    $ds ="AzureSqlOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;


    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }

Execute estes comandos até ver um setor no estado **Pronto** ou no estado **Falhou**. Quando o setor estiver no estado Pronto, verifique a tabela **emp** na base de dados SQL do Azure para os dados de saída. 

Para cada setor, duas linhas de dados do ficheiro de origem são copiadas para a tabela emp na base de dados SQL do Azure. Por conseguinte, verá 24 novos registos na tabela emp quando todos os setores tiverem sido processados com êxito (no estado Pronto). 


## Resumo
Neste tutorial, utilizou uma API REST para criar uma fábrica de dados do Azure para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Eis os passos de alto nível que realizou neste tutorial:  

1.  Criou uma **fábrica de dados** do Azure.
2.  Criou **serviços ligados**:
    1. Um serviço ligado do Armazenamento do Azure para ligar a sua conta de Armazenamento do Azure que contém os dados de entrada.    
    2. Um serviço ligado SQL do Azure para ligar a sua base de dados SQL do Azure que contém os dados de saída. 
3.  Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4.  Criou um **pipeline** com uma Atividade de Cópia com BlobSource como origem e SqlSink como sink. 

## Consultar Também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de Movimento de Dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a Atividade de Cópia que utilizou no tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como os utilizar para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory.
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. 



[use-custom-activities]: data-factory-use-custom-activities.md
[resolução de problemas]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 


<!--HONumber=Sep16_HO3-->


