---
title: "Através de programação monitorizar tarefas do Stream Analytics | Microsoft Docs"
description: "Saiba como monitorizar programaticamente tarefas do Stream Analytics criadas através de REST APIs, o SDK do Azure ou o PowerShell."
keywords: "monitor de .net, o monitor da tarefa, a monitorização de aplicações"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 2ec02cc9-4ca5-4a25-ae60-c44be9ad4835
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: 7e9d2f6f03fd539c59b105108fb46697bcd60f1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programaticamente criar um monitor de tarefa do Stream Analytics

Este artigo demonstra como ativar a monitorização de uma tarefa de Stream Analytics. Tarefas do Stream Analytics que são criadas através de REST APIs, o SDK do Azure ou o PowerShell não dispõe de monitorização ativada por predefinição. Poderá ativar manualmente-la no portal do Azure ao aceder à página de Monitor da tarefa e clicar no botão Ativar ou pode automatizar este processo, seguindo os passos neste artigo. Os dados de monitorização serão apresentada na área de métricas do portal do Azure para a tarefa de Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este processo, tem de ter o seguinte:

* Visual Studio 2017 ou 2015
* [O SDK .NET do Azure](https://azure.microsoft.com/downloads/) transferido e instalado
* Uma tarefa do Stream Analytics existente que tem de ter a monitorização ativada

## <a name="create-a-project"></a>Criar um projeto

1. Crie uma aplicação de consola do Visual Studio c# .NET.
2. Na consola do Gestor de pacotes, execute os seguintes comandos para instalar os pacotes NuGet. A primeira é o SDK .NET da Azure Stream Analytics Management. Segunda é o SDK de Monitor do Azure que será utilizado para ativar a monitorização. O cliente do Azure Active Directory que será utilizado para a autenticação é o último.
   
   ```
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Adicione a seguinte secção appSettings ao ficheiro App. config.
   
   ```
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Substitua os valores para *SubscriptionId* e *ActiveDirectoryTenantId* com os IDs de subscrição e de inquilino do Azure. Pode obter estes valores, executando o seguinte cmdlet do PowerShell:
   
   ```
   Get-AzureAccount
   ```
4. Adicione as seguintes instruções de utilização para o ficheiro de origem (Program.cs) no projeto.
   
   ```
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Adicione um método de programa auxiliar de autenticação.
   
     cadeia estática pública GetAuthorizationHeader()
   
         {
             AuthenticationResult result = null;
             var thread = new Thread(() =>
             {
                 try
                 {
                     var context = new AuthenticationContext(
                         ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                         ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
   
                     result = context.AcquireToken(
                         resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                         clientId: ConfigurationManager.AppSettings["AsaClientId"],
                         redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                         promptBehavior: PromptBehavior.Always);
                 }
                 catch (Exception threadEx)
                 {
                     Console.WriteLine(threadEx.Message);
                 }
             });
   
             thread.SetApartmentState(ApartmentState.STA);
             thread.Name = "AcquireTokenThread";
             thread.Start();
             thread.Join();
   
             if (result != null)
             {
                 return result.AccessToken;
             }
   
             throw new InvalidOperationException("Failed to acquire token");
     }

## <a name="create-management-clients"></a>Criar os clientes de gestão

O seguinte código irá configurar as variáveis necessárias e os clientes de gestão.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Ativar a monitorização para uma tarefa de Stream Analytics existente

O código seguinte ativa a monitorização para um **existente** tarefa do Stream Analytics. A primeira parte do código efetua um pedido GET contra o serviço de Stream Analytics para obter informações sobre a tarefa de Stream Analytics específica. Utiliza o *Id* propriedade (obtida no pedido GET) como um parâmetro para o método Put na segunda metade do código, o que envia uma PUT o pedido para o serviço de informações para ativar a monitorização para a tarefa de Stream Analytics.

>[!WARNING]
>Se ativou anteriormente monitorização para uma tarefa de Stream Analytics diferente, através do portal do Azure ou através de programação através de abaixo código, **, recomendamos que forneça o mesmo nome de conta de armazenamento que utilizou quando ativou anteriormente a monitorização.**
> 
> A conta de armazenamento está ligada à região que criou a tarefa de Stream Analytics, não especificamente para a tarefa propriamente dito.
> 
> Todos os Stream Analytics tarefas (e todos os outros recursos do Azure) nessa mesma região partilharem esta conta de armazenamento para armazenar dados de monitorização. Se fornecer uma conta de armazenamento diferente, pode provocar indesejados efeitos secundários na monitorização do seu outras tarefas do Stream Analytics ou outros recursos do Azure.
> 
> O nome da conta de armazenamento que utilizar para substituir `<YOUR STORAGE ACCOUNT NAME>` no seguinte código deve ser uma conta de armazenamento que está na mesma subscrição que a tarefa de Stream Analytics que pretende ativar a monitorização.
> 
> 

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Obter suporte

Para obter mais assistência, experimente a nossa [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

