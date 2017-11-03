---
title: Gerir recursos e entidades relacionadas com os Media Services .NET SDK
description: Saiba como gerir recursos e entidades relacionadas com o SDK de Media Services para .NET.
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: 5efe16a09808267d0797521f9e1df2b60aec9cbb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Gerir recursos e entidades relacionadas com os Media Services .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

Este tópico mostra como gerir entidades de Media Services do Azure com o .NET. 

>[!NOTE]
> A partir de 1 de abril de 2017, qualquer Registo de tarefa na sua conta com mais de 90 dias será eliminado automaticamente, juntamente com os seus registos de Tarefas associados, mesmo se o número total de registos for inferior à quota máxima. Por exemplo, no dia 1 de Abril de 2017, qualquer registo de tarefas na sua conta com mais de 31 de Dezembro de 2016, será automaticamente eliminado. Se precisar de arquivar as informações de tarefa de tarefas, pode utilizar o código descrito neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

## <a name="get-an-asset-reference"></a>Obter uma referência de elemento
Uma tarefa frequente é obter uma referência a um recurso existente nos Media Services. O exemplo de código seguinte mostra como pode obter uma referência de elemento da coleção de recursos no servidor do objeto de contexto, com base num recurso de ID. Exemplo de código seguinte utiliza uma consulta de Linq para obter uma referência a um objeto de IAsset existente.

    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }

## <a name="list-all-assets"></a>Listar todos os elementos
À medida que cresce o número de ativos que tem no armazenamento, é útil listar os seus recursos. Exemplo de código seguinte mostra como itere através da coleção de recursos no objeto de contexto de servidor. Com cada recurso, o exemplo de código também escreve alguns dos seus valores de propriedade para a consola. Por exemplo, cada recurso pode conter muitos ficheiros de suporte de dados. O exemplo de código escreve todos os ficheiros associados a cada recurso.

    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }

## <a name="get-a-job-reference"></a>Obter uma referência de tarefa

Quando trabalha com processamento de tarefas no código de Media Services, muitas vezes, terá de obter uma referência a uma tarefa existente com base num ID. Exemplo de código seguinte mostra como obter uma referência a um objeto de IJob da coleção de tarefas.

Poderá ter de obter uma referência de tarefa ao iniciar uma tarefa de codificação de longa execução e tem de verificar o estado da tarefa num thread. Em casos como esta, quando o método devolve a partir de um thread, terá de obter uma referência a um trabalho atualizada.

    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }

## <a name="list-jobs-and-assets"></a>Lista de tarefas e de recursos
Uma tarefa relacionada importante é a recursos de lista com o trabalho associado nos serviços de suporte de dados. Exemplo de código seguinte mostra como listar cada objeto IJob e, em seguida, para cada tarefa, apresenta as propriedades da tarefa, todos os relacionados com tarefas, todos os recursos e todos os recursos de saída de entrada. O código neste exemplo pode ser útil para várias outras tarefas. Por exemplo, se pretende listar os ativos de saída de uma ou mais tarefas de codificação que executou anteriormente, este código mostra como pode aceder os recursos de saída. Quando tiver uma referência a um elemento de saída, em seguida, pode distribuir os conteúdos para outros utilizadores ou aplicações, transferindo-a ou fornecer URLs. 

Para obter mais informações sobre as opções de entrega de recursos, consulte [fornecer recursos com o SDK de Media Services para .NET](media-services-deliver-streaming-content.md).

    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }

## <a name="list-all-access-policies"></a>Lista todas as políticas de acesso
Nos Media Services, pode definir uma política de acesso de um recurso ou os respetivos ficheiros. Uma política de acesso define as permissões para um ficheiro ou de um recurso (o tipo de acesso e a duração). No seu código de Media Services, normalmente, definir uma política de acesso através da criação de um objeto de IAccessPolicy e, em seguida, associar a um recurso existente. Em seguida, criar um objeto de ILocator, que lhe permite fornecer acesso direto a recursos nos serviços de suporte de dados. O projeto do Visual Studio que acompanha esta série de documentação contém vários exemplos de código mostram como criar e atribuir políticas de acesso e localizadores aos recursos.

Exemplo de código seguinte mostra como listar todas as políticas de acesso no servidor e mostra o tipo de permissões associadas a cada um. Outra forma útil para ver as políticas de acesso listar todos os objetos de ILocator no servidor e, em seguida, para cada localização, pode listar a política de acesso associados, utilizando a respetiva propriedade AccessPolicy.

    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
    
## <a name="limit-access-policies"></a>Políticas de acesso de limite 

>[!NOTE]
> Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). 

Por exemplo, pode criar um conjunto de políticas genérico com o seguinte código que executaria apenas uma vez na sua aplicação. Pode iniciar os IDs de um ficheiro de registo para utilização posterior:

    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);

Em seguida, pode utilizar os IDs existentes no seu código como esta:

    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());

## <a name="list-all-locators"></a>Listar todos os localizadores
Um localizador é um URL que fornece um caminho direto para aceder a um recurso, juntamente com permissões para o elemento, tal como definido pela política de acesso associados o localizador. Cada elemento pode ter uma coleção de objetos de ILocator associada a respetiva propriedade localizadores. O contexto de servidor também tem uma coleção de localizadores que contém todos os localizadores.

Exemplo de código seguinte apresenta uma lista de todos os localizadores no servidor. Para cada localização, mostra o Id para a política de acesso e de recursos relacionado. Também apresenta o tipo de permissões, a data de expiração e o caminho completo para o elemento.

Tenha em atenção que um caminho de localizador para um recurso é apenas um URL de base para o elemento. Para criar um caminho direto para ficheiros individuais que um utilizador ou aplicação foi procurar, o código tem de adicionar o caminho de ficheiro específico para o caminho de localizador. Para obter mais informações sobre como fazê-lo, consulte o tópico [fornecer recursos com o SDK de Media Services para .NET](media-services-deliver-streaming-content.md).

    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerar através de coleções de grandes dimensões de entidades
Ao consultar entidades, não há um limite de 1000 entidades devolvido de uma só vez porque pública REST v2 limita os resultados da consulta para resultados de 1000. Tem de utilizar ignorar e tomar ao enumerar através de coleções de grandes dimensões de entidades. 

A seguinte função repetido ao longo de todas as tarefas na conta de serviços de suporte de dados fornecido. Os Media Services devolve 1000 tarefas na coleção de tarefas. A função faz com que a utilização de ignorar e tomar para se certificar de que todas as tarefas são enumerada (no caso de ter mais de 1000 tarefas na sua conta).

    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

## <a name="delete-an-asset"></a>Eliminar um recurso
O exemplo seguinte elimina um recurso.

    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }

## <a name="delete-a-job"></a>Eliminar uma tarefa
Para eliminar uma tarefa, tem de verificar o estado da tarefa como indicado na propriedade de estado. As tarefas são concluídas ou canceladas que podem ser eliminadas, enquanto as tarefas que estão em determinados Estados como em fila, agendada ou processamento, tem de ser canceladas primeiro e, em seguida, pode ser eliminados.

Exemplo de código seguinte mostra um método para eliminar uma tarefa por a verificação de Estados de tarefa e, em seguida, eliminar quando o estado é concluído ou cancelado. Este código depende da secção anterior deste tópico para obter uma referência a uma tarefa: obter uma referência de tarefa.

    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }


## <a name="delete-an-access-policy"></a>Eliminar uma política de acesso
O exemplo de código seguinte mostra como obter uma referência a uma política de acesso com base numa política Id e, em seguida, ao eliminar a política.

    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }



## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

