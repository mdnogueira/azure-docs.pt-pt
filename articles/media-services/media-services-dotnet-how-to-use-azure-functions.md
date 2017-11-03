---
title: "Desenvolver as funções do Azure com os Media Services"
description: "Este tópico mostra como começar a desenvolver as funções do Azure com os Media Services utilizando o portal do Azure."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/03/2017
ms.author: juliako
ms.openlocfilehash: e8cad53d95186f4f7679d1f19f339ad4149059a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="develop-azure-functions-with-media-services"></a>Desenvolver as funções do Azure com os Media Services

Este tópico mostra como começar com a criação das funções do Azure que utilizam os serviços de suporte de dados. A função de Azure definida neste tópico monitoriza um contentor de conta de armazenamento com o nome **entrada** para novos ficheiros MP4. Depois de um ficheiro é ignorado para o contentor de armazenamento, o acionador de blob irá executar a função. Para rever as funções do Azure, consulte [descrição geral](../azure-functions/functions-overview.md) e outros tópicos do **as funções do Azure** secção.

Se pretende explorar e implementar as funções do Azure existente que utilizar Media Services do Azure, veja [das funções do suporte de dados de serviços do Azure](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Este repositório inclui exemplos que utilizam os serviços de suporte de dados para mostrar os fluxos de trabalho relacionados para ingestão relacionadas com o conteúdo diretamente do armazenamento de BLOBs, codificação e escrever o conteúdo de volta para o blob storage. Também inclui exemplos de como monitorizar as notificações de tarefa por WebHooks e filas do Azure. Também pode desenvolver as suas funções com base nos exemplos de [das funções do suporte de dados de serviços do Azure](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) repositório. Para implementar as funções, prima a **implementar no Azure** botão.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de poder criar a sua primeira função, tem de ter uma conta do Azure ativa. Se ainda não tiver uma conta do Azure, [estão disponíveis contas gratuitas](https://azure.microsoft.com/free/).
- Se pretender criar as funções do Azure que efetuam ações na sua conta de serviços de suporte de dados do Azure (AMS) ou escutar eventos enviados pelos Media Services, deve criar uma conta de AMS, conforme descrito [aqui](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Criar uma aplicação de função

1. Aceda ao [portal do Azure](http://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Criar uma aplicação de função, conforme descrito [aqui](../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Uma conta do storage que especificar no **StorageConnection** variável de ambiente (consulte o passo seguinte) deve estar na mesma região que a sua aplicação.

## <a name="configure-function-app-settings"></a>Configurar definições da aplicação de função

Quando desenvolver as funções de Media Services, é útil para adicionar as variáveis de ambiente que serão utilizadas em toda as suas funções. Para configurar as definições de aplicação, clique na hiperligação de configurar as definições de aplicação. Para obter mais informações, consulte [como configurar as definições de aplicação de função do Azure](../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

A função, definida neste artigo, parte do princípio de que tem as seguintes variáveis de ambiente nas definições da aplicação:

**AMSAADTenantDomain** : ponto final de inquilino do Azure AD. Para obter mais informações sobre a ligação para a API do AMS, consulte [isto](media-services-use-aad-auth-to-access-ams-api.md) artigo.

**AMSRESTAPIEndpoint** : URI que representa o ponto final de REST API. 

**AMSClientId** : ID de cliente da aplicação do Azure AD.

**AMSClientSecret**: segredo de cliente de aplicação do Azure AD.

**StorageConnection** : ligação de armazenamento da conta associada à conta de Media Services. Este valor é utilizado o **function.json** ficheiro e **run.csx** ficheiro (descrito abaixo).

## <a name="create-a-function"></a>Criar uma função

Assim que a sua aplicação de função é implementada, pode encontrá-lo entre **serviços aplicacionais** das funções do Azure.

1. Selecione a sua aplicação de função e clique em **nova função**.
2. Escolha o **c#** idioma e **processamento de dados** cenário.
3. Escolha **BlobTrigger** modelo. Esta função será acionada sempre que um blob é carregado para o **entrada** contentor. O **entrada** nome é especificado no **caminho**, no próximo passo.

    ![Ficheiros](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Depois de selecionar **BlobTrigger**, alguns controlos mais aparecerá na página.

    ![Ficheiros](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Clique em **Criar**. 

## <a name="files"></a>Ficheiros

A função do Azure está associada com ficheiros de código e outros ficheiros que são descritos nesta secção. Ao utilizar o portal do Azure para criar uma função, **function.json** e **run.csx** são criados por si. Terá de adicionar ou carregar um **project.json** ficheiro. O resto desta secção fornece uma breve explicação de cada ficheiro e mostra as respetivas definições.

![Ficheiros](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>Function.JSON

O ficheiro de function.json define os enlaces de função e outras definições de configuração. O tempo de execução utiliza este ficheiro para determinar os eventos a monitorizar e como transmitir dados para e devolver dados de execução de função. Para obter mais informações, consulte [enlaces HTTP e webhook das funções do Azure](../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Definir o **desativada** propriedade **verdadeiro** para impedir que a função a ser executado. 

Substitua o conteúdo do ficheiro function.json existente pelo código seguinte:

```
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>Project.JSON

O ficheiro de project.json contém dependências. Eis um exemplo de **project.json** ficheiro que inclui os pacotes de Media Services do Azure .NET necessários do Nuget. Tenha em atenção que os números de versão mudará com as atualizações mais recentes para os pacotes, pelo que deve confirmar as versões mais recentes. 

Adicione a seguinte definição à project.json. 

```
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>Run.csx

Este é o código c# para a sua função.  A função definida abaixo monitores um contentor de conta de armazenamento com o nome **entrada** (que é o que foi especificado no caminho) para os novos ficheiros MP4. Depois de um ficheiro é ignorado para o contentor de armazenamento, o acionador de blob irá executar a função.
    
O exemplo definido nesta secção demonstra 

1. como um recurso de inserção para uma conta de Media Services (por coping um blob para um elemento de AMS) e 
2. como submeter uma tarefa de codificação que utiliza o codificador de multimédia Standard "Transmissão em fluxo adaptável" da configuração predefinida.

No cenário de vida real, provavelmente pretende controlar o progresso da tarefa e, em seguida, publicar o elemento codificado. Para obter mais informações, consulte [utilização do Azure WebHooks para monitorizar as notificações de tarefa de Media Services](media-services-dotnet-check-job-progress-with-webhooks.md). Para obter mais exemplos, consulte [das funções do suporte de dados de serviços do Azure](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Substitua o conteúdo do ficheiro run.csx existente pelo código seguinte. Quando tiver terminado definir a função clique **guarde e execute**.

```
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

##<a name="test-your-function"></a>Testar a função

Para testar a sua função, tem de carregar um ficheiro MP4 para o **entrada** contentor da conta do storage que especificou na cadeia de ligação.  

1. Selecione a conta de armazenamento que especificou no **StorageConnection** variável de ambiente.
2. Clique em **Blobs**.
3. Clique em **+ contentor**. Nome do contentor **entrada**.
4. Prima **carregar** e navegue para um ficheiro mp4 que pretende carregar.

>[!NOTE]
> Quando estiver a utilizar um acionador de blob um plano de consumo, podem existir até um atraso de 10 minutos processar novos blobs, depois de uma aplicação de função tornou-se inativo. Depois da aplicação de função está em execução, blobs são processados imediatamente. Para obter mais informações, consulte [do Blob storage acionadores e enlaces](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob#blob-storage-triggers-and-bindings).

## <a name="next-steps"></a>Passos seguintes

Neste momento, está pronto para começar a desenvolver uma aplicação dos Media Services. 
 
Para obter mais detalhes e exemplos/soluções completas de utilização das funções do Azure e Logic Apps com Media Services do Azure para criar fluxos de trabalho de criação de conteúdos personalizados, consulte o [suporte de dados de serviços .NET funções integração exemplo no GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Além disso, consulte [utilização do Azure WebHooks para monitorizar as notificações de tarefa de Media Services com .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

