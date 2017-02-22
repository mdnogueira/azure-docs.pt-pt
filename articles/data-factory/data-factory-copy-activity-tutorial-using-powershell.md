---
title: 'Tutorial: Criar um pipeline para mover dados com o Azure PowerShell | Microsoft Docs'
description: "Neste tutorial, vai utilizar o Azure PowerShell para criar um pipeline do Azure Data Factory com a Atividade de Cópia."
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
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: fbf77e9848ce371fd8d02b83275eb553d950b0ff
ms.openlocfilehash: a95e65db804f1c6cc2927901216ee7a287a911ee


---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Tutorial: Criar um pipeline do Data Factory que move os dados com o Azure PowerShell
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

Neste tutorial, vai utilizar cmdlets do Azure PowerShell para criar e monitorizar uma instância do Azure Data Factory. O pipeline na fábrica de dados que vai criar neste tutorial utiliza uma atividade de cópia para copiar dados a partir de um blob do Azure para uma base de dados SQL do Azure.

A funcionalidade Atividade de Cópia executa o movimento de dados no Data Factory. A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Veja [Data Movement Activities (Atividades de Movimentos de Dados)](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.   

> [!NOTE]
> Este artigo não abrange todos os cmdlets do Data Factory. Veja [Data Factory Cmdlet Reference (Referência aos Cmdlets do Data Factory)](/powershell/resourcemanager/azurerm.datafactories/v2.5.0/azurerm.datafactories) para obter a documentação completa sobre estes cmdlets.
>
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Não transforma dados de entrada para produzir dados de saída. Para ver um tutorial sobre como transformar dados através do Azure Data Factory, consulte [Tutorial: Build a pipeline to transform data using Hadoop cluster (Tutorial: Criar um pipeline para transformar dados com o cluster do Hadoop)](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos
- Leia a [Descrição Geral e Pré-requisitos do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter uma descrição geral do tutorial e concluir os passos de **pré-requisitos**.
- Instale o Azure PowerShell. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../powershell-install-configure.md).

## <a name="in-this-tutorial"></a>Neste tutorial
A tabela seguinte lista os passos que vai efetuar como parte do tutorial.

| Passo | Descrição |
| --- | --- |
| [Criar uma fábrica de dados do Azure Data Factory](#create-data-factory) |Neste passo, irá criar uma fábrica de dados do Azure com o nome **ADFTutorialDataFactoryPSH**. |
| [Criar serviços ligados](#create-linked-services) |Neste passo, irá criar dois serviços ligados: **StorageLinkedService** e **AzureSqlLinkedService**. O StorageLinkedService liga um serviço de Armazenamento do Azure e o AzureSqlLinkedService liga uma base de dados SQL do Azure ao ADFTutorialDataFactoryPSH. |
| [Criar conjuntos de dados de entrada e saída](#create-datasets) |Neste passo, vai definir dois conjuntos de dados (**EmpTableFromBlob** e **EmpSQLTable**). Estes conjuntos de dados são utilizados como tabelas de entrada e de saída para a **Atividade de Cópia** no ADFTutorialPipeline que vai criar no passo seguinte. |
| [Criar e executar um pipeline](#create-pipeline) |Neste passo, vai criar um pipeline com o nome **ADFTutorialPipeline** na fábrica de dados ADFTutorialDataFactoryPSH. O pipeline utiliza a Atividade de Cópia para copiar dados de um blob do Azure para uma tabela de base de dados do Azure de saída. |
| [Monitorizar conjuntos de dados e pipeline](#monitor-pipeline) |Neste passo, vai monitorizar os conjuntos de dados e o pipeline com o Azure PowerShell. |

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Neste passo, vai utilizar o Azure PowerShell para criar uma fábrica de dados do Azure Data Factory com o nome **ADFTutorialDataFactoryPSH**.

1. Inicie o **Azure PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.

   a. Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:

           Login-AzureRmAccount   
   b. Execute o comando seguinte para ver todas as subscrições desta conta:

           Get-AzureRmSubscription
   c. Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua subscrição do Azure:

           Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
2. Crie um grupo de recursos do Azure com o nome **ADFTutorialResourceGroup** ao executar o seguinte comando:

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Alguns dos passos deste tutorial partem do princípio de que utiliza o grupo de recursos com o nome **ADFTutorialResourceGroup**. Se utiliza um grupo de recursos diferente, terá de utilizá-lo em vez de ADFTutorialResourceGroup neste tutorial.
3. Execute o cmdlet **New-AzureRmDataFactory** para criar uma fábrica de dados com o nome **ADFTutorialDataFactoryPSH**:  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o seguinte erro, altere o nome (por exemplo, oseunomeADFTutorialDataFactory). Utilize este nome em vez de ADFTutorialFactoryPSH ao realizar os passos deste tutorial. Veja [Data Factory - Naming Rules (Data Factory - Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.

        Data factory name “ADFTutorialDataFactoryPSH” is not available
* Para criar instâncias do Data Factory, tem de ser contribuidor ou administrador da subscrição do Azure.
* O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
* Poderá ver o erro seguinte "**Esta subscrição não está registada para utilizar o espaço de nomes Microsoft.DataFactory**". Efetue um dos seguintes procedimentos e tente publicar novamente:

  * No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory:

          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

      Execute o seguinte comando para confirmar que o fornecedor do Data Factory está registado:

          Get-AzureRmResourceProvider
  * Utilize a subscrição do Azure para iniciar sessão no [portal do Azure](https://portal.azure.com). Aceda a um painel do Data Factory ou crie uma fábrica de dados no portal do Azure. Esta ação regista automaticamente o fornecedor por si.

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Um arquivo de dados pode ser um serviço de Armazenamento do Azure, uma Base de Dados SQL do Azure ou uma base de dados SQL Server no local, que contém dados de entrada ou armazena dados de saída para um pipeline do Data Factory. Um serviço de computação é um serviço que processa dados de entrada e produz dados de saída.

Neste passo, irá criar dois serviços ligados: **StorageLinkedService** e **AzureSqlLinkedService**. StorageLinkedService liga uma conta de armazenamento do Azure e AzureSqlLinkedService liga uma base de dados SQL do Azure à fábrica de dados **ADFTutorialDataFactoryPSH**. Mais tarde neste tutorial, irá criar um pipeline que copia dados de um contentor de blobs no StorageLinkedService para uma tabela SQL no AzureSqlLinkedService.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Criar um serviço ligado para uma conta de armazenamento do Azure
1. Crie um ficheiro JSON com o nome **StorageLinkedService.json** na pasta **C:\ADFGetStartedPSH** com o conteúdo seguinte. (Se ainda não existir, crie a pasta ADFGetStartedPSH.)

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
3. Pode utilizar o cmdlet **New-AzureRmDataFactoryLinkedService** para criar um serviço ligado. Este cmdlet e os outros cmdlets do Data Factory que utilizar neste tutorial requerem que transmita os valores para os parâmetros **ResourceGroupName** e **DataFactoryName**. Em alternativa, pode utilizar **Get-AzureRmDataFactory** para obter um objeto DataFactory e transmiti-lo sem escrever ResourceGroupName e DataFactoryName sempre que executar um cmdlet. Execute o seguinte comando para atribuir a saída do cmdlet **Get-AzureRmDataFactory** a uma variável: **$df**:

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

Se fechar o Azure PowerShell a meio do tutorial, tem de executar o cmdlet Get-AzureRmDataFactory da próxima vez que iniciar o Azure PowerShell, para concluir o tutorial.

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Criar um serviço ligado para uma base de dados SQL do Azure
1. Crie um ficheiro JSON com o nome AzureSqlLinkedService.json, com o conteúdo seguinte :

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

   Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada no seu servidor da base de dados SQL do Azure. Para verificar e ativá-la, siga os seguintes passos:

   1. Clique no hub **PROCURAR**, à esquerda, e clique em **servidores SQL**.
   2. Selecione o servidor e clique em **DEFINIÇÕES**, no painel do **SQL SERVER**.
   3. No painel **DEFINIÇÕES**, clique em **Firewall**.
   4. No painel **Definições da firewall**, clique em **ATIVAR** para **Permitir acesso aos serviços do Azure**.
   5. Clique no hub **ATIVAR**, à esquerda, para mudar para o painel **Data Factory** onde estava.

## <a name="create-datasets"></a>Criar conjuntos de dados
No passo anterior, criou serviços para ligar uma conta de armazenamento do Azure e uma base de dados SQL do Azure à fábrica de dados. Neste passo, vai criar conjuntos de dados que representam os dados de entrada e saída para a Atividade de Cópia no pipeline que vai criar no próximo passo.

Uma tabela é um conjunto de dados retangular. Atualmente, é o único tipo de conjuntos de dados suportado. A tabela de entrada neste tutorial refere-se a um contentor de blobs no Armazenamento do Azure. A tabela de saída refere-se a uma tabela SQL na base de dados SQL do Azure.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Preparar o Armazenamento de Blobs do Azure e a Base de Dados SQL para o tutorial
Ignore este passo se já leu o tutorial do artigo [Copy data from Blob storage to SQL Database (Copiar dados do Armazenamento de Blobs para a Base de Dados SQL)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Realize os passos seguintes para preparar o armazenamento de blobs e a base de dados SQL para este tutorial.

1. Crie um contentor de blobs com o nome **adftutorial** no Armazenamento de blobs para o qual o **StorageLinkedService** aponta.
2. Crie e carregue um ficheiro de texto, com o nome **emp.txt**, como um blob para o contentor **adftutorial**.
3. Crie uma tabela, com o nome **emp**, na Base de Dados SQL para a qual **AzureSqlLinkedService** aponta.

4. Inicie o Bloco de notas, cole o seguinte texto e guarde-o como **emp.txt** na pasta **C:\ADFGetStartedPSH** no seu disco rígido.

        John, Doe
        Jane, Doe
5. Utilize ferramentas, como o [Explorador do Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/), para criar o contentor **adftutorial** e carregar o ficheiro **emp.txt** para o contentor.

    ![Explorador do Storage do Azure](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
6. Utilize o seguinte script SQL para criar a tabela **emp** na sua base de dados SQL.  

        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    Se tiver instalado no seu computador o SQL Server 2014, siga as instruções em [Step 2: Connect to SQL Database of the Managing Azure SQL Database using SQL Server Management Studio (Passo 2: Ligar à Base de Dados SQL do artigo Gerir a Base de Dados SQL do Azure com o SQL Server Management Studio)](../sql-database/sql-database-manage-azure-ssms.md) para ligar ao seu servidor da base de dados SQL e executar o script SQL.

    Se o seu cliente não tiver autorização para aceder ao servidor da base de dados SQL, tem de configurar a firewall do servidor da base de dados SQL para permitir o acesso a partir do seu computador (Endereço IP). Para obter os passos, veja [este artigo](../sql-database/sql-database-configure-firewall-settings.md).

### <a name="create-an-input-dataset"></a>Criar um conjunto de dados de entrada
Uma tabela é um conjunto de dados retangular com um esquema. Neste passo, vai criar uma tabela, com o nome **EmpBlobTable**. Esta tabela aponta para um contentor de blobs no Armazenamento do Azure que é representado pelo serviço ligado **StorageLinkedService**. Este contentor do blob (**adftutorial**) contém os dados de entrada no ficheiro **emp.txt**.

1. Crie um ficheiro JSON com o nome **EmpBlobTable.json** na pasta **C:\ADFGetStartedPSH** com o conteúdo seguinte:

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

   * O **tipo** do conjunto de dados está definido como **AzureBlob**.
   * O **linkedServiceName** está definido como **StorageLinkedService**.
   * O **folderPath** está definido para o contentor **adftutorial**.
   * O **fileName** está definido como **emp.txt**. Se não especificar o nome do blob, os dados de todos os blobs no contentor são considerados dados de entrada.  
   * O **tipo** do formato está definido como **TextFormat**.
   * Existem dois campos no ficheiro de texto, **FirstName** e **LastName**, separados por vírgula (**columnDelimiter**).    
   * A **disponibilidade** está definida como **de hora a hora** (**a frequência** está definida como **hora** e o **intervalo** está definido como **1**). Assim, o Data Factory procura dados de entrada a cada hora na pasta raiz no contentor de blobs (**adftutorial**).

   Se não especificar um **fileName** para uma **tabela de entrada**, todos os ficheiros e blobs da pasta de entrada (**folderPath**) são considerados entradas. Se especificar um fileName no JSON, apenas o ficheiro ou blob especificado é considerado como entrada.

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

   Para obter detalhes sobre as propriedades JSON, veja [JSON Scripting Reference (Referência a Scripts JSON)](data-factory-data-movement-activities.md).
2. Execute o seguinte comando para criar o conjunto de dados do Data Factory.

    ```  
    New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
    ```

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
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

   * O **tipo** do conjunto de dados está definido como **AzureSqlTable**.
   * O **linkedServiceName** está definido como **AzureSqlLinkedService**.
   * O **tablename** está definido como **emp**.
   * Existem três colunas, **ID**, **FirstName**, e **LastName**, na tabela emp da base de dados. O ID é uma coluna de identidade, por isso terá de especificar apenas **FirstName** e **LastName** aqui.
   * A **disponibilidade** está definida como **de hora a hora** (**frequência** definida como **hora** e **intervalo** definido como **1**). O serviço Data Factory gera um setor de dados de saída a cada hora na tabela **emp** da base de dados SQL do Azure.
2. Execute o comando seguinte para criar o conjunto de dados da fábrica de dados.

    ```   
    New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste passo, vai criar um pipeline com a **Atividade de Cópia**. O pipeline utiliza **EmpTableFromBlob** como entrada e **EmpSQLTable** como saída.

1. Crie um ficheiro JSON com o nome **ADFTutorialPipeline.json** na pasta **C:\ADFGetStartedPSH** com o conteúdo seguinte:

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

   Substitua o valor da propriedade **start** pelo dia atual e o da propriedade **end** pelo dia seguinte. Tanto o datetime de início como de fim têm de estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. Este tutorial utiliza a hora **end**, mas esta propriedade é opcional.

   Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9/9/9999** como o valor da propriedade **end**.

   Existem 24 setores de dados no exemplo, uma vez que estes são produzidos de hora a hora.

   Para obter mais detalhes sobre as propriedades JSON, veja [JSON Scripting Reference (Referência a Scripts JSON)](data-factory-data-movement-activities.md).
2. Execute o comando seguinte para criar a tabela da fábrica de dados.

    ```   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

Parabéns! Criou uma fábrica de dados, serviços ligados, tabelas e pipelines do Azure Data Fabric. Também agendou o pipeline.

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
Neste passo, irá utilizar o Azure PowerShell para monitorizar os acontecimentos de uma fábrica de dados do Azure.

1. Execute **Get-AzureRmDataFactory** e atribua o resultado a uma variável $df.

    ```  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

2. Execute **Get-AzureRmDataFactorySlice** para obter detalhes sobre todos os setores do **EmpSQLTable**, que é a tabela de saída do pipeline.  

    ```   
    Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Substitua as partes de ano, mês e data do parâmetro **StartDateTime** pelo ano, mês e data atuais. Esta definição deve corresponder ao valor **Start** no JSON do pipeline.

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

   Deverá ver um resultado semelhante ao exemplo seguinte:

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

Para obter documentação completa sobre os cmdlets do Data Factory, veja a [Data Factory Cmdlet Reference (Referência aos Cmdlets do Data Factory)][cmdlet-reference].

## <a name="summary"></a>Resumo
Neste tutorial, vai criar um Azure Data Factory para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Utilizou o PowerShell para criar a fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Eis os passos de alto nível que realizou neste tutorial:  

1. Criou uma **fábrica de dados** do Azure.
2. Criou **serviços ligados**:

   a. Um serviço ligado do **Armazenamento do Azure** para ligar a sua conta de Armazenamento do Azure que contém os dados de entrada.     
   b. Um serviço ligado do **SQL do Azure** para ligar a sua base de dados SQ que contém os dados de saída.
3. Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4. Criou um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como a origem e **SqlSink** como o sink.

## <a name="see-also"></a>Consultar também
| Tópico | Descrição |
|:--- |:--- |
| [Referência dos Cmdlets do Data Factory](/powershell/resourcemanager/azurerm.datafactories/v2.5.0/azurerm.datafactories) | Esta secção fornece informações sobre todos os cmdlets do Data Factory |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory. |
| [conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo de aplicação do Azure Data Factory. |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md



<!--HONumber=Feb17_HO1-->


