---
title: Executar tarefas de Sqoop utilizando o PowerShell e o Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar o Azure PowerShell a partir de uma estação de trabalho a executar o Sqoop importar e exportar entre um cluster do Hadoop e uma base de dados SQL do Azure."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: bbb6f53a-e019-4d01-92bd-92c208c760b6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: 5a45948851e24c8546789e0ca4f11f7e8b87a15f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="run-sqoop-jobs-by-using-azure-powershell-for-hadoop-in-hdinsight"></a>Executar tarefas de Sqoop utilizando o Azure PowerShell para o Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Azure PowerShell para executar tarefas de Sqoop no Azure HDInsight para importar e exportar entre um cluster do HDInsight e uma base de dados SQL do Azure ou a base de dados do SQL Server.

> [!NOTE]
> Apesar de poder utilizar os procedimentos neste artigo com uma um cluster do HDInsight baseado em Windows ou Linux, que utilizam apenas a partir de um cliente Windows. Para optar por outros métodos, utilize o Seletor de separador na parte superior deste artigo. 
> 
> 

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes itens:

* Uma estação de trabalho com o Azure PowerShell.
* Um cluster do Hadoop no HDInsight. Para obter mais informações, consulte [criar o cluster e a SQL database](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-sqoop-by-using-powershell"></a>Execute o Sqoop com o PowerShell
O seguinte script do PowerShell previamente processa o ficheiro de origem e, em seguida, exporta-o para uma base de dados SQL do Azure:

    $resourceGroupName = "<AzureResourceGroupName>"
    $hdinsightClusterName = "<HDInsightClusterName>"

    $httpUserName = "admin"
    $httpPassword = "<Password>"

    $defaultStorageAccountName = $hdinsightClusterName + "store"
    $defaultBlobContainerName = $hdinsightClusterName


    $sqlDatabaseServerName = $hdinsightClusterName + "dbserver"
    $sqlDatabaseName = $hdinsightClusterName + "db"
    $sqlDatabaseLogin = "sqluser"
    $sqlDatabasePassword = "<Password>"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - pre-process the source file

    Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green

    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"

    # Define the connection string
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    # Create block blob objects referencing the source and destination blob.
    $storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $defaultStorageAccountName
    $storageContainer = ($storageAccount |Get-AzureStorageContainer -Name $defaultBlobContainerName).CloudBlobContainer
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)

    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")

        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)

            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }

        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }

    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    #endregion

    #region - export the log file from the cluster to the SQL database

    Write-Host "Exporting the log file ..." -ForegroundColor Green

    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    $tableName_log4j = "log4jlogs"
    $exportDir_log4j = "/tutorials/usesqoop/data"
    $sqljdbcdriver = "/user/oozie/share/lib/sqoop/sqljdbc41.jar"

    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
        -Files $sqljdbcdriver

    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId

    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    #endregion

## <a name="limitations"></a>Limitações
HDInsight baseado em Linux apresenta as seguintes limitações:

* Em massa exportação: Sqoop o conector, que é utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta atualmente inserções em massa.

* Criação de batches: utilizando o `-batch` mudar quando executa inserções, Sqoop efetua várias inserções em vez das operações de inserção de criação de batches. 

## <a name="next-steps"></a>Passos seguintes
Agora tem aprendeu a utilizar o Sqoop. Para saber mais, consulte:

* [Utilizar o Oozie com o HDInsight](../hdinsight-use-oozie.md): Utilize Sqoop ação um fluxo de trabalho do Oozie.
* [Analisar dados de atraso do voo utilizando HDInsight](../hdinsight-analyze-flight-delay-data.md): utilizar o Hive para analisar voo atrasar dados e, em seguida, utilizar o Sqoop para exportar dados para uma base de dados SQL do Azure.
* [Carregar dados para o HDInsight](../hdinsight-upload-data.md): localizar outros métodos para carregar dados para armazenamento HDInsight ou Blob do Azure.

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
