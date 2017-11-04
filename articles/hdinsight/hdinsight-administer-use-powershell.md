---
title: "Gerir clusters do Hadoop no HDInsight com o PowerShell – Azure | Microsoft Docs"
description: Saiba como efetuar tarefas administrativas para os clusters do Hadoop no HDInsight com o Azure PowerShell.
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: 
ms.assetid: bfdfa754-18e5-4ef9-b0d6-2dbdcebc0283
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: 58028c969130991f3dfa6556baa37a0510f9c7e3
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gerir clusters do Hadoop no HDInsight ao utilizar o Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

O Azure PowerShell pode ser utilizado para controlar e automatizar a implementação e gestão das cargas de trabalho no Azure. Neste artigo, irá aprender a gerir clusters do Hadoop no Azure HDInsight com o Azure PowerShell. Para obter a lista dos cmdlets do PowerShell do HDInsight, consulte [referência de cmdlets do HDInsight][hdinsight-powershell-reference].

**Pré-requisitos**

Antes de começar este artigo, tem de ter os seguintes itens:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Se tiver instalado o Azure PowerShell versão 0,9 x, tem de o desinstalar antes de instalar uma versão mais recente.

Para verificar a versão do PowerShell instalada:

```powershell
Get-Module *azure*
```

Para desinstalar a versão mais antiga, execute programas e funcionalidades no painel de controlo.

## <a name="create-clusters"></a>Criar clusters
Consulte [baseado em Linux criar clusters no HDInsight com o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Lista de clusters
Utilize o seguinte comando para listar todos os clusters na subscrição atual:

```powershell
Get-AzureRmHDInsightCluster
```

## <a name="show-cluster"></a>Mostrar o cluster
Utilize o seguinte comando para mostrar os detalhes de um cluster específico na subscrição atual:

```powershell
Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Eliminar clusters
Utilize o seguinte comando para eliminar um cluster:

```powershell
Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

Também pode eliminar um cluster ao remover o grupo de recursos que contém o cluster. Eliminar um grupo de recursos elimina todos os recursos no grupo, incluindo a conta do storage predefinida.

```powershell
Remove-AzureRmResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Dimensionar clusters
O cluster dimensionamento funcionalidade permite-lhe alterar o número de nós de trabalho utilizado por um cluster que está em execução no Azure HDInsight sem ter de recriar o cluster.

> [!NOTE]
> Apenas clusters do hdinsight versão 3.1.3 ou superior, são suportadas. Se não souber a versão do cluster, pode verificar a página de propriedades.  Consulte [clusters lista e mostrar](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
>
>

O impacto da alteração do número de nós de dados para cada tipo de cluster suportada pelo HDInsight:

* Hadoop

    Perfeitamente pode aumentar o número de nós de trabalho de um cluster de Hadoop está em execução sem afetar quaisquer tarefas pendentes ou em execução. Também podem ser submetidas novas tarefas enquanto a operação está em curso. Falhas na operação de dimensionamento normalmente são processadas, para que o cluster está sempre deixado no estado funcional.

    Quando um cluster do Hadoop é dimensionado para baixo, reduzindo o número de nós de dados, alguns dos serviços em cluster são reiniciados. Reiniciar serviços faz com que todos os em execução e trabalhos pendentes a falhar após a conclusão da operação de dimensionamento. Pode, no entanto, a submeter as tarefas depois de concluída a operação.
* HBase

    Perfeitamente pode adicionar ou remover nós ao HBase cluster enquanto estiver em execução. Servidores regionais são equilibrados automaticamente dentro de alguns minutos de concluir a operação de dimensionamento. No entanto, pode também manualmente equilibrar os servidores regionais através do registo headnode do cluster e, em seguida, execute os seguintes comandos a partir de uma janela de linha de comandos:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Storm

    Perfeitamente pode adicionar ou remover nós de dados ao cluster do Storm enquanto estiver em execução. Mas, após uma conclusão com êxito da operação de dimensionamento, terá de reequilibrar a topologia.

    Reequilíbrio pode ser conseguido de duas formas:

  * IU da web do Storm
  * Ferramenta de interface de linha de comandos (CLI)

    Consulte o [documentação do Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A IU da web de Storm está disponível no cluster do HDInsight:

    ![Reequilibre de escala do HDInsight storm](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

    Eis um exemplo como utilizar o comando da CLI para rebalancear a topologia do Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Para alterar o tamanho do cluster de Hadoop com o Azure PowerShell, execute o seguinte comando a partir de uma máquina cliente:

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Conceder/revogar o acesso
Clusters do HDInsight tem os seguintes serviços web HTTP (todos esses serviços tem pontos finais RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por predefinição, estes serviços são concedidos para o acesso. Pode revogar/conceder o acesso. Para revogar:

```powershell
Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Para conceder:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]
> Ao conceder/revogar o acesso ao repor o nome de utilizador do cluster e a palavra-passe.
>
>

Também podem ser feitos conceder e revogar o acesso através do portal. Consulte [administrar HDInsight ao utilizar o portal do Azure][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Atualizar as credenciais de utilizador HTTP
É o mesmo procedimento como [conceder/revogar HTTP acesso](#grant/revoke-access). Se o cluster foi concedido o acesso HTTP, tem primeiro de o revogar.  E, em seguida, conceder o acesso com novas credenciais do utilizador HTTP.

## <a name="find-the-default-storage-account"></a>Localizar a conta do storage predefinida
O script do PowerShell seguinte demonstra como obter o nome de conta do storage predefinida e as informações relacionadas:

```powershell
#Login-AzureRmAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>Localizar o grupo de recursos
No modo Resource Manager, cada cluster do HDInsight pertence a um grupo de recursos do Azure.  Para localizar o grupo de recursos:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Submeter tarefas
**Para submeter tarefas de MapReduce**

Consulte [amostras de MapReduce de execução de Hadoop no HDInsight baseado em Windows](hdinsight-run-samples.md).

**Para submeter tarefas do Hive**

Consulte [executar consultas do Hive com o PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Para submeter tarefas do Pig**

Consulte [tarefas de executar o Pig com o PowerShell](hadoop/apache-hadoop-use-pig-powershell.md).

**Para submeter tarefas Sqoop**

Consulte [utilizar o Sqoop com o HDInsight](hadoop/hdinsight-use-sqoop.md).

**Para submeter tarefas de Oozie**

Consulte [Oozie de utilização com o Hadoop para definir e executar um fluxo de trabalho no HDInsight](hdinsight-use-oozie.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados para o Blob storage do Azure
Consulte [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data].

## <a name="see-also"></a>Veja Também
* [Documentação de referência de cmdlet do HDInsight][hdinsight-powershell-reference]
* [Administrar HDInsight ao utilizar o portal do Azure][hdinsight-admin-portal]
* [Administrar HDInsight utilizando uma interface de linha de comandos][hdinsight-admin-cli]
* [Criar clusters do HDInsight][hdinsight-provision]
* [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data]
* [Submeter tarefas do Hadoop através de programação][hdinsight-submit-jobs]
* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
