---
title: ".NET SDK de gestão do Azure Stream Analytics | Microsoft Docs"
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
ms.openlocfilehash: f17225d92fc35a6da9f6aa3cb0397569665e95e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>.NET SDK de gestão: Configurar e executar tarefas de análise utilizando a API do Azure Stream Analytics para .NET
Saiba como configurar e executar tarefas de análise utilizando a API do Stream Analytics para .NET utilizando o SDK .NET de gestão. Configurar um projeto, crie as origens de entrada e saídas, transformações e iniciar e parar tarefas. Para as tarefas de análise, pode transmitir dados de armazenamento de BLOBs ou de um hub de eventos.

Consulte o [documentação de referência de gestão para a API do Stream Analytics para o .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

O Azure Stream Analytics é um serviço completamente gerido que fornece processamento de eventos de baixa latência, elevada disponibilidade, dimensionáveis, complexo através de transmissão em fluxo de dados na nuvem. Do Stream Analytics permite que os clientes configurar as tarefas de transmissão em fluxo para analisar os fluxos de dados e permite-lhes unidade perto de análise em tempo real.  

> [!NOTE]
> Foi atualizado com o código de exemplo neste artigo com a versão do SDK .NET da Azure Stream Analytics gestão v2.x. Para utilizar a versão do SDK lagecy (1. x) utiliza um código de exemplo, consulte [utilizar v1.x o SDK .NET de gestão para o Stream Analytics](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

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
2. Na consola do Gestor de pacotes, execute os seguintes comandos para instalar os pacotes NuGet. A primeira é o SDK .NET da Azure Stream Analytics Management. Segunda destina-se a autenticação de cliente do Azure.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
3. Adicione o seguinte **appSettings** secção para o ficheiro App. config:
   
        <appSettings>
          <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
          <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
        </appSettings>

    Substitua os valores para **SubscriptionId** e **ActiveDirectoryTenantId** com os IDs de subscrição e de inquilino do Azure. Pode obter estes valores, executando o seguinte cmdlet do PowerShell do Azure:

        Get-AzureAccount

4. Adicione a seguinte referência de ficheiro. csproj:

        <Reference Include="System.Configuration" />

5. Adicione o seguinte **utilizando** instruções para o ficheiro de origem (Program.cs) no projeto:
   
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Threading;
        using System.Threading.Tasks;
        
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Rest;
6. Adicione um método de programa auxiliar de autenticação:

   ```
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Criar um cliente de gestão do Stream Analytics
A **StreamAnalyticsManagementClient** objeto permite-lhe gerir a tarefa e os componentes de tarefa, tal como entrada, saída e a transformação.

Adicione o seguinte código para o início do **Main** método:

   ```
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

O **resourceGroupName** valor da variável deve ser igual ao nome do grupo de recursos criado ou selecionado nos passos de pré-requisitos.

Para automatizar o aspeto de apresentação da credencial da criação da tarefa, consulte [autenticar um principal de serviço com o Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).

As secções restantes deste artigo partem do princípio de que este código está no início do **Main** método.

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics
O código seguinte cria uma tarefa de Stream Analytics sob o grupo de recursos que foram definidos. Irá adicionar uma entrada, saída e a transformação para a tarefa mais tarde.

   ```
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Criar uma origem de entrada do Stream Analytics
O código seguinte cria uma origem de entrada do Stream Analytics com o tipo de origem de entrada de blob e a serialização de CSV. Para criar uma origem de entrada de hub de eventos, utilize **EventHubStreamInputDataSource** em vez de **BlobStreamInputDataSource**. Da mesma forma, pode personalizar o tipo de serialização de origem de entrada.

   ```
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Origens de entrada, do armazenamento de BLOBs ou um hub de eventos estão associadas a uma tarefa específica. Para utilizar a mesma origem de entrada para tarefas diferentes, tem de chamar o método novo e especifique um nome de tarefa diferente.

## <a name="test-a-stream-analytics-input-source"></a>Testar uma origem de entrada do Stream Analytics
O **Testarligação** método testa se a tarefa de Stream Analytics é capaz de ligar à origem de entrada, bem como outros aspetos específicos para o tipo de origem de entrada. Por exemplo, no blob origem de entrada que criou no passo anterior, o método irá verificar que o nome da conta de armazenamento e um par de chaves pode ser utilizado para ligar à conta de armazenamento, bem como verificar que o contentor especificado existe.

   ```
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Criar um destino de saída do Stream Analytics
A criação de um destino de saída é muito semelhante à criação de uma origem de entrada do Stream Analytics. Como origens de entrada, saída destinos estão associados a uma tarefa específica. Para utilizar o mesmo destino de saída para tarefas diferentes, tem de chamar o método novo e especifique um nome de tarefa diferente.

O código seguinte cria um destino de saída (base de dados SQL do Azure). Pode personalizar o tipo de dados de destino de saída e/ou tipo de serialização.

   ```
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testar um destino de saída do Stream Analytics
Também tem um destino de saída do Stream Analytics o **Testarligação** método para ligações de teste.

   ```
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Criar uma transformação de Stream Analytics
O código seguinte cria uma transformação de Stream Analytics com a consulta "selecionar * de entrada" e especifica ao alocar uma unidade de transmissão em fluxo para a tarefa de Stream Analytics. Para obter mais informações sobre o ajuste de unidades de transmissão em fluxo, consulte [tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md).

   ```
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Tal como entrada e saída, uma transformação está também associada à tarefa do Stream Analytics específica que foi criada em.

## <a name="start-a-stream-analytics-job"></a>Iniciar uma tarefa de Stream Analytics
Depois de criar uma tarefa de Stream Analytics e a transformação, saídas e input(s), pode iniciar a tarefa chamando a **iniciar** método.

Uma tarefa de Stream Analytics com uma hora de início de saída personalizado como 12 de Dezembro de 2012, 12:12:12 de inicia de código de exemplo seguintes UTC:

   ```
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Parar uma tarefa de Stream Analytics
Pode parar uma tarefa de Stream Analytics execução ao chamar o **parar** método.

   ```
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Eliminar uma tarefa de Stream Analytics
O **eliminar** método irá eliminar a tarefa, bem como os recursos de secundárias subjacentes, incluindo input(s), saídas e a transformação da tarefa.

   ```
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

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
