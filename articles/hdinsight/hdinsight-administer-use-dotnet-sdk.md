---
title: Gerir clusters do Hadoop no HDInsight com o .NET SDK - Azure | Microsoft Docs
description: Saiba como efetuar tarefas administrativas para os clusters do Hadoop no HDInsight com o SDK .NET do HDInsight.
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: 
ms.assetid: fd134765-c2a0-488a-bca6-184d814d78e9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: jgao
ms.openlocfilehash: e3e67ab865a68867a3f76fe4cce51685f9ea98dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Gerir clusters do Hadoop no HDInsight ao utilizar o .NET SDK
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como gerir clusters do HDInsight utilizando [HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).

**Pré-requisitos**

Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Ligar para o Azure HDInsight

Terá dos seguintes pacotes de NuGet:

```
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

O exemplo de código seguinte mostra como ligar ao Azure antes de poder administrar clusters do HDInsight na sua subscrição do Azure.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Deverá ver uma linha de comandos quando executar este programa.  Se não pretender ver a linha de comandos, consulte o artigo [criar aplicações do HDInsight de .NET de autenticação não interativa](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

## <a name="create-clusters"></a>Criar clusters
Consulte [baseado em Linux criar clusters HDInsight utilizando o SDK .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

## <a name="list-clusters"></a>Lista de clusters
O fragmento de código seguinte apresenta uma lista de clusters e algumas propriedades:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Eliminar clusters
Utilize o seguinte fragmento de código para eliminar um cluster de forma síncrona ou assíncrona: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
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
  
    Quando um cluster do Hadoop é dimensionado para baixo, reduzindo o número de nós de dados, alguns dos serviços em cluster são reiniciados. Isto faz com que todos os em execução e trabalhos pendentes a falhar após a conclusão da operação de dimensionamento. Pode, no entanto, a submeter as tarefas depois de concluída a operação.
* HBase
  
    Perfeitamente pode adicionar ou remover nós ao HBase cluster enquanto estiver em execução. Servidores regionais são equilibrados automaticamente dentro de alguns minutos de concluir a operação de dimensionamento. No entanto, pode equilibrar também manualmente os servidores regionais por iniciar sessão nos headnode do cluster e executando os comandos seguintes a partir de uma janela de linha de comandos:
  
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
    
    ![Reequilibre de escala do HDInsight Storm](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Eis um exemplo como utilizar o comando da CLI para rebalancear a topologia do Storm:
    
    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

O fragmento de código seguinte mostra como redimensionar um cluster de forma síncrona ou assíncrona:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Conceder/revogar o acesso
Clusters do HDInsight tem os seguintes serviços web HTTP (todos esses serviços tem pontos finais RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por predefinição, estes serviços são concedidos para o acesso. Pode revogar/conceder o acesso. Para revogar:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Para conceder:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]
> Por conceder/revogar o acesso irá repor a palavra-passe e nome de utilizador do cluster.
> 
> 

Isto também pode ser feito através do Portal. Consulte [administrar HDInsight ao utilizar o portal do Azure][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Atualizar as credenciais de utilizador HTTP
É o mesmo procedimento como [conceder/revogar HTTP acesso](#grant/revoke-access). Se o cluster foi concedido o acesso HTTP, tem primeiro de o revogar.  E, em seguida, conceder o acesso com novas credenciais do utilizador HTTP.

## <a name="find-the-default-storage-account"></a>Localizar a conta do storage predefinida
O fragmento de código seguinte demonstra como obter o nome de conta do storage predefinida e a chave de conta de armazenamento predefinido para um cluster.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Submeter tarefas
**Para submeter tarefas de MapReduce**

Consulte [amostras de MapReduce de execução de Hadoop no HDInsight](hdinsight-hadoop-run-samples-linux.md).

**Para submeter tarefas do Hive** 

Consulte [executar consultas do Hive com o .NET SDK](hdinsight-hadoop-use-hive-dotnet-sdk.md).

**Para submeter tarefas do Pig**

Consulte [tarefas de executar o Pig com o .NET SDK](hdinsight-hadoop-use-pig-dotnet-sdk.md).

**Para submeter tarefas Sqoop**

Consulte [utilizar o Sqoop com o HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md).

**Para submeter tarefas de Oozie**

Consulte [Oozie de utilização com o Hadoop para definir e executar um fluxo de trabalho no HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados para o Blob storage do Azure
Consulte [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data].

## <a name="see-also"></a>Veja Também
* [Documentação de referência do SDK .NET do HDInsight](https://msdn.microsoft.com/library/mt271028.aspx)
* [Administrar HDInsight ao utilizar o portal do Azure][hdinsight-admin-portal]
* [Administrar HDInsight utilizando uma interface de linha de comandos][hdinsight-admin-cli]
* [Criar clusters do HDInsight][hdinsight-provision]
* [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data]
* [Get started with Azure HDInsight (Introdução ao Azure HDInsight)][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


