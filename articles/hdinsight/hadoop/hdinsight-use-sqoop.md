---
title: Executar tarefas de Apache Sqoop com o Azure HDInsight (Hadoop) | Microsoft Docs
description: "Saiba como utilizar o Azure PowerShell a partir de uma estação de trabalho a executar o Sqoop importar e exportar entre um cluster do Hadoop e uma base de dados SQL do Azure."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 2fdcc6b7-6ad5-4397-a30b-e7e389b66c7a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jgao
ms.openlocfilehash: 96d6d707d69f80a866f6ac17addabc0db076b079
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-sqoop-with-hadoop-in-hdinsight"></a>Utilizar o Sqoop com o Hadoop no HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Sqoop no HDInsight para importar e exportar entre o cluster do HDInsight e a SQL database do Azure ou a base de dados do SQL Server.

Apesar de Hadoop é uma escolha natural para processamento de dados não estruturados e semiestruturados, tais como registos e ficheiros, também poderão existir a necessidade de processar dados estruturados, que são armazenados nas bases de dados relacionais.

[Sqoop] [ sqoop-user-guide-1.4.4] é uma ferramenta concebida para transferir dados entre clusters do Hadoop e bases de dados relacionais. Pode utilizá-lo para importar dados a partir de um sistema de gestão de base de dados relacional (RDBMS), tal como o SQL Server, MySQL ou Oracle para o sistema de ficheiros distribuído do Hadoop (HDFS), transformar os dados no Hadoop com o MapReduce ou Hive e, em seguida, exportar os dados de volta para um RDBMS. Neste tutorial, está a utilizar uma base de dados do SQL Server da base de dados relacional.

Para versões de Sqoop que são suportadas nos clusters do HDInsight, consulte [Novidades nas versões do cluster fornecidas pelo HDInsight?][hdinsight-versions]

## <a name="understand-the-scenario"></a>Compreender o cenário

Cluster do HDInsight é fornecido com alguns dados de exemplo. Utilize os seguintes dois exemplos:

* Um ficheiro de registo de log4j, que está localizado em */example/data/sample.log*. Os seguintes registos são extraídos a partir do ficheiro:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Uma tabela do Hive com o nome *hivesampletable*, que referencia o ficheiro de dados, localizado em */hive/warehouse/hivesampletable*. A tabela contém alguns dados de dispositivos móveis. 
  
  | Campo | Tipo de dados |
  | --- | --- |
  | ID de cliente |Cadeia |
  | querytime |Cadeia |
  | mercado |Cadeia |
  | deviceplatform |Cadeia |
  | devicemake |Cadeia |
  | devicemodel |Cadeia |
  | state |Cadeia |
  | País |Cadeia |
  | querydwelltime |duplo |
  | ID de sessão |bigint |
  | sessionpagevieworder |bigint |

Neste tutorial, pode utilizar estes dois conjuntos de dados para testar Sqoop importação e exportação.

## <a name="create-cluster-and-sql-database"></a>Criar o cluster e a base de dados SQL
Esta secção mostra como criar um cluster, uma base de dados do SQL Server e os esquemas de base de dados do SQL Server para executar o tutorial, utilizando o portal do Azure e um modelo Azure Resource Manager. O modelo pode ser encontrado na [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). O modelo do Resource Manager chama um pacote de bacpac para implementar os esquemas de tabela à base de dados do SQL Server.  O pacote de bacpac está localizado num contentor de blob público, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se pretender utilizar um contentor privado para os ficheiros de bacpac, utilize os seguintes valores no modelo:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Se preferir utilizar o Azure PowerShell para criar o cluster e a base de dados do SQL Server, consulte [anexo A](#appendix-a---a-powershell-sample).

> [!NOTE]
> Importar utilizando um modelo ou do portal do Azure só suporta a importação de um ficheiro BACPAC do armazenamento de Blobs do Azure.

**Para configurar o ambiente através de um modelo de gestão de recursos**
1. Clique na imagem seguinte para abrir um modelo do Resource Manager no portal do Azure.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Introduza as seguintes propriedades:

    - **Subscrição**: introduza a sua subscrição do Azure.
    - **Grupo de recursos**: criar um novo grupo de recursos do Azure, ou selecione um grupo de recursos existente.  É um grupo de recursos para fins de gestão.  É um contentor de objetos.
    - **Localização**: selecione uma região.
    - **ClusterName**: introduza um nome para o cluster do Hadoop.
    - **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é admin.
    - **Nome de utilizador e palavra-passe do SSH**.
    - **SQL Server da base de dados nome de início de sessão do servidor e a palavra-passe**.
    - **_artifacts localização**: Utilize o valor predefinido, a menos que pretenda utilizar o seu próprio ficheiro backpac numa localização diferente.
    - **_artifacts localização Sas Token**: pode deixar em branco.
    - **Nome de ficheiro Bacpac**: Utilize o valor predefinido, a menos que pretenda utilizar o seu próprio ficheiro backpac.
     
        Os valores seguintes são codificado na secção de variáveis:
        
        |Nome|Valor|
        |----|-----|
        | Nome de conta do storage predefinida | &lt;CluterName > armazenar |
        | Nome de servidor de base de dados SQL do Azure | &lt;ClusterName > dbserver |
        | Nome de base de dados SQL do Azure | &lt;ClusterName > base de dados |
     
3. Selecione **concordo com os termos e condições indicadas acima**.
4. Clique em **Comprar**. Verá um novo mosaico intitulado submeter a implementação para a implementação do modelo. A criação do cluster e da SQL Database demora cerca de 20 minutos.

Se optar por utilizar a base de dados SQL do Azure existente ou Microsoft SQL Server

* **Base de dados SQL do Azure**: tem de configurar uma regra de firewall para o servidor de base de dados SQL do Azure permitir o acesso da sua estação de trabalho. Para obter instruções sobre como criar uma base de dados SQL do Azure e a configuração da firewall, consulte [começar a utilizar a SQL database do Azure][sqldatabase-get-started]. 
  
  > [!NOTE]
  > Por predefinição uma base de dados SQL do Azure permite ligações de serviços do Azure, como o Azure HDInsight. Se esta definição de firewall estiver desativada, tem de ativá-la a partir do portal do Azure. Para instruções sobre como criar uma base de dados SQL do Azure e configurar regras de firewall, consulte [criar e configurar a base de dados do SQL][sqldatabase-create-configue].
  > 
  > 
* **SQL Server**: se o cluster do HDInsight está na mesma rede virtual no Azure como o SQL Server, pode utilizar os passos neste artigo para importar e exportar dados para uma base de dados do SQL Server.
  
  > [!NOTE]
  > HDInsight suporta apenas com base na localização de redes virtuais e não funcionam atualmente com redes virtuais baseadas no grupo de afinidade.
  > 
  > 
  
  * Para criar e configurar uma rede virtual, consulte [criar uma rede virtual com o portal do Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    
    * Quando estiver a utilizar o SQL Server no seu centro de dados, tem de configurar a rede virtual como *site para site* ou *ponto a site*.
      
      > [!NOTE]
      > Para **ponto a site** redes virtuais, SQL Server tem de executar o cliente VPN aplicação de configuração, o que está disponível a partir de **Dashboard** da sua configuração de rede virtual do Azure.
      > 
      > 
    * Quando estiver a utilizar o SQL Server numa máquina virtual do Azure, qualquer configuração de rede virtual pode ser utilizada se a máquina virtual que aloja o SQL Server é um membro da mesma rede virtual como HDInsight.
  * Para criar um cluster do HDInsight numa rede virtual, consulte [clusters do Hadoop criar no HDInsight utilizando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]
    > SQL Server também têm de permitir a autenticação. Tem de utilizar um início de sessão do SQL Server para concluir os passos neste artigo.
    > 
    > 

**Para validar a configuração**

1. Abra o grupo de recursos no portal do Azure. Deverá ver quatro recursos no grupo de:

    - o cluster
    - o servidor de base de dados
    - a base de dados
    - a conta do storage predefinida

2. Abra a base de dados no Microsoft SQL Server Management Studio.  Deverá ver duas bases de dados implementados:

    ![Azure HDInsight Sqoop SQL Server Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Executar tarefas de Sqoop
HDInsight pode executar tarefas de Sqoop utilizando uma variedade de métodos. Utilize a tabela seguinte para decidir qual o método é mais adequado para si e, em seguida, siga a ligação para obter instruções.

| **Utilize esta opção** se pretender que... | … .an **interativa** shell | ... **batch** processamento | … .with isto **sistema de operativo de cluster** | … .from isto **sistema operativo do cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [.NET SDK para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |✔ |Linux ou do Windows |Windows (por agora) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |✔ |Linux ou do Windows |Windows |

## <a name="limitations"></a>Limitações
* Exportação em massa - baseado em Linux com o HDInsight, o conector de Sqoop utilizado para exportar dados para o Microsoft SQL Server ou SQL Database do Azure não suporta atualmente inserções em massa.
* Criação de batches - com o HDInsight baseado em Linux, ao utilizar o `-batch` mudar quando efetuar inserções, Sqoop efetua várias inserções em vez das operações de inserção de criação de batches.

## <a name="next-steps"></a>Passos seguintes
Agora tem aprendeu a utilizar o Sqoop. Para saber mais, consulte:

* [Utilizar o Hive com o HDInsight](../hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](../hdinsight-use-pig.md)
* [Carregar dados para o HDInsight][hdinsight-upload-data]: localizar outros métodos para carregar dados para armazenamento de Blobs do HDInsight/Azure.

## <a name="appendix-a---a-powershell-sample"></a>Apêndice A – um exemplo do PowerShell
O exemplo do PowerShell efetua os seguintes passos:

1. Ligar ao Azure.
2. Criar um grupo de recursos do Azure. Para obter mais informações, consulte [utilizar o Azure PowerShell com o Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md)
3. Crie um servidor de base de dados do Azure SQL, uma base de dados SQL do Azure e duas tabelas. 
   
    Se utilizar o SQL Server em vez disso, utilize as seguintes instruções para criar as tabelas:
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    A forma mais fácil para examinar a base de dados e tabelas está a utilizar o Visual Studio. O servidor de base de dados e a base de dados podem ser reduzidas no portal do Azure.
4. Crie um cluster do HDInsight.
   
    Para examinar o cluster, pode utilizar o portal do Azure ou o Azure PowerShell.
5. Pré-processou o ficheiro de dados de origem.
   
    Neste tutorial, exporte um ficheiro de registo de log4j (um ficheiro delimitado) e uma tabela do Hive para uma base de dados SQL do Azure. O ficheiro delimitado denomina */example/data/sample.log*. Anteriormente no tutorial, viu alguns exemplos de registos de log4j. No ficheiro de registo, existem algumas linhas em branco e algumas linhas semelhantes a estas:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Isto é adequado para outros exemplos que utilizam estes dados, mas antes, pode importar para a base de dados SQL do Azure ou SQL Server, tem de remover estas exceções. Exportação de Sqoop falha se existir uma cadeia vazia ou uma linha com menos elemento que o número de campos definidos na tabela de base de dados SQL do Azure. A tabela de log4jlogs tem sete campos do tipo de cadeia.
   
    Este procedimento cria um novo ficheiro no cluster: tutorials/usesqoop/data/sample.log. Para examinar o ficheiro de dados modificados, pode utilizar o portal do Azure, uma ferramenta do Explorador de armazenamento do Azure ou do Azure PowerShell. [Introdução ao HDInsight] [ hdinsight-get-started] tem um exemplo de código para utilizar o Azure PowerShell para transferir um ficheiro e apresentar o conteúdo do ficheiro.
6. Exporte um ficheiro de dados para a base de dados SQL do Azure.
   
    O ficheiro de origem é tutorials/usesqoop/data/sample.log. A tabela em que os dados são exportados para denomina log4jlogs.
   
   > [!NOTE]
   > Além de informações da cadeia de ligação, os passos nesta secção devem funcionar para uma base de dados SQL do Azure ou para o SQL Server. Estes passos foram testados utilizando a seguinte configuração:
   > 
   > * **Configuração de ponto a site de rede virtual do Azure**: uma rede virtual ligado do cluster do HDInsight para um servidor de SQL no Centro de dados privado. Consulte [configurar uma VPN ponto a Site no Portal de gestão](../../vpn-gateway/vpn-gateway-point-to-site-create.md) para obter mais informações.
   > * **Azure HDInsight 3.1**: consulte [clusters do Hadoop criar no HDInsight utilizando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md) para obter informações sobre como criar um cluster de uma rede virtual.
   > * **SQL Server 2014**: configurado para permitir a autenticação e a executar o cliente VPN do pacote de configuração para ligar de forma segura à rede virtual.
   > 
   > 
7. Exporte uma tabela do Hive para a base de dados SQL do Azure.
8. Importe a tabela de mobiledata para o cluster do HDInsight.
   
    Para examinar o ficheiro de dados modificados, pode utilizar o portal do Azure, uma ferramenta do Explorador de armazenamento do Azure ou do Azure PowerShell.  [Introdução ao HDInsight] [ hdinsight-get-started] tem um código de exemplo sobre como utilizar o Azure PowerShell para transferir um ficheiro e apresentar o conteúdo do ficheiro.

### <a name="the-powershell-sample"></a>O exemplo do PowerShell

```powershell
# Prepare an Azure SQL database to be used by the Sqoop tutorial

#region - provide the following values

$subscriptionID = "<Enter your Azure Subscription ID>"

$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
$sqlDatabasePassword = "<Enter a Password>"

$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh username
$sshPassword = $httpPassword 

# used for creating Azure service names
$nameToken = "<Enter an alias>" 
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
#endregion

#region - variables

# Resource group variables
$resourceGroupName = $namePrefix + "rg"
$location = "East US 2" # used by all Azure services defined in this tutorial

# SQL database varialbes
$sqlDatabaseServerName = $namePrefix + "sqldbserver"
$sqlDatabaseName = $namePrefix + "sqldb"
$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
$sqlDatabaseMaxSizeGB = 10

# Used for retrieving external IP address and creating firewall rules
$ipAddressRestService = "http://bot.whatismyipaddress.com"
$fireWallRuleName = "UseSqoop"

# Used for creating tables and clustered indexes
$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
    [t1] [nvarchar](50),
    [t2] [nvarchar](50),
    [t3] [nvarchar](50),
    [t4] [nvarchar](50),
    [t5] [nvarchar](50),
    [t6] [nvarchar](50),
    [t7] [nvarchar](50))"

$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder][bigint])"

$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

# HDInsight variables
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

#region - Create Azure resouce group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch{
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-Azureservices" `
        -StartIpAddress "0.0.0.0" `
        -EndIpAddress "0.0.0.0"
}

#endregion

#region - Create and validate Azure SQL database
Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

try {
    Get-AzureRmSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzureRMSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName `
        -Edition "Standard" `
        -RequestedServiceObjectiveName "S1"
}

#endregion

#region - Create tables
Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = $sqlDatabaseConnectionString
$conn.Open()

# Create the log4jlogs table and index
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.Connection = $conn
$cmd.CommandText = $cmdCreateLog4jTable
$ret = $cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateLog4jClusteredIndex
$cmd.ExecuteNonQuery()

# Create the mobiledata table and index
$cmd.CommandText = $cmdCreateMobileTable
$cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
$cmd.ExecuteNonQuery()

$conn.close()

#endregion


#region - Create HDInsight cluster

Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

# Create the default storage account
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzureRmHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $HDInsightClusterName `
    -Location $location `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version 3.6 `
    -ClusterSizeInNodes 2 `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $defaultBlobContainerName  

# Validate the cluster
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
#endregion

#region - pre-process the source file

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"

# Define the connection string
$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

# Create block blob objects referencing the source and destination blob.
$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
$storageClient = $storageAccount.CreateCloudBlobClient();
$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
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

#region - export a log file from the cluster to the SQL database

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

$tableName_log4j = "log4jlogs"

# Connection string for Azure SQL Database.
# Comment if using SQL Server
$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
# Connection string for SQL Server.
# Uncomment if using SQL Server.
#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

$exportDir_log4j = "/tutorials/usesqoop/data"

# Submit a Sqoop job
$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
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

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
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
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion

#region - import a database

$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

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
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion
```


[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: ../hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
