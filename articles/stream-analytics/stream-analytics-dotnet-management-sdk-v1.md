---
title: "Gestão .NET v1.x do SDK do Azure Stream Analytics | Microsoft Docs"
description: "Introdução ao SDK do .NET de gestão do fluxo de análise. Saiba como configurar e executar tarefas de análise. Crie um projeto, entradas, saídas e transformações."
keywords: ".NET SDK, análise de API"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: samacha
ms.openlocfilehash: cd8f4e0dd07be8878d6bbb1739fb84a3cc96a186
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="management-net-sdk-v1x-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Gestão .NET SDK v1.x: definir a configurar e executar tarefas de análise utilizando a API do Azure Stream Analytics para .NET
Saiba como configurar e executar tarefas de análise utilizando a API do Stream Analytics para .NET utilizando o SDK .NET de gestão. Configurar um projeto, crie as origens de entrada e saídas, transformações e iniciar e parar tarefas. Para as tarefas de análise, pode transmitir dados de armazenamento de BLOBs ou de um hub de eventos.

Consulte o [documentação de referência de gestão para a API do Stream Analytics para o .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

O Azure Stream Analytics é um serviço completamente gerido que fornece processamento de eventos de baixa latência, elevada disponibilidade, dimensionáveis, complexo através de transmissão em fluxo de dados na nuvem. Do Stream Analytics permite que os clientes configurar as tarefas de transmissão em fluxo para analisar os fluxos de dados e permite-lhes unidade perto de análise em tempo real.  

> [!NOTE]
> Código de exemplo neste artigo ainda utiliza a versão de legado (1. x) do SDK do Azure Stream Analytics Management .NET. Para o código de exemplo utilizando a versão atualizada do SDK, consulte [utilizar o SDK .NET de gestão para o Stream Analytics](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-dotnet-management-sdk).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* Instale Visual Studio 2017 ou 2015.
* Transfira e instale [SDK .NET da Azure](https://azure.microsoft.com/downloads/).
* Crie um grupo de recursos do Azure na sua subscrição. Segue-se um exemplo de script do PowerShell do Azure. Para obter informações do Azure PowerShell, consulte [instalar e configurar o Azure PowerShell](/powershell/azure/overview);  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* Configure uma origem de entrada e de destino de saída para utilizar. Para obter mais informações, consulte as instruções [adicionar entradas](stream-analytics-add-inputs.md) para configurar uma entrada de exemplo e [adicione saídas](stream-analytics-add-outputs.md) para configurar uma saída de exemplo.

## <a name="set-up-a-project"></a>Configurar um projeto
Para criar uma tarefa de análise, utilize a API do Stream Analytics para o .NET, configure primeiro o projeto.

1. Crie uma aplicação de consola do Visual Studio c# .NET.
2. Na consola do Gestor de pacotes, execute os seguintes comandos para instalar os pacotes NuGet. A primeira é o SDK .NET da Azure Stream Analytics Management. Segunda é o cliente do Azure Active Directory que será utilizado para autenticação.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 1.8.3
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.4
3. Adicione o seguinte **appSettings** secção para o ficheiro App. config:
   
        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>

    Substitua os valores para **SubscriptionId** e **ActiveDirectoryTenantId** com os IDs de subscrição e de inquilino do Azure. Pode obter estes valores, executando o seguinte cmdlet do PowerShell do Azure:

        Get-AzureAccount

4. Adicione a seguinte referência de ficheiro. csproj:

        <Reference Include="System.Configuration" />

1. Adicione o seguinte **utilizando** instruções para o ficheiro de origem (Program.cs) no projeto:
   
        using System;
        using System.Configuration;
        using System.Threading.Tasks;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
2. Adicione um método de programa auxiliar de autenticação:

   ```   
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>Criar um cliente de gestão do Stream Analytics
A **StreamAnalyticsManagementClient** objeto permite-lhe gerir a tarefa e os componentes de tarefa, tal como entrada, saída e a transformação.

Adicione o seguinte código para o início do **Main** método:

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
        ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

O **resourceGroupName** valor da variável deve ser igual ao nome do grupo de recursos criado ou selecionado nos passos de pré-requisitos.

Para automatizar o aspeto de apresentação da credencial da criação da tarefa, consulte [autenticar um principal de serviço com o Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).

As secções restantes deste artigo partem do princípio de que este código está no início do **Main** método.

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics
O código seguinte cria uma tarefa de Stream Analytics sob o grupo de recursos que foram definidos. Irá adicionar uma entrada, saída e a transformação para a tarefa mais tarde.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Criar uma origem de entrada do Stream Analytics
O código seguinte cria uma origem de entrada do Stream Analytics com o tipo de origem de entrada de blob e a serialização de CSV. Para criar uma origem de entrada de hub de eventos, utilize **EventHubStreamInputDataSource** em vez de **BlobStreamInputDataSource**. Da mesma forma, pode personalizar o tipo de serialização de origem de entrada.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Origens de entrada, do armazenamento de BLOBs ou um hub de eventos estão associadas a uma tarefa específica. Para utilizar a mesma origem de entrada para tarefas diferentes, tem de chamar o método novo e especifique um nome de tarefa diferente.

## <a name="test-a-stream-analytics-input-source"></a>Testar uma origem de entrada do Stream Analytics
O **Testarligação** método testa se a tarefa de Stream Analytics é capaz de ligar à origem de entrada, bem como outros aspetos específicos para o tipo de origem de entrada. Por exemplo, no blob origem de entrada que criou no passo anterior, o método irá verificar que o nome da conta de armazenamento e um par de chaves pode ser utilizado para ligar à conta de armazenamento, bem como verificar que o contentor especificado existe.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Criar um destino de saída do Stream Analytics
A criação de um destino de saída é muito semelhante à criação de uma origem de entrada do Stream Analytics. Como origens de entrada, saída destinos estão associados a uma tarefa específica. Para utilizar o mesmo destino de saída para tarefas diferentes, tem de chamar o método novo e especifique um nome de tarefa diferente.

O código seguinte cria um destino de saída (base de dados SQL do Azure). Pode personalizar o tipo de dados de destino de saída e/ou tipo de serialização.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Testar um destino de saída do Stream Analytics
Também tem um destino de saída do Stream Analytics o **Testarligação** método para ligações de teste.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Criar uma transformação de Stream Analytics
O código seguinte cria uma transformação de Stream Analytics com a consulta "selecionar * de entrada" e especifica ao alocar uma unidade de transmissão em fluxo para a tarefa de Stream Analytics. Para obter mais informações sobre o ajuste de unidades de transmissão em fluxo, consulte [tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md).

    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Tal como entrada e saída, uma transformação está também associada à tarefa do Stream Analytics específica que foi criada em.

## <a name="start-a-stream-analytics-job"></a>Iniciar uma tarefa de Stream Analytics
Depois de criar uma tarefa de Stream Analytics e a transformação, saídas e input(s), pode iniciar a tarefa chamando a **iniciar** método.

Uma tarefa de Stream Analytics com uma hora de início de saída personalizado como 12 de Dezembro de 2012, 12:12:12 de inicia de código de exemplo seguintes UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);

## <a name="stop-a-stream-analytics-job"></a>Parar uma tarefa de Stream Analytics
Pode parar uma tarefa de Stream Analytics execução ao chamar o **parar** método.

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Eliminar uma tarefa de Stream Analytics
O **eliminar** método irá eliminar a tarefa, bem como os recursos de secundárias subjacentes, incluindo input(s), saídas e a transformação da tarefa.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);

## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
Aprendeu as noções básicas da utilização de um SDK .NET para criar e executar tarefas de análise. Para saber mais, consulte o seguinte:

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Gestão de análise do Azure Stream .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
