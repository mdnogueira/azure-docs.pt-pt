---
title: Migrar para as ferramentas do Azure Resource Manager para o HDInsight | Microsoft Docs
description: Como migrar para as ferramentas de desenvolvimento do Azure Resource Manager para clusters do HDInsight
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: 
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e9fe49873a03867bdc55239eb4af623f0587f4f8
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager para clusters do HDInsight

HDInsight está a descontinuar ferramentas baseadas no Azure Service Manager ASM para o HDInsight. Se tiver sido a utilizar PowerShell do Azure, CLI do Azure ou o SDK .NET do HDInsight para trabalhar com clusters do HDInsight, são encorajados a utilizar as versões baseado no Azure Resource Manager ARM do PowerShell, CLI e SDK do .NET passa. Este artigo fornece indicações sobre como migrar para a nova abordagem baseadas em ARM. Independentemente do local onde aplicável, este artigo também pontos terminar as diferenças entre o ASM e ARM abordagens para o HDInsight.

> [!IMPORTANT]
> O suporte de ASM com o PowerShell, CLI, e .NET SDK irá descontinuar no **1 de Janeiro de 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Migrar a CLI do Azure para o Azure Resource Manager
A CLI do Azure agora predefinições para o modo Azure Resource Manager (ARM), a menos que estiver a atualizar uma instalação anterior; Neste caso, poderá ter de utilizar o `azure config mode arm` comando para mudar para modo ARM.

Os comandos básicos que a CLI do Azure fornecida para trabalhar com o HDInsight com gestão de serviço do Azure (ASM) são os mesmos, quando utilizar ARM; No entanto alguns parâmetros e os comutadores poderão ter os nomes dos novos e existem muitas novos parâmetros disponíveis quando com ARM. Por exemplo, pode agora utilizar `azure hdinsight cluster create` para especificar as informações de metastore do Oozie e Azure Virtual Network que um cluster devem ser criados na ou Hive.

Comandos básicos para trabalhar com o HDInsight através do Azure Resource Manager são:

* `azure hdinsight cluster create`-cria um novo cluster do HDInsight
* `azure hdinsight cluster delete`-Elimina o cluster do HDInsight existente
* `azure hdinsight cluster show`-apresentar informações sobre um cluster existente
* `azure hdinsight cluster list`-Apresenta uma lista de clusters do HDInsight para a sua subscrição do Azure

Utilize o `-h` comutador para inspecionar os parâmetros e os comutadores disponíveis para cada comando.

### <a name="new-commands"></a>Novos comandos
Novos comandos disponíveis com o Azure Resource Manager são:

* `azure hdinsight cluster resize`-alterado dinamicamente o número de nós de trabalho no cluster
* `azure hdinsight cluster enable-http-access`-permite o acesso de HTTPs para o cluster (em por predefinição)
* `azure hdinsight cluster disable-http-access`-Desativa o acesso de HTTPs para o cluster
* `azure hdinsight script-action`-fornece comandos para criar/gerir ações de Script num cluster
* `azure hdinsight config`-fornece comandos para criar um ficheiro de configuração que pode ser utilizado com o `hdinsight cluster create` comando para fornecer informações de configuração.

### <a name="deprecated-commands"></a>Comandos preteridos
Se utilizar o `azure hdinsight job` comandos para submeter tarefas ao cluster do HDInsight, estes não estão disponíveis os comandos do ARM. Se precisar de programaticamente submeter tarefas ao HDInsight de scripts, em vez disso, deve utilizar as APIs REST fornecidas pelo HDInsight. Para obter mais informações sobre a submeter as tarefas que utilizam as APIs REST, consulte os seguintes documentos.

* [Executar tarefas de MapReduce com o Hadoop no HDInsight utilizando cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Executar consultas do Hive com o Hadoop no HDInsight utilizando cURL](hadoop/apache-hadoop-use-hive-curl.md)
* [Executar tarefas do Pig com o Hadoop no HDInsight utilizando cURL](hadoop/apache-hadoop-use-pig-curl.md)

Para obter informações sobre outras formas de executar o MapReduce, de ramo de registo e Pig interativamente, consulte [utilizar de MapReduce com o Hadoop no HDInsight](hadoop/hdinsight-use-mapreduce.md), [utilizar o Hive com o Hadoop no HDInsight](hadoop/hdinsight-use-hive.md), e [utilizar o Pig com o Hadoop no HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exemplos
**Criar um cluster**

* Comando antigo (ASM)-`azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Novo comando (ARM)-`azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Eliminar um cluster**

* Comando antigo (ASM)-`azure hdinsight cluster delete myhdicluster`
* Novo comando (ARM)-`azure hdinsight cluster delete mycluster -g myresourcegroup`

**Lista de clusters**

* Comando antigo (ASM)-`azure hdinsight cluster list`
* Novo comando (ARM)-`azure hdinsight cluster list`

> [!NOTE]
> Para o comando de lista, especificando o grupo de recursos utilizando `-g` irá devolver apenas os clusters no grupo de recursos especificado.
> 
> 

**Mostrar informações de cluster**

* Comando antigo (ASM)-`azure hdinsight cluster show myhdicluster`
* Novo comando (ARM)-`azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrar o Azure PowerShell para o Azure Resource Manager
As informações gerais sobre o Azure PowerShell no modo Azure Resource Manager (ARM) podem ser encontradas em [utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

Os cmdlets do Azure ARM do PowerShell podem ser instalado do lado do lado a lado com os cmdlets ASM. Os cmdlets de dois modos podem ser distinguidos pelos respetivos nomes.  O modo ARM tem *AzureRmHDInsight* em comparação com os nomes de cmdlet *AzureHDInsight* no modo ASM.  Por exemplo, *New-AzureRmHDInsightCluster* vs. *Novo AzureHDInsightCluster*. Os parâmetros e comutadores poderão ter nomes de notícias e existem muitas novos parâmetros disponíveis quando utilizar ARM.  Por exemplo, vários cmdlets necessitam de um novo comutador chamado *- ResourceGroupName*. 

Antes de poder utilizar os cmdlets do HDInsight, tem de ligar à sua conta do Azure e criar um novo grupo de recursos:

* Login-AzureRmAccount ou [selecione AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Consulte [autenticar um principal de serviço com o Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Novo-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Cmdlets cujo nome foi alterados
Para listar os cmdlets do HDInsight ASM na consola do Windows PowerShell:

    help *azurermhdinsight*

A tabela seguinte lista os cmdlets ASM e os respetivos nomes no modo ARM:

| Cmdlets ASM | Cmdlets ARM |
| --- | --- |
| Add-AzureHDInsightConfigValues |[AzureRmHDInsightConfigValues adicionar](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[AzureRmHDInsightMetastore adicionar](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[AzureRmHDInsightScriptAction adicionar](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[AzureRmHDInsightStorage adicionar](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Conceder AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Conceder AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[AzureRmHDInsightHiveJob invocar](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[Novo AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[Novo AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[Novo AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[Novo AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[Novo AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[Novo AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[Novo AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remover AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revogar AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revogar AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Conjunto AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Conjunto AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Início AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Utilize AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Espera-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Novos cmdlets
Seguem-se os novos cmdlets que só estão disponíveis no modo ARM. 

**Ação de script cmdlets relacionados:**

* **Get-AzureRmHDInsightPersistedScriptAction**: obtém as ações de script persistentes para um cluster e apresenta uma lista por ordem cronológica ou obtém os detalhes de uma ação de script persistentes especificado. 
* **Get-AzureRmHDInsightScriptActionHistory**: obtém o histórico de ações de script para um cluster e apresenta uma lista de, por ordem cronológica inversa ou obtém os detalhes de uma ação de script anteriormente executados. 
* **Remover AzureRmHDInsightPersistedScriptAction**: Remove uma ação de script persistentes um cluster do HDInsight.
* **Conjunto AzureRmHDInsightPersistedScriptAction**: define uma ação de script anteriormente executados para ser uma ação de script persistentes.
* **AzureRmHDInsightScriptAction submeter**: submete uma nova ação de script para um cluster do Azure HDInsight. 

Para informações adicionais de utilização, consulte [clusters do HDInsight baseado em Linux personalizar através da ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

**Identidade Clsuter cmdlets relacionados:**

* **AzureRmHDInsightClusterIdentity adicionar**: Adiciona uma identidade de cluster para um objeto de configuração de cluster para que o cluster do HDInsight pode aceder aos arquivos do Azure Data Lake. Consulte [criar um cluster do HDInsight com o Data Lake Store com o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exemplos
**Criar cluster**

Comando antigo (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Novo comando (ARM):

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Eliminar o cluster**

Comando antigo (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Novo comando (ARM):

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Cluster de lista**

Comando antigo (ASM):

    Get-AzureHDInsightCluster

Novo comando (ARM):

    Get-AzureRmHDInsightCluster 

**Mostrar o cluster**

Comando antigo (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Novo comando (ARM):

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Outros exemplos
* [Criar clusters do HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Submeter tarefas do Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Submeter tarefas do Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Submeter tarefas Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-arm-based-hdinsight-net-sdk"></a>Migrar para o SDK .NET do HDInsight baseados em ARM
A gestão de serviço do Azure com base em [SDK do .NET (ASM) HDInsight](https://msdn.microsoft.com/library/azure/mt416619.aspx) agora foi preterido. É encorajados a utilizar a gestão de recursos do Azure com base em [SDK do .NET (ARM) HDInsight](https://msdn.microsoft.com/library/azure/mt271028.aspx). Os seguintes pacotes do HDInsight baseado em ASM vão ser preteridos.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Esta secção fornece indicações para obter mais informações sobre como efetuar determinadas tarefas utilizando o SDK baseadas em ARM.

| Como... com o SDK do HDInsight baseados em ARM | Ligações |
| --- | --- |
| Criar clusters do HDInsight com o .NET SDK |Consulte [criar clusters do HDInsight com o .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Personalizar um cluster utilizando a ação de Script com o .NET SDK |Consulte [clusters do HDInsight Linux personalizar através da ação de Script](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Autenticar aplicações interativamente utilizando o Azure Active Directory com o .NET SDK |Consulte [executar consultas do Hive com o .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). O fragmento de código neste artigo utiliza a abordagem de autenticação interativa. |
| Autenticar aplicações de forma não interativa utilizando o Azure Active Directory com o .NET SDK |Consulte [criar aplicações não interativo para o HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Submeter uma tarefa do Hive com o .NET SDK |Consulte [submeter ramo de registo de tarefas](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Submeter uma tarefa Pig com o .NET SDK |Consulte [tarefas do Pig submeter](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Submeter uma tarefa de Sqoop com o .NET SDK |Consulte [Sqoop submeter tarefas](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Lista de clusters do HDInsight com o .NET SDK |Consulte [clusters do HDInsight de lista](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Dimensionamento de clusters do HDInsight com o .NET SDK |Consulte [clusters do HDInsight de escala](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Conceder/revogar o acesso a clusters do HDInsight com o .NET SDK |Consulte [conceder/revogar o acesso a clusters do HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Atualizar as credenciais de utilizador HTTP para clusters do HDInsight com o .NET SDK |Consulte [credenciais de utilizador de HTTP de atualização de clusters do HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Localizar a conta de armazenamento predefinido para clusters do HDInsight com o .NET SDK |Consulte [encontrar a conta de armazenamento predefinido para clusters do HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Eliminar os clusters do HDInsight com o .NET SDK |Consulte [clusters do HDInsight eliminar](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemplos
Seguem-se alguns exemplos sobre como uma operação é efetuada utilizando o SDK do ASM e o fragmento de código equivalente para o SDK baseadas em ARM.

**Criar um cliente CRUD de cluster**

* Comando antigo (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager (ARM)
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Novo comando (ARM) (autorização de principal de serviço)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Novo comando (ARM) (autorização do utilizador)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Criar um cluster**

* Comando antigo (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Novo comando (ARM)
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Ativar o acesso HTTP**

* Comando antigo (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Novo comando (ARM)
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Eliminar um cluster**

* Comando antigo (ASM)
  
        client.DeleteCluster(dnsName);
* Novo comando (ARM)
  
        client.Clusters.Delete(resourceGroup, dnsname);

