---
title: Carregar ficheiros para uma conta de Media Services utilizando o .NET | Microsoft Docs
description: "Saiba como obter o conteúdo do suporte de dados para os serviços de suporte de dados através da criação e carregar recursos."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: juliako
ms.openlocfilehash: ec8c1da633374ba684f6a0a895c542ee76ef73b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Carregar ficheiros para uma conta de Media Services utilizando o .NET
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 
> 

Nos Media Services, pode carregar (ou inserir) os seus ficheiros digitais num elemento. O **Asset** entidade pode conter vídeo, áudio, imagens, coleções de miniaturas, texto controla e legendas ficheiros (e os metadados sobre estes ficheiros.)  Assim que os ficheiros são carregados, o seu conteúdo é armazenado em segurança na nuvem para processamento adicional e a transmissão em fluxo.

Os ficheiros no elemento são denominados **Ficheiros de Elemento**. O **AssetFile** instância e o ficheiro de multimédia real são dois objetos distintos. A instância de AssetFile contém metadados sobre o ficheiro de suporte de dados, enquanto o ficheiro de suporte de dados contém o conteúdo de multimédia real.

> [!NOTE]
> Aplicam as seguintes considerações:
> 
> * Os Media Services utiliza o valor da propriedade IAssetFile.Name ao criar os URLs para os conteúdos de transmissão em fluxo (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, por cento de codificação não é permitida. O valor da **nome** propriedade não pode ter qualquer um dos seguintes [por cento codificação-reservados carateres](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Além disso, só pode existir um '.' para a extensão de nome de ficheiro.
> * O comprimento do nome não deve ser superior a 260 carateres.
> * Existe um limite para o tamanho máximo dos ficheiros suportado para processamento nos Serviços de Multimédia. Veja [este](media-services-quotas-and-limitations.md) tópico para obter detalhes sobre as limitações relativas aos tamanhos de ficheiros.
> * Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.
> 

Ao criar recursos, pode especificar as seguintes opções de encriptação. 

* **Nenhum** - Não é utilizada qualquer encriptação. Este é o valor predefinido. Tenha em atenção que ao utilizar esta opção o conteúdo não está protegido em trânsito ou inativo no armazenamento.
  Se planear distribuir um MP4 utilizando uma transferência progressiva, utilize esta opção. 
* **CommonEncryption** -Utilize esta opção se estiver a carregar conteúdo que já foi encriptado e protegido com encriptação comum ou PlayReady DRM (por exemplo, transmissão em fluxo uniforme protegida com PlayReady DRM).
* **EnvelopeEncrypted** – Utilize esta opção se estiver a carregar HLS encriptado com AES. Tenha em atenção que os ficheiros têm de ser codificados e encriptados pelo Gestor de Transformação.
* **StorageEncrypted** - encripta o seu conteúdo transparente localmente utilizando AES 256 bits encriptação e, em seguida, esta carrega-o ao Storage do Azure onde é armazenado encriptados em pausa. Os elementos protegidos com Encriptação do Storage são desencriptados automaticamente e colocados num sistema de ficheiros encriptados antes da codificação, sendo opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal motivo para a encriptação de armazenamento é quando pretender proteger os ficheiros de suporte de dados de entrada de alta qualidade com uma encriptação forte Inativos no disco.
  
    Os Media Services fornecem encriptação de armazenamento no disco para os recursos, não a ativação pós-falha a transmissão, como o Gestor de direitos digitais (DRM).
  
    Se o seu elemento armazenamento encriptado, tem de configurar política de entrega de elementos. Para obter mais informações consulte [configurar política de entrega de elemento](media-services-dotnet-configure-asset-delivery-policy.md).

Se especificar para o seu elemento seja encriptado com uma **CommonEncrypted** opção, ou um **EnvelopeEncypted** opção, terá de associar o seu elemento com um **ContentKey**. Para obter mais informações, consulte [como criar um ContentKey](media-services-dotnet-create-contentkey.md). 

Se especificar para o seu elemento seja encriptado com uma **StorageEncrypted** opção, o SDK de Media Services para .NET irá criar um **StorateEncrypted** **ContentKey** para o recurso.

Este tópico mostra como utilizar o SDK .NET dos Media Services, bem como as extensões do SDK .NET dos Media Services para carregar ficheiros para um recurso dos Media Services.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Carregar um único ficheiro com o SDK .NET dos Media Services
O código de exemplo abaixo utiliza o .NET SDK para carregar um único ficheiro. O localizador e AccessPolicy são criadas e destruído pela função de carregamento. 


        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Carregar vários ficheiros com o SDK .NET dos Media Services
O código seguinte mostra como criar um elemento e carregar vários ficheiros.

O código faz o seguinte:

* Cria um elemento vazio utilizando o método de CreateEmptyAsset definido no passo anterior.
* Cria um **AccessPolicy** instância que define a duração de acesso e permissões para o elemento.
* Cria um **localizador** instância que fornece acesso ao elemento.
* Cria um **BlobTransferClient** instância. Este tipo representa um cliente que opera em blobs do Azure. Neste exemplo é utilizar o cliente para monitorizar o progresso do carregamento. 
* Enumera através de ficheiros no diretório especificado e cria um **AssetFile** instância para cada ficheiro.
* Carrega os ficheiros para os Media Services utilizando o **UploadAsync** método. 

> [!NOTE]
> Utilize o método de UploadAsync para garantir que não estão a bloquear as chamadas e os ficheiros são carregados em paralelo.
> 
> 

        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }



Quando carregar um grande número de ativos, considere o seguinte.

* Crie um novo **CloudMediaContext** objeto por thread. O **CloudMediaContext** classe não é seguros para threads.
* Aumente NumberOfConcurrentTransfers do valor predefinido de 2 para um valor superior, como 5. Definir esta propriedade afeta todas as instâncias de **CloudMediaContext**. 
* Manter ParallelTransferThreadCount no valor predefinido de 10.

## <a id="ingest_in_bulk"></a>Inserção dos elementos em massa utilizando o SDK .NET dos Media Services
Carregar ficheiros grandes recurso pode ser um estrangulamento durante a criação do recurso. Inserção dos elementos em massa ou "Em massa ingestão relacionadas", envolve desassociar a criação de recurso do processo de carregamento. Para utilizar um volume ingestão relacionadas abordagem, crie um manifesto (IngestManifest) que descrevem o elemento e os ficheiros associados. Em seguida, utilize o método de carregamento à sua escolha para carregar os ficheiros associados para o contentor de blob do manifesto. Serviços de suporte de dados do Microsoft Azure controla o contentor de blob associado o manifesto. Depois de um ficheiro é carregado para o contentor de blob, Media Services do Microsoft Azure conclui a criação de recursos com base na configuração do elemento no manifesto (IngestManifestAsset).

Para criar uma nova chamada de IngestManifest o método Create exposto pela coleção IngestManifests no CloudMediaContext. Este método irá criar um novo IngestManifest com o nome do manifesto que fornecer.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Crie os elementos que serão associados IngestManifest em volume. Configure as opções de encriptação desejado no elemento para ingestão relacionadas em massa.

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Um IngestManifestAsset associa um recurso em massa IngestManifest para ingestão relacionadas em massa. Também associa o AssetFiles que constituirá cada recurso. Para criar um IngestManifestAsset, utilize o método Create no contexto de servidor.

O exemplo seguinte demonstra o manifesto de inserção de adição dois IngestManifestAssets novos que associam os dois recursos que criou anteriormente para o volume. Cada IngestManifestAsset também associa um conjunto de ficheiros que irá ser carregado para cada recurso durante ingestão relacionadas em massa.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });

Pode utilizar qualquer aplicação de cliente de alta velocidade com capacidade de carregar os ficheiros de recurso para o contentor de blob storage URI fornecido pelo **IIngestManifest.BlobStorageUriForUpload** propriedade o IngestManifest. É um serviço de carregamento de alta velocidade acentuadas [Aspera a pedido para a aplicação Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). Também pode escrever código para carregar os ficheiros de recursos, conforme mostrado no exemplo de código seguinte.

    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }

O código para carregar os ficheiros de recurso para o exemplo utilizado neste tópico é mostrado no exemplo de código seguinte.

    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);


Pode determinar o progresso da ingestão relacionadas em massa para todos os recursos associados a um **IngestManifest** através de consultas de propriedade de estatísticas do **IngestManifest**. Para atualizar as informações de progresso, tem de utilizar um novo **CloudMediaContext** sempre que poderá consulta a propriedade de estatísticas.

O exemplo seguinte demonstra a consulta um IngestManifest pelo respetivo **Id**.

    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }



## <a name="upload-files-using-net-sdk-extensions"></a>Carregar ficheiros através de extensões do SDK do .NET
O exemplo abaixo mostra como carregar um único ficheiro através de extensões do SDK do .NET. Neste caso, o **CreateFromFile** método é utilizado, mas também está disponível a versão assíncrona (**CreateFromFileAsync**). O **CreateFromFile** método permite-lhe especificar o nome de ficheiro, a opção de encriptação e uma chamada de retorno para comunicar o progresso do carregamento do ficheiro.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }

O exemplo seguinte chamadas de função UploadFile e especifica a encriptação de armazenamento como a opção de criação do recurso.  

    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);

## <a name="next-steps"></a>Passos seguintes

Agora, pode codificar os elementos que carregar. Para obter mais informações, veja [Codificar elementos](media-services-portal-encode.md)

Também pode utilizar as Funções do Azure para acionar uma tarefa de codificação num ficheiro que esteja a chegar ao contentor configurado. Para obter mais informações, veja [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passo seguinte
Agora que tenha carregado um recurso aos Media Services, vá para o [como obter um processador de multimédia] [ How to Get a Media Processor] tópico.

[How to Get a Media Processor]: media-services-get-media-processor.md

