<properties 
   pageTitle="Introdução à Análise do Azure Data Lake ao utilizar o Azure PowerShell | Azure" 
   description="Saiba como utilizar o Azure PowerShell para criar uma conta de Data Lake Store, criar uma tarefa de Data Lake Analytics, utilizando U-SQL, e submeter a tarefa. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Tutorial: introdução à Análise do Azure Data Lake ao utilizar o Azure PowerShell

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar o Azure PowerShell para criar contas de Azure Data Lake Analytics, definir as tarefas de Data Lake Analytics em [U-SQL](data-lake-analytics-u-sql-get-started.md) e submeter as tarefas para contas de Data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md).

Neste tutorial, irá desenvolver uma tarefa que lê um ficheiro de valores separados por tabulações (TSV) e converte-o num ficheiro de valores separados por vírgulas (CSV). Para seguir o mesmo tutorial, utilizando outras ferramentas suportadas, clique nos separadores na parte superior desta secção.

[AZURE.INCLUDE [basic-process-include](../../includes/data-lake-analytics-basic-process.md)]

##Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte:

- **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Uma estação de trabalho com o Azure PowerShell**. Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
    
##Criar conta de Data Lake Analytics

Tem de ter uma conta de Data Lake Analytics antes de poder executar quaisquer tarefas. Para criar uma conta de Data Lake Analytics, tem de especificar o seguinte:

- **Grupo de Recursos do Azure**: uma conta de Data Lake Analytics tem de ser criada dentro de um grupo de Recursos do Azure. O [Azure Resource Manager](../resource-group-overview.md) permite trabalhar com os recursos na aplicação como um grupo. Pode implementar, atualizar ou eliminar todos os recursos da aplicação numa operação única e coordenada.  

    Para enumerar os grupos de recursos na subscrição:
    
        Get-AzureRmResourceGroup
    
    Para criar um novo grupo de recursos:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **Nome da conta de Data Lake Analytics**
- **Localização**: um dos centros de dados do Azure que suportem a Data Lake Analytics.
- **Conta do Data Lake predefinida**: cada conta de Data Lake Analytics tem uma conta do Data Lake predefinida.

    Para criar uma nova conta do Data Lake:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] O nome de conta do Data Lake só pode conter minúsculas e números.



**Para criar uma conta de Data Lake Analytics**

1. Abra o ISE do PowerShell a partir da estação de trabalho do Windows.
2. Execute o seguintes script:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##Carregar dados para o Data Lake

Neste tutorial, irá processar alguns registos de pesquisa.  O registo de pesquisa pode ser armazenado no Data Lake Store ou no Armazenamento de Blobs do Azure. 

Um ficheiro de registo de pesquisa de exemplo foi copiado para um contentor de Blobs do Azure público. Utilize o seguinte script do PowerShell para transferir o ficheiro para a estação de trabalho e, em seguida, carregue o ficheiro para a conta de Data Lake Store predefinida da sua conta de Data Lake Analytics.

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

O script do PowerShell seguinte mostra como obter o nome do Data Lake Store predefinido para uma conta de Data Lake Analytics:


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

>[AZURE.NOTE] O Portal do Azure fornece uma interface de utilizador para copiar os ficheiros de dados de exemplo para a conta de Data Lake Store predefinida. Para instruções, consulte [Introdução à Análise do Azure Data Lake ao utilizar o Portal do Azure](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

A Data Lake Analytics também pode aceder ao armazenamento de Blobs do Azure.  Para carregar dados para o armazenamento de Blobs do Azure, consulte [Utilizar o Azure PowerShell com o Armazenamento do Azure](../storage/storage-powershell-guide-full.md).

##Submeter tarefas de Data Lake Analytics

As tarefas de Data Lake Analytics são escritas em linguagem U-SQL. Para saber mais sobre U-SQL, consulte [Introdução à linguagem U SQL](data-lake-analytics-u-sql-get-started.md) e [Referência de linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para criar um script de tarefa de Data Lake Analytics**

- Crie um ficheiro de texto com o seguinte script de U-SQL e guarde o ficheiro de texto na estação de trabalho:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Este script U-SQL lê o ficheiro de dados de origem, utilizando **Extractors.Tsv()**, e, em seguida, cria um ficheiro csv, utilizando **Outputters.Csv()**. 
    
    Não modifique os dois caminhos, exceto se copiar o ficheiro de origem para uma localização diferente.  A Data Lake Analytics cria a pasta de saída, se não existir.
    
    É mais simples utilizar caminhos relativos para ficheiros armazenados em contas do Data Lake predefinidas. Também pode utilizar caminhos absolutos.  Por exemplo 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Tem de utilizar caminhos absolutos para aceder a ficheiros em contas de Armazenamento ligadas.  A sintaxe para ficheiros armazenados numa Conta de Armazenamento do Azure ligada é:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] O contentor de Blobs do Azure com blobs públicos ou permissões de acesso a contentores públicos não são atualmente suportados.    
    
    
**Para submeter a tarefa**

1. Abra o ISE do PowerShell a partir da estação de trabalho do Windows.
2. Execute o seguintes script:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 
                        
        While (($t = Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId).State -ne "Ended"){
            Write-Host "Job status: "$t.State"..."
            Start-Sleep -seconds 5
        }
        
        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    No script, o ficheiro de script U-SQL é armazenado em c:\tutorials\data-lake-analytics\copyFile.usql. Atualize o caminho do ficheiro em conformidade.
 
Depois de a tarefa ser concluída, pode utilizar os seguintes cmdlets para listar o ficheiro e transferi-lo:
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## Consulte também

- Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
- Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicações U-SQL, consulte [Desenvolver scripts SQL-U, utilizando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para tarefas de gestão, consulte [Gerir a Análise do Azure Data Lake através do Portal do Azure](data-lake-analytics-manage-use-portal.md).
- Para uma descrição geral da Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).




<!--HONumber=sep16_HO2-->


