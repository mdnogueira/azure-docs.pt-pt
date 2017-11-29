---
title: "Personalizar Clusters do HDInsight utilizando o arranque de configuração - Azure | Microsoft Docs"
description: "Saiba como personalizar clusters do HDInsight utilizando o arranque de configuração."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ab2ebf0c-e961-4e95-8151-9724ee22d769
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jgao
ms.openlocfilehash: e5ed66e263c3048981279ad26cb204ded8e8fba3
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizar clusters do HDInsight utilizando o arranque de configuração

Por vezes, pretende configurar os ficheiros de configuração, que incluem:

* clusterIdentity.xml
* Core-site.XML
* gateway.XML
* hbase env.xml
* hbase site.xml
* hdfs site.xml
* env.xml do Hive
* site.xml do Hive
* mapred site
* oozie site.xml
* oozie env.xml
* Storm site.xml
* tez site.xml
* webhcat site.xml
* yarn site.xml
* Server.Properties (configuração kafka mediador)

Existem três métodos para utilizar o arranque de configuração:

* Utilizar o Azure PowerShell
* Utilizar o .NET SDK
* Utilizar o modelo do Azure Resource Manager

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Para obter informações sobre como instalar os componentes adicionais num cluster do HDInsight durante a hora de criação, consulte:

* [Personalizar clusters do HDInsight através da ação de Script (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
O seguinte código do PowerShell customizes uma configuração de ramo de registo:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

New-AzureRmHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.5" `
    -HttpCredential $httpCredential `
    -Config $config 
```

Um script do PowerShell de trabalho completado pode ser encontrado na [apêndice](#appendix-powershell-sample).

**Para verificar a alteração:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **clusters do HDInsight**. Se não o vir, clique em **mais serviços** primeiro.
3. Clique no cluster que acabou de criar utilizando o script do PowerShell.
4. Clique em **Dashboard** da parte superior do painel para abrir a IU do Ambari.
5. Clique em **Hive** no menu esquerdo.
6. Clique em **HiveServer2** de **resumo**.
7. Clique em de **folhas** separador.
8. Clique em **Hive** no menu esquerdo.
9. Clique em de **avançadas** separador.
10. Desloque para baixo e, em seguida, expanda **avançadas do hive site**.
11. Procure **hive.metastore.client.socket.timeout** na secção.

Alguns exemplos mais sobre a personalização outros ficheiros de configuração:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```
Para obter mais informações, consulte o blogue de Azim Uddin intitulada [criação do Cluster de HDInsight personalizar](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>Utilizar o .NET SDK
Consulte [baseado em Linux criar clusters HDInsight utilizando o SDK .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Utilize o Gestor de recursos de modelo
Pode utilizar o arranque no modelo do Resource Manager:

```json
"configurations": {
    …
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop customizes modelo Azure Resource Manager arranque de configuração do cluster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Consultar também
* [Criar clusters do Hadoop no HDInsight] [ hdinsight-provision-cluster] fornece instruções sobre como criar um cluster do HDInsight utilizando outras opções personalizadas.
* [Desenvolver scripts de ação de Script para o HDInsight][hdinsight-write-script]
* [Instalar e utilizar o Spark nos clusters do HDInsight][hdinsight-install-spark]
* [Instalar e utilizar o R nos clusters do HDInsight][hdinsight-install-r]
* [Instalar e utilizar Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install.md).
* [Instalar e utilizar Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fases durante a criação do cluster"

## <a name="appendix-powershell-sample"></a>Apêndice: Exemplo do PowerShell
Este script do PowerShell cria um cluster do HDInsight e customizes uma definição de ramo de registo:

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US 2"
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzureRmResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_GRS

$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageContext = New-AzureStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzureRmHDInsightClusterConfig `
    | Set-AzureRmHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzureRmHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzureRmHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.5" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

#endregion
```
