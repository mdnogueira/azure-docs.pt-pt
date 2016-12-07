---
title: "Tutorial: Criar um pipeline com a Atividade de Cópia com o Azure PowerShell | Microsoft Docs"
description: "Neste tutorial, irá criar um pipeline do Azure Data Factory com uma Atividade de Cópia com o Azure PowerShell."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/16/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: a4121f8857fa9eaeb1cf1bca70e29666f6a04f63
ms.openlocfilehash: 457a5c758923a0741ab0baeebd2f22c43930f71d


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-powershell"></a>Tutorial: Criar um pipeline com a Atividade de Cópia com o Azure PowerShell
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Neste tutorial, irá criar e monitorizar uma fábrica de dados do Azure com cmdlets do Azure PowerShell. O pipeline na fábrica de dados que criou neste tutorial utiliza uma Atividade de Cópia para copiar dados a partir de um blob do Azure para uma base de dados SQL do Azure.

A Atividade de Cópia executa o movimento de dados no Azure Data Factory. A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Veja o artigo [Atividades de Movimentos de Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.   

> [!IMPORTANT]
> Veja o artigo [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua os passos de **pré-requisitos** antes de executar este tutorial.
> 
> Este artigo não abrange todos os cmdlets do Data Factory. Veja [Referência dos Cmdlets do Data Factory](https://msdn.microsoft.com/library/dn820234.aspx) para obter a documentação completa sobre os cmdlets do Data Factory.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para além dos pré-requisitos listados no tópico Descrição Geral do Tutorial, tem de instalar o **Azure PowerShell**. Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar o Azure PowerShell no seu computador.

## <a name="in-this-tutorial"></a>Neste tutorial
A tabela seguinte lista os passos que irá efetuar como parte do tutorial e das suas descrições. 

| Passo | Descrição |
| --- | --- |
| [Criar um Azure Data Factory](#create-data-factory) |Neste passo, irá criar uma fábrica de dados do Azure com o nome **ADFTutorialDataFactoryPSH**. |
| [Criar serviços ligados](#create-linked-services) |Neste passo, irá criar dois serviços ligados: **StorageLinkedService** e **AzureSqlLinkedService**. O StorageLinkedService liga um armazenamento do Azure e o AzureSqlLinkedService liga uma base de dados SQL do Azure a ADFTutorialDataFactoryPSH. |
| [Criar conjuntos de dados de entrada e saída](#create-datasets) |Neste passo, irá definir dois conjuntos de dados (**EmpTableFromBlob** e **EmpSQLTable**). Estes conjuntos de dados são utilizados como tabelas de entrada e de saída para a **Atividade de Cópia** no ADFTutorialPipeline que irá criar no passo seguinte. |
| [Criar e executar um pipeline](#create-pipeline) |Neste passo, irá criar um pipeline com o nome **ADFTutorialPipeline** na fábrica de dados: **ADFTutorialDataFactoryPSH**. O pipeline tem uma **Atividade de Cópia** que copia dados a partir de um blob do Azure para uma tabela de base de dados do Azure de saída. |
| [Monitorizar conjuntos de dados e pipeline](#monitor-pipeline) |Neste passo, irá monitorizar os conjuntos de dados e o pipeline com o Azure PowerShell. |

## <a name="create-data-factory"></a>Criar fábrica de dados
Neste passo, utiliza o Azure PowerShell para criar um Azure Data Factory com o nome **ADFTutorialDataFactoryPSH**.

1. Inicie o **Azure PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.
   
   1. Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:
      
           Login-AzureRmAccount   
   2. Execute o comando seguinte para ver todas as subscrições desta conta:
      
           Get-AzureRmSubscription 
   3. Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua subscrição do Azure:
      
           Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
2. Crie um grupo de recursos do Azure com o nome: **ADFTutorialResourceGroup** ao executar o seguinte comando:
   
        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
   
    Alguns dos passos deste tutorial partem do princípio de que utiliza o grupo de recursos com o nome **ADFTutorialResourceGroup**. Se utiliza um grupo de recursos diferente, terá de utilizá-lo em vez de ADFTutorialResourceGroup neste tutorial. 
3. Execute o cmdlet **New-AzureRmDataFactory** para criar uma fábrica de dados com o nome: **ADFTutorialDataFactoryPSH**:  
   
        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o seguinte erro, altere o nome (por exemplo, oseunomeADFTutorialDataFactory). Utilize este nome em vez de ADFTutorialFactoryPSH ao realizar os passos deste tutorial. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
        Data factory name “ADFTutorialDataFactoryPSH” is not available
* Para criar instâncias do Data Factory, tem de ser um contribuidor/administrador da subscrição do Azure
* O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
* Se receber o erro: "**Esta subscrição não está registada para utilizar o espaço de nomes Microsoft.DataFactory**", realize um dos seguintes procedimentos e tente publicar novamente: 
  
  * No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory: 
    
          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
      Execute o seguinte comando para confirmar que o fornecedor do Data Factory está registado: 
    
          Get-AzureRmResourceProvider
  * Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory (ou) crie uma fábrica de dados no Portal do Azure. Esta ação regista automaticamente o fornecedor por si.

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Um arquivo de dados pode ser um Armazenamento do Azure, uma Base de Dados SQL do Azure ou uma base de dados SQL Server no local, que contém dados de entrada ou armazena dados de saída para um pipeline do Data Factory. Um serviço de computação é um serviço que processa dados de entrada e produz dados de saída. 

Neste passo, irá criar dois serviços ligados: **StorageLinkedService** e **AzureSqlLinkedService**. O serviço ligado StorageLinkedService liga uma Conta de Armazenamento do Azure e o AzureSqlLinkedService liga uma base de dados SQL do Azure à fábrica de dados: **ADFTutorialDataFactoryPSH**. Mais tarde neste tutorial, irá criar um pipeline que copia dados de um contentor de blobs no StorageLinkedService para uma tabela SQL no AzureSqlLinkedService.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Criar um serviço ligado para uma conta de armazenamento do Azure
1. Crie um ficheiro JSON com o nome **StorageLinkedService.json** em **C:\ADFGetStartedPSH** com o seguinte conteúdo. Se ainda não existir, crie a pasta ADFGetStartedPSH.
   
         {
               "name": "StorageLinkedService",
               "properties": {
                 "type": "AzureStorage",
                 "typeProperties": {
                       "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                 }
               }
         }
   
   Substitua **accountname** e **accountkey** pelo nome e chave da sua conta de armazenamento do Azure.
2. No **Azure PowerShell**, mude para a pasta **ADFGetStartedPSH**. 
3. Pode utilizar o cmdlet **New-AzureRmDataFactoryLinkedService** para criar um serviço ligado. Este cmdlet e os outros cmdlets do Data Factory que utilizar neste tutorial requerem que passe os valores para os parâmetros **ResourceGroupName** e **DataFactoryName**. Em alternativa, pode utilizar **Get-AzureRmDataFactory** para obter um objeto de DataFactory e passar o objeto sem escrever ResourceGroupName e DataFactoryName sempre que executar um cmdlet. Execute o seguinte comando para atribuir a saída do cmdlet **Get-AzureRmDataFactory** a uma variável: **$df**: 
   
```   
$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
```

4. Agora, execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço ligado: **StorageLinkedService**. 
   
```
New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
```

Se não tiver executado o cmdlet **Get-AzureRmDataFactory** e atribuído a saída à variável **$df**, terá de especificar valores para os parâmetros ResourceGroupName e DataFactoryName da seguinte forma.   
   
```
New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
```

Se fechar o Azure PowerShell a meio do tutorial, tem de executar o cmdlet Get-AzureRmDataFactory, da próxima vez que iniciar o Azure PowerShell, para concluir o tutorial.

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Criar um serviço ligado para uma SQL Database do Azure
1. Crie um ficheiro JSON denominado AzureSqlLinkedService.json com o seguinte conteúdo:
   
         {
             "name": "AzureSqlLinkedService",
             "properties": {
                 "type": "AzureSqlDatabase",
                 "typeProperties": {
                       "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                 }
               }
         }
   
   Substitua **servername**, **databasename**, **username@servername** e **password** pelos nomes do seu servidor, base de dados, conta de utilizador e palavra-passe do SQL do Azure.
2. Execute o seguinte comando para criar um serviço ligado: 
   
```
New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
```

   Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada para o seu servidor SQL do Azure. Para verificar e ativá-la, siga os seguintes passos:
   
   1. Clique no hub **PROCURAR** à esquerda e clique em **servidores SQL**.
   2. Selecione o servidor e clique em **DEFINIÇÕES** no painel do SQL SERVER.
   3. No painel **DEFINIÇÕES**, clique em **Firewall**.
   4. No painel **Definições da firewall**, clique em **ATIVAR** para **Permitir acesso aos serviços do Azure**.
   5. Clique no hub **ATIVAR** hub à esquerda para mudar para o painel **Data Factory** onde estava.

## <a name="create-datasets"></a>Criar conjuntos de dados
No passo anterior, criou serviços ligados **StorageLinkedService** e **AzureSqlLinkedService** para ligar uma Conta de armazenamento do Azure e a SQL Database do Azure a uma fábrica de dados: **ADFTutorialDataFactoryPSH**. Neste passo, irá criar conjuntos de dados que representam os dados de entrada e saída para a Atividade de Cópia no pipeline que vai criar no próximo passo. 

Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados suportado atualmente. A tabela de entrada neste tutorial refere-se a um contentor de blobs no armazenamento do Azure para o qual o StorageLinkedService aponta. A tabela de saída refere-se a uma tabela do SQL na base de dados SQL do Azure para a qual o AzureSqlLinkedService aponta.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Preparar o Blob Storage do Azure e a SQL Database do Azure para o tutorial
Ignorar este passo se já leu o tutorial do artigo [Copiar dados a partir do Armazenamento de Blobs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Realize os seguintes passos para preparar o armazenamento de blobs do Azure e a base de dados SQL do Azure para este tutorial. 

* Crie um contentor do blob com o nome **adftutorial** no armazenamento de blobs do Azure para o qual o **StorageLinkedService** aponta. 
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

    Se tiver instalado no seu computador o SQL Server 2014: siga as instruções do artigo [Passo 2: Ligar à Base de Dados SQL da Base de Dados SQL do Azure de Gestão com o SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md) para estabelecer ligação ao servidor SQL do Azure e executar o script SQL.

    Se o cliente não tiver permissão para aceder ao servidor SQL do Azure, terá de configurar a firewall do seu servidor SQL do Azure para permitir o acesso a partir do seu computador (Endereço IP). Veja [este artigo](../sql-database/sql-database-configure-firewall-settings.md) para obter os passos para configurar a firewall para o seu servidor SQL do Azure.

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
Uma tabela é um conjunto de dados retangular com um esquema. Neste passo, irá criar uma tabela com o nome **EmpBlobTable** que aponta para um contentor de blobs no Armazenamento do Azure, representado pelo serviço ligado **StorageLinkedService**. Este contentor do blob (**adftutorial**) contém os dados de entrada no ficheiro: **emp.txt**. 

1. Crie um ficheiro JSON com o nome **EmpBlobTable.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo:
   
         {
           "name": "EmpTableFromBlob",
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
             "linkedServiceName": "StorageLinkedService",
             "typeProperties": {
               "fileName": "emp.txt",
               "folderPath": "adftutorial/",
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
   
   Tenha em atenção os seguintes pontos: 
   
   * O **type** do conjunto de dados está definido como **AzureBlob**.
   * O **linkedServiceName** está definido como **StorageLinkedService**. 
   * O **folderPath** está definido para o contentor **adftutorial**. 
   * O **fileName** está definido como **emp.txt**. Se não especificar o nome do blob, os dados de todos os blobs no contentor são considerados dados de entrada.  
   * O **type** do formato está definido como **TextFormat**
   * Existem dois campos no ficheiro de texto – **FirstName** e **LastName** – separados por vírgula (**columnDelimiter**)    
   * A **disponibilidade** está definida como **de hora a hora** (**a frequência** está definida como **hora** e o **intervalo** está definido como **1**). Assim, o Data Factory procura dados de entrada a cada hora na pasta raiz no contentor de blobs (**adftutorial**).
   
   Se não especificar um **fileName** para uma **tabela de** **entrada**, todos os ficheiros/blobs da pasta de entrada (**folderPath**) são considerados entradas. Se especificar um fileName no JSON, apenas o ficheiro/blob especificado é considerado uma entrada asn. 
   
   Se não especificar um **fileName** para uma **tabela de saída**, os ficheiros gerados no **folderPath** recebem um nome com o seguinte formato: Data.<Guid\>.txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).
   
   Para definir **folderPath** e **fileName** de forma dinâmica com base no tempo **SliceStart**, utilize a propriedade **partitionedBy**. No exemplo seguinte, o folderPath utiliza o Ano, o Mês e o Dia do SliceStart (hora de início do setor a ser processado) e o fileName utiliza a Hora do SliceStart. Por exemplo, se estiver a ser produzido um setor para 2016-10-20T08:00:00, o folderName deverá ser definido como wikidatagateway/wikisampledataout/2016/10/20 e o fileName definido como 08.csv. 
   
         "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
         "fileName": "{Hour}.csv",
         "partitionedBy": 
         [
             { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
             { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
             { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
             { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
         ],
   
   Veja [Referência de Processamento de Scripts JSON](data-factory-data-movement-activities.md) para obter mais detalhes sobre as propriedades de JSON.
2. Execute o seguinte comando para criar o conjunto de dados do Data Factory.
   
```  
New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
```

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Neste passo, irá criar um conjunto de dados de saída com o nome **EmpSQLTable**. Este conjunto de dados aponta para uma tabela SQL (**emp**) na base de dados SQL do Azure, representada por **AzureSqlLinkedService**. O pipeline copia dados do blob de entrada para a tabela **emp**. 

1. Crie um ficheiro JSON denominado **EmpSQLTable.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo:
   
         {
           "name": "EmpSQLTable",
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
   
   Tenha em atenção os seguintes pontos: 
   
   * O **type** do conjunto de dados está definido como **AzureSqlTable**.
   * O **linkedServiceName** está definido como **AzureSqlLinkedService**.
   * O **tablename** está definido como **emp**.
   * Existem três colunas – **ID**, **FirstName**, e **LastName** – na tabela emp da base de dados. O ID é uma coluna de identidade, por isso terá de especificar apenas **FirstName** e **LastName** aqui.
   * A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço Data Factory gera um setor de dados de saída a cada hora na tabela **emp** da base de dados SQL do Azure.
2. Execute o seguinte comando para criar o conjunto de dados do Data Factory. 

```   
New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
```

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, cria um pipeline com uma **Atividade de Cópia** que utiliza **EmpTableFromBlob** como entrada e **EmpSQLTable** como saída.

1. Crie um ficheiro JSON com o nome **ADFTutorialPipeline.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo: 
   
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
                     "name": "EmpTableFromBlob"
                   }
                 ],
                 "outputs": [
                   {
                     "name": "EmpSQLTable"
                   }
                 ],
                 "typeProperties": {
                   "source": {
                     "type": "BlobSource"
                   },
                   "sink": {
                     "type": "SqlSink"
                   }
                 },
                 "Policy": {
                   "concurrency": 1,
                   "executionPriorityOrder": "NewestFirst",
                   "style": "StartOfInterval",
                   "retry": 0,
                   "timeout": "01:00:00"
                 }
               }
             ],
             "start": "2016-08-09T00:00:00Z",
             "end": "2016-08-10T00:00:00Z",
             "isPaused": false
           }
         }
   
   Tenha em atenção os seguintes pontos:
   
   * Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**.
   * A entrada da atividade está definida como **EmpTableFromBlob** e a saída da atividade está definida como **EmpSQLTable**.
   * Na secção **transformação**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink.
   
   Substitua o valor da propriedade **start** com o dia atual e o valor **end** com o dia seguinte. Tanto o datetime de início como de fim têm de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial. 
   
   Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9/9/9999** como o valor da propriedade **end**.
   
   Existem 24 setores de dados no exemplo, uma vez que estes são produzidos de hora a hora.
   
   Veja [Referência de Processamento de Scripts JSON](data-factory-data-movement-activities.md) para obter mais detalhes sobre as propriedades de JSON.
2. Execute o seguinte comando para criar a tabela do Data Factory. 

```   
New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
```

**Parabéns!** Criou um Azure Data Factory, serviços ligados, tabelas e um pipeline com êxito e agendou o pipeline.

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, irá utilizar o Azure PowerShell para monitorizar os acontecimentos de uma fábrica de dados do Azure.

1. Execute **Get-AzureRmDataFactory** e atribua o resultado a uma variável $df.

```  
$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
```

2. Execute **Get-AzureRmDataFactorySlice** para obter detalhes sobre todos os setores do **EmpSQLTable**, que é a tabela de saída do pipeline.  

```   
Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
```

   Substitua o ano, mês e parte da data do parâmetro **StartDateTime** com a data, o mês e ano atuais. Esta definição deve corresponder ao valor **Start** no JSON do pipeline. 
   
   Deve ver 24 setores, um para cada hora, desde as 12:00 do dia atual até as 12:00 do dia seguinte. 
   
   **Resultado do exemplo:**

```   
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
```
3. Execute **Get-AzureRmDataFactoryRun** para obter os detalhes das execuções de um setor **específico** da atividade. Altere o valor do parâmetro **StartDateTime** para corresponder ao tempo **Start** do setor da saída. O valor de **StartDateTime** tem de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). 

```  
Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
```

   Deverá ver um resultado semelhante ao seguinte:

```   
     Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
     ResourceGroupName   : ADFTutorialResourceGroup
     DataFactoryName     : ADFTutorialDataFactoryPSH
     TableName           : EmpSQLTable
     ProcessingStartTime : 8/9/2016 11:03:28 PM
     ProcessingEndTime   : 8/9/2016 11:04:36 PM
     PercentComplete     : 100
     DataSliceStart      : 8/9/2016 10:00:00 PM
     DataSliceEnd        : 8/9/2016 11:00:00 PM
     Status              : Succeeded
     Timestamp           : 8/9/2016 11:03:28 PM
     RetryAttempt        : 0
     Properties          : {}
     ErrorMessage        :
     ActivityName        : CopyFromBlobToSQL
     PipelineName        : ADFTutorialPipeline
     Type                : Copy
```

Veja [Referência Cmdlets do Data Factory][cmdlet-reference] para obter a documentação completa sobre os cmdlets do Data Factory. 

## <a name="summary"></a>Resumo
Neste tutorial, vai criar um Azure Data Factory para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Utilizou o PowerShell para criar a fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Eis os passos de alto nível que realizou neste tutorial:  

1. Criou uma **fábrica de dados** do Azure.
2. Criou **serviços ligados**:
   1. Um serviço ligado do ** Storagedo Azure** para ligar a sua Conta de armazenamento do Azure que contém dados de entrada.     
   2. Um serviço ligado do **SQL do Azure** para ligar a sua base de dados SQL do Azure que contém dados de saída. 
3. Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4. Criou um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como sink. 

## <a name="see-also"></a>Veja Também
| Tópico | Descrição |
|:--- |:--- |
| [Atividades de Movimento de Dados](data-factory-data-movement-activities.md) |Este artigo fornece informações detalhadas sobre a Atividade de Cópia que utilizou no tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. |

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



<!--HONumber=Nov16_HO2-->


