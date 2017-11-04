---
title: "Fragmentos para migrar conteúdos a partir de coleções de área de trabalho do Power BI de código | Microsoft Docs"
description: "Seguem-se alguns fragmentos de código de operações básicas necessárias para a migração de conteúdo."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 64f012322c89236fa8827fddf2f9090d0a167b76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="code-snippets-for-migrating-content-from-power-bi-workspace-collections"></a>Fragmentos de código para a migração de conteúdo a partir de coleções de área de trabalho do Power BI

Este artigo dá-lhe alguns fragmentos de código de operações básicas necessárias para a migração de conteúdo. Para obter fluxos relacionados para determinados tipos de relatório, consulte [como migrar conteúdo de coleção de área de trabalho do Power BI Embedded para Power BI](migrate-from-power-bi-workspace-collections.md#content-migration).

A **ferramenta de migração** está disponível para utilização para melhorar a copiar o conteúdo de coleções de área de trabalho do Power BI para Power BI Embedded. Especialmente se tiver uma grande quantidade de conteúdo. Para obter mais informações, consulte [ferramenta de migração do Power BI Embedded](migrate-tool.md).

O código abaixo são exemplos utilizar c# e a [SDK .NET do Power BI](https://www.nuget.org/profiles/powerbi).

Certifique-se de que está a utilizar os seguintes espaços de nomes para executar os fragmentos de código abaixo.

```
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.PowerBI.Api.V1;
using Microsoft.PowerBI.Api.V1.Models;
using Microsoft.PowerBI.Api.V2;
using Microsoft.PowerBI.Api.V2.Models;
using Microsoft.Rest;
using Microsoft.Rest.Serialization;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
```

## <a name="export-report-from-a-power-bi-workspace-collection-workspace"></a>Exportar o relatório a partir de uma área de trabalho de coleção de área de trabalho do Power BI

```
    // Create a token credentials with "AppKey" type
    var credentials = new TokenCredentials(<myAppKey==>, "AppKey");

    // Instantiate your Power BI client passing in the required credentials
    var client = new PowerBIClient(credentials);

    client.BaseUri = new Uri("https://api.powerbi.com");

    var response = client.Reports.ExportReportWithHttpMessagesAsync(<myWorkspaceCollectionName>, <myWorkspaceId>, <myReportId>);

    if (response.Result.Response.StatusCode == HttpStatusCode.OK)
    {
        var stream = response.Result.Response.Content.ReadAsStreamAsync();

        using (FileStream fileStream = File.Create(@"C:\Migration\myfile.pbix"))
        {
            stream.Result.CopyTo(fileStream);
            fileStream.Close();
        }
    }
```

## <a name="import-report-to-power-bi-embedded"></a>Relatório de importação para o Power BI Embedded

```
    AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/common/oauth2/authorize");
    var PBISaaSAuthResult = authContext.AcquireToken("https://analysis.windows.net/powerbi/api", <myClientId>, new Uri("urn:ietf:wg:oauth:2.0:oob"), PromptBehavior.Always);
    var credentials = new TokenCredentials(PBISaaSAuthResult.AccessToken);
    var client = new PowerBIClient(new Uri($"{"https://api.powerbi.com"}"), credentials);
    using (var file = File.Open(@"C:\Migration\myfile.pbix", FileMode.Open))
    {
        client.Imports.PostImportWithFileInGroup(<mySaaSWorkspaceId>, file, "importedreport", "Abort");
        while (true) ;
    }
```

## <a name="extract-directquery-connection-string-from-power-bi-workspace-collections"></a>Extrair a cadeia de ligação de DirectQuery de coleções de área de trabalho do Power BI

Isto é para atualizar o PBIX depois de migrar para o Power BI Embedded.

```
    // Extract connection string from PaaS - DirectQuery report
    // Create a token credentials with "AppKey" type
    var credentials = new TokenCredentials(<myAppKey==>, "AppKey");

    // Instantiate your Power BI client passing in the required credentials
    var client = new PowerBIClient(credentials);

    client.BaseUri = new Uri("https://api.powerbi.com");

    var reports = client.Reports.GetReports(<myWorkspaceCollectionName>, <myWorkspaceId>);

    Report report = reports.Value.FirstOrDefault(r => string.Equals(r.Id, <myReportId, StringComparison.OrdinalIgnoreCase));

    var datasource = client.Datasets.GetDatasources(<myWorkspaceCollectionName>, <myWorkspaceId>, report.DatasetId);
```

## <a name="update-directquery-connection-string-in-power-bi-embedded"></a>Atualizar a cadeia de ligação de DirectQuery no Power BI Embedded

```
    public class ConnectionString
    {
        [JsonProperty(PropertyName = "connectionString")]
        public string connection { get; set; }
    }

    AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/common/oauth2/authorize");
    var PBISaaSAuthResult = authContext.AcquireToken("https://analysis.windows.net/powerbi/api",<myclient_id>, new Uri("urn:ietf:wg:oauth:2.0:oob"), PromptBehavior.Always);
    var credentials = new TokenCredentials(PBISaaSAuthResult.AccessToken);
    var client = new PowerBIClient(new Uri($"{"https://api.powerbi.com"}"), credentials);

    ConnectionString connection = new ConnectionString() { connection = "data source = <server_name>; initial catalog = <db_name>; persist security info = True; encrypt = True; trustservercertificate = False" };

    client.Datasets.SetAllConnectionsInGroup(<myWorkspaceId>, <dataset_id>, connection);
```

## <a name="set-directquery-credentials-in-power-bi-embedded"></a>Defina credenciais de DirectQuery no Power BI Embedded

Este fragmento, estamos a utilizar as credenciais não encriptadas de simplicidade, envio de credenciais encriptado é suportada, bem como.

```
    public class ConnectionString
    {
        [JsonProperty(PropertyName = "connectionString")]
        public string connection { get; set; }
    }

    public class BasicCreds
    {
        [JsonProperty(PropertyName = "username")]
        public string user { get; set; }

        [JsonProperty(PropertyName = "password")]
        public string pwd { get; set; }
    }

    var basicCreds = new BasicCreds() { user = <sqldb_username>, pwd = <sqldb_password> };
    var body = new SetCredsRequestBody() { credentialType = "Basic", basicCreds = basicCreds };

    var url = string.Format("https://api.powerbi.com/v1.0/myorg/gateways/{0}/datasources/{1}", <gateway_id>, <datasource_id>);
    var request = new HttpRequestMessage(new HttpMethod("PATCH"), url);
    // Set authorization header from you acquired Azure AD token
    AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/common/oauth2/authorize");
    var PBISaaSAuthResult = authContext.AcquireToken("https://analysis.windows.net/powerbi/api", <myclient_id>, new Uri("urn:ietf:wg:oauth:2.0:oob"), PromptBehavior.Always);

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", PBISaaSAuthResult.AccessToken);

    request.Content = new StringContent(JsonConvert.SerializeObject(body), Encoding.UTF8, "application/json");

    HttpClient simpleClient = new HttpClient();
    var response = await simpleClient.SendAsync(request);
```

## <a name="push-dataset-and-report"></a>Push de conjunto de dados e relatório

Terá de recriar o relatório para o conjunto de dados criado.

Este fragmento, partimos do pressuposto que o conjunto de dados pushable já está a ser uma área de trabalho de aplicação no Power BI. Para obter informações sobre a API de push, consulte [enviar dados para um conjunto de dados do Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/).

```
    var credentials = new TokenCredentials(<Your WSC access key>, "AppKey");

    // Instantiate your Power BI client passing in the required credentials
    var client = new Microsoft.PowerBI.Api.V1.PowerBIClient(credentials);
    client.BaseUri = new Uri("https://api.powerbi.com");

    // step 1 -> create dummy dataset at PaaS worksapce
    var fileStream = File.OpenRead(<Path to your dummy dataset>);
    var import = client.Imports.PostImportWithFileAsync(<Your WSC NAME>, <Your workspace ID>, fileStream, "dummyDataset");
    while (import.Result.ImportState != "Succeeded" && import.Result.ImportState != "Failed")
    {
        import = client.Imports.GetImportByIdAsync(<Your WSC NAME>, <Your workspace ID>, import.Result.Id);
        Thread.Sleep(1000);
    }
    var dummyDatasetID = import.Result.Datasets[0].Id;

    // step 2 -> clone the pushable dataset and rebind to dummy dataset
    var cloneInfo = new Microsoft.PowerBI.Api.V1.Models.CloneReportRequest("pushableReportClone",null, dummyDatasetID);
    var clone = client.Reports.CloneReportAsync(<Your WSC NAME>, <Your workspace ID>, <Your pushable report ID>, cloneInfo);
    var pushableReportCloneID = clone.Result.Id;


    // step 3 -> Download the push API clone report with the dummy dataset
    var response = client.Reports.ExportReportWithHttpMessagesAsync(<Your WSC NAME>, <Your workspace ID>, pushableReportCloneID);
    if (response.Result.Response.StatusCode == HttpStatusCode.OK)
    {
        var stream = response.Result.Response.Content.ReadAsStreamAsync();
        using (fileStream = File.Create(@"C:\Migration\PushAPIReport.pbix"))
        {
            stream.Result.CopyTo(fileStream);
            fileStream.Close();
        }
    }

    // step 4 -> Upload dummy PBIX to Power BI Embedded
    AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/common/oauth2/authorize");
    var PBISaaSAuthResult = authContext.AcquireToken("https://analysis.windows.net/powerbi/api", <Your client ID>, new Uri("urn:ietf:wg:oauth:2.0:oob"), PromptBehavior.Always);
    var credentialsSaaS = new TokenCredentials(PBISaaSAuthResult.AccessToken);
    var clientSaaS = new Microsoft.PowerBI.Api.V2.PowerBIClient(new Uri($"{"https://api.powerbi.com"}"), credentialsSaaS);
    using (var file = File.Open(@"C:\Migration\PushAPIReport.pbix", FileMode.Open))
    {

        var importSaaS = clientSaaS.Imports.PostImportWithFileAsyncInGroup(<Your GroupID>, file, "importedreport1", "Abort");
        while (importSaaS.Result.ImportState != "Succeeded" && importSaaS.Result.ImportState != "Failed")
        {
            importSaaS = clientSaaS.Imports.GetImportByIdAsync(importSaaS.Result.Id);
            Thread.Sleep(1000);
        }
        var importedreport1ID = importSaaS.Result.Reports[0].Id;


        // step 5 -> Rebind report to "real" push api dataset
        var rebindInfoSaaS = new Microsoft.PowerBI.Api.V2.Models.RebindReportRequest(<Your pushable dataset  ID at power bi>);
        var rebindSaaS = clientSaaS.Reports.RebindReportInGroupWithHttpMessagesAsync(<Your GroupID>, importedreport1ID, rebindInfoSaaS);

    }
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre o processo de migração, consulte [como migrar conteúdo de coleção de área de trabalho do Power BI para Power BI Embedded](migrate-from-power-bi-workspace-collections.md).

Mais perguntas? [Tente pedir a Comunidade do Power BI](http://community.powerbi.com/)