---
title: "Personalizar Clusters do HDInsight utilizando ações de script - Azure | Microsoft Docs"
description: "Saiba como personalizar clusters do HDInsight através da ação de Script."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3a63e216-4163-40c1-aa04-6b42fd0162ad
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: ec95b6d66c71b4278dd1e16807fcc75f5e8b1c36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Personalizar clusters do HDInsight baseado em Windows através da ação de Script
**Ação de script** pode ser utilizado para invocar [scripts personalizados](hdinsight-hadoop-script-actions.md) durante o processo de criação do cluster para instalar software adicional num cluster.

As informações neste artigo são específicas para clusters do HDInsight baseados em Windows. Para clusters baseados em Linux, consulte [clusters do HDInsight baseado em Linux personalizar através da ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

> [!IMPORTANT]
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

Clusters do HDInsight podem ser personalizados numa variedade de outras formas, bem como, por exemplo, incluindo contas de armazenamento do Azure adicionais, alterar o Hadoop ficheiros de configuração (Core-site.XML site.xml do hive, etc.) ou adicionar partilhado bibliotecas (por exemplo, do Hive, Oozie) para localizações de comuns no cluster. Estas personalizações podem ser feitas através do Azure PowerShell, o SDK .NET da Azure HDInsight ou o portal do Azure. Para obter mais informações, consulte [clusters do Hadoop criar no HDInsight][hdinsight-provision-cluster].

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Ação de script no processo de criação de cluster
Ação de script é utilizada apenas enquanto está a ser criado um cluster. O diagrama seguinte ilustra quando a ação de Script é executada durante o processo de criação:

![Personalização de cluster do HDInsight e fases durante a criação do cluster][img-hdi-cluster-states]

Quando o script está em execução, o cluster introduz o **ClusterCustomization** fase. Nesta fase, o script é executado na conta de administrador de sistema, em paralelo em todos os nós especificado no cluster e fornece privilégios de administrador total em nós.

> [!NOTE]
> Porque o utilizador tem privilégios de administrador em nós do cluster durante a **ClusterCustomization** fase, pode utilizar o script para efetuar operações como parar e iniciar serviços, incluindo os serviços relacionados com o Hadoop. Por isso, como parte do script, tem de se certificar de que os serviços do Ambari e outros serviços relacionados com o Hadoop estão em execução antes do script é executado concluída. Estes serviços são necessários para ascertain com êxito o estado de funcionamento e o estado do cluster enquanto está a ser criada. Se alterar qualquer configuração em cluster que afeta a estes serviços, tem de utilizar as funções de programa auxiliar que são fornecidas. Para mais informações sobre funções de programa auxiliar, consulte [scripts de ação de Script desenvolver para o HDInsight][hdinsight-write-script].
>
>

O resultado e os registos de erros para o script são armazenados na conta de armazenamento predefinido especificado para o cluster. Os registos são armazenados numa tabela com o nome **u < \cluster-name-fragment >< \time-stamp > setuplog**. Estes são registos agregados do script executado em todos os nós (nó principal e nós de trabalho) no cluster.

Cada cluster pode aceitar várias ações de script são invocadas pela ordem na qual são especificados. Um script pode ser executado no nó principal, os nós de trabalho ou ambos.

O HDInsight fornece vários scripts para instalar os seguintes componentes nos clusters do HDInsight:

| Nome | Script |
| --- | --- |
| **Instalar o Spark** |https://hdiconfigactions.blob.Core.Windows.NET/sparkconfigactionv03/spark-Installer-v03.ps1. Consulte [instalar e utilizar clusters do Spark no HDInsight][hdinsight-install-spark]. |
| **Instalar o R** |https://hdiconfigactions.blob.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Consulte [instalar e utilizar R nos clusters do HDInsight][hdinsight-install-r]. |
| **Instalar Solr** |https://hdiconfigactions.blob.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Consulte [instalar e utilizar Solr no HDInsight clusters](hdinsight-hadoop-solr-install.md). |
| - **Instalar Giraph** |https://hdiconfigactions.blob.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Consulte [instalar e utilizar Giraph no HDInsight clusters](hdinsight-hadoop-giraph-install.md). |
| **Pré-carregar as bibliotecas Hive** |https://hdiconfigactions.blob.Core.Windows.NET/setupcustomhivelibsv01/Setup-customhivelibs-v01.ps1. Consulte [bibliotecas adicionar Hive nos clusters do HDInsight](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="call-scripts-using-the-azure-portal"></a>Scripts de chamada no portal do Azure
**No portal do Azure**

1. Começar a criar um cluster, conforme descrito em [clusters do Hadoop criar no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
2. Em configuração opcional, para o **ações de Script** painel, clique em **adicionar ação de script** para fornecer os detalhes sobre a ação de script, conforme mostrado abaixo:

    ![Utilize a ação de Script para personalizar um cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "utilize a ação de Script para personalizar um cluster")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script.</td></tr>
        <tr><td>URI de script</td>
            <td>Especifique o URI para o script que é invocado para personalizar o cluster. s</td></tr>
        <tr><td>HEAD/trabalho</td>
            <td>Especifique os nós (**Head** ou **trabalho**) no qual é executado o script de personalização.</b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário, o script.</td></tr>
    </table>

    Prima ENTER para adicionar mais do que uma ação de script para instalar vários componentes no cluster.
3. Clique em **selecione** para guardar a configuração de ação de script e continuar com a criação do cluster.

## <a name="call-scripts-using-azure-powershell"></a>Scripts de chamada com o Azure PowerShell
Este script do PowerShell seguinte demonstra como instalar o Spark no cluster do HDInsight baseado em Windows.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.

    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"

    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    #############################################################
    # Connect to Azure
    #############################################################

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #############################################################
    # Prepare the dependent components
    #############################################################

    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext

    #############################################################
    # Create cluster with ApacheSpark
    #############################################################

    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey


    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `

    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Para instalar outro software, terá de substituir o ficheiro de script no script:

Quando lhe for pedido, introduza as credenciais para o cluster. Pode demorar vários minutos antes do cluster for criado.

## <a name="call-scripts-using-net-sdk"></a>Scripts de chamada utilizando o .NET SDK
O exemplo seguinte demonstra como instalar o Spark no cluster do HDInsight baseado em Windows. Para instalar outro software, terá de substituir o ficheiro de script no código.

**Para criar um cluster do HDInsight com o Spark**

1. Crie uma aplicação de consola c# no Visual Studio.
2. A partir da consola do Gestor de pacotes Nuget, execute o seguinte comando.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight
3. Utilize as seguintes instruções de utilização no ficheiro Program.cs:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
4. Coloque o código na classe com o seguinte:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId;
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,
                DefaultStorageInfo = new AzureStorageInfo(ExistingStorageName, ExistingStorageKey, ExistingContainer),
                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
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
5. Prima **F5** para executar a aplicação.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para o software de open source utilizados nos clusters do HDInsight
O serviço do Microsoft Azure HDInsight é uma plataforma flexível que lhe permite criar aplicações de macrodados na nuvem utilizando um ecossistema de tecnologias de open source formado em torno do Hadoop. Microsoft Azure fornece um nível geral de suporte para tecnologias de open source, tal como explicado o **âmbito suporta** secção o <a href="http://azure.microsoft.com/support/faq/" target="_blank">site FAQ de suporte do Azure</a>. O serviço de HDInsight fornece um nível adicional de suporte para alguns dos componentes, conforme descrito abaixo.

Existem dois tipos de componentes de open source que estão disponíveis no serviço do HDInsight:

* **Componentes incorporados** -estes componentes são previamente instalados nos clusters do HDInsight e fornecer a funcionalidade principal do cluster. Por exemplo, YARN ResourceManager, o idioma de consulta do Hive (HiveQL) e a biblioteca de Mahout pertencem a esta categoria. Uma lista completa dos componentes de cluster está disponível no [Novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight?](hdinsight-component-versioning.md) </a>.
* **Componentes personalizados** -, como um utilizador do cluster, pode instalar ou utilizar na sua carga de trabalho qualquer componente disponível na Comunidade ou criado por si.

Componentes incorporados são totalmente suportados e Support da Microsoft irá ajudá-lo para isolar e resolver problemas relacionados com estes componentes.

> [!WARNING]
> Componentes fornecidos com o cluster do HDInsight são totalmente suportados e Support da Microsoft irá ajudá-lo para isolar e resolver problemas relacionados com estes componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudar a resolver o problema. Isto pode resultar na resolução do problema ou pedir-lhe para interagir com os canais disponíveis para as tecnologias de open source para onde se encontra profundo conhecimentos para que a tecnologia. Por exemplo, existem vários sites de Comunidade que podem ser utilizadas, como: [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos do Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).
>
>

O serviço de HDInsight fornece várias formas de utilizar componentes personalizados. Independentemente de como um componente é utilizado ou instalado no cluster, aplica-se o mesmo nível de suporte. Segue-se uma lista das formas mais comuns que os componentes personalizados podem ser utilizados nos clusters do HDInsight:

1. Submissão de tarefa - Hadoop ou outros tipos de tarefas que executarem ou utilizam componentes personalizados pode ser submetido para o cluster.
2. Personalização de cluster - durante a criação do cluster, pode especificar definições adicionais e componentes personalizados que serão instalados em nós de cluster.
3. Amostras - para componentes personalizados populares, a Microsoft e outras pessoas podem fornecer exemplos de como estes componentes podem ser utilizados nos clusters do HDInsight. Estes exemplos são fornecidos sem suporte.

## <a name="develop-script-action-scripts"></a>Desenvolver scripts de ação de Script
Consulte [scripts de ação de Script desenvolver para o HDInsight][hdinsight-write-script].

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
