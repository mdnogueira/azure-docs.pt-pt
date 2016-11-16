---
title: "Introdução à entrega de conteúdo a pedido com o .NET | Microsoft Docs"
description: "Este tutorial vai ajudá-lo a implementar uma aplicação de distribuição de conteúdos a pedido com os Media Services do Azure utilizando o .NET."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/17/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 80606d9fd08a4d5b5845af8ed43fdcef050e47e9


---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introdução à distribuição de conteúdos a pedido utilizando o SDK do .NET
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
> 
> 

## <a name="overview"></a>Descrição geral
Este tutorial vai ajudá-lo a implementar uma aplicação de distribuição de conteúdos de Vídeo a Pedido (VoD) utilizando o SDK do .NET dos Media Services do Azure (AMS).

O tutorial apresenta o fluxo de trabalho básico dos Media Services, os objetos de programação mais comuns e as tarefas necessárias para o desenvolvimento dos Media Services. No final do tutorial, será capaz de transmitir ou transferir progressivamente um ficheiro de multimédia de exemplo que carregou, codificou e transferiu.

## <a name="what-youll-learn"></a>O que irá aprender
O tutorial mostra como realizar as seguintes tarefas:

1. Criar uma conta dos Serviços de Multimédia (com o Portal do Azure).
2. Configurar o ponto final de transmissão em fluxo (com o Portal do Azure).
3. Criar e configurar um projeto de Visual Studio.
4. Ligar à conta de Media Services.
5. Criar um novo elemento e carregar um ficheiro de vídeo.
6. Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.
7. Publicar o elemento e obter os URLs para transmissão em fluxo e transferência progressiva.
8. Testar com a reprodução do seu conteúdo.

## <a name="prerequisites"></a>Pré-requisitos
O seguinte é necessário para concluir o tutorial.

* Para concluir este tutorial, precisa de uma conta do Azure. 
  
    Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Recebe créditos que podem ser utilizados para experimentar os serviços pagos do Azure. Mesmo depois de gastar todos os créditos, pode manter a conta e utilizar os serviços e recursos gratuitos do Azure, tal como a funcionalidade Web Apps no App Service do Azure.
* Sistemas operativos: Windows 8 ou posterior, Windows 2008 R2, Windows 7.
* .NET framework 4.0 ou posterior
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou versões posteriores.

## <a name="download-sample"></a>Transferir exemplo
Obtenha e execute um exemplo [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Criar uma conta dos Serviços de Multimédia do Azure com o Portal do Azure
Os passos nesta secção explicam como criar uma conta dos AMS.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **+Novo** > **Multimédia + CDN** > **Serviços de Multimédia**.
   
    ![Criar Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. Em **CRIAR CONTA DE MEDIA SERVICES**, Introduza os valores necessários.
   
    ![Criar Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. Em **Nome da Conta**, introduza o nome da nova conta de AMS. Um nome de conta dos Media Services é composto por números ou letras minúsculas sem espaços, com 3 a 24 carateres de comprimento.
   2. Na subscrição, selecione entre as diferentes subscrições do Azure disponíveis para si.
   3. Em **Grupo de Recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é uma coleção de recursos que partilham o ciclo de vida, as permissões e as políticas. Sabia mais [aqui](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. Em **Localização**, selecione a região geográfica que é utilizada para armazenar os registos de multimédia e metadados da conta dos Media Services. Esta região é utilizada para processar e transmitir em fluxo os seus conteúdos multimédia. Apenas as regiões dos Media Services disponíveis são apresentadas na caixa de lista pendente. 
   5. Em **Conta do Storage**, selecione uma conta do Storage para fornecer o Blob Storage do conteúdo de multimédia da conta de Media Services. Pode selecionar uma conta de armazenamento existente na mesma região geográfica da conta dos Serviços de Multimédia ou pode criar uma conta de armazenamento. É criada uma nova conta do Storage na mesma região. As regras para os nomes da conta do Storage são iguais às das contas dos Media Services.
      
       Saiba mais sobre armazenamento [aqui](../storage/storage-introduction.md).
   6. Selecione **Afixar no dashboard** para ver o progresso da implementação da conta.
4. Clique em **Criar** na parte inferior do formulário.
   
    Após criar a conta com êxito, o estado é alterado para **Em Execução**. 
   
    ![Definições dos Media Services](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Para gerir a sua conta de AMS (por exemplo, carregar vídeos, codificar elementos, monitorizar o progresso da tarefa) utilize a janela **Definições**.

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurar os pontos finais de transmissão em fluxo com o Portal do Azure
Ao trabalhar com os Azure Media Services, uma das situações mais comuns é a entrega de vídeo através de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Os Media Services suportam as seguintes tecnologias de transmissão em fluxo de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), transmissão em Fluxo Uniforme, MPEG DASH e HDS (para detentores de licença do Adobe PrimeTime/acesso apenas).

Os Media Services fornecem um empacotamento dinâmico, permitindo a entrega dos seus conteúdos codificados em MP4 de velocidade de transmissão adaptável em formatos de transmissão em fluxo suportados pelos Media Services (MPEG DASH, HLS, Transmissão em Fluxo Uniforme, HDS) just-in-time, sem ter de voltar a armazenar versões pré-empacotadas de cada um destes formatos de transmissão em fluxo.

Para tirar partido do empacotamento dinâmico, tem de fazer o seguinte:

* Codificar o ficheiro (origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável (os passos da codificação são demonstrados mais à frente neste tutorial).  
* Criar, pelo menos, uma unidade de transmissão em fluxo para o *ponto final de transmissão em fluxo* a partir do qual planeia entregar o conteúdo. Os passos abaixo mostram como alterar o número de unidades de transmissão em fluxo.

Com o empacotamento dinâmico, só tem de armazenar e pagar os ficheiros num único formato de armazenamento, e os Media Services compilam e disponibilizam a resposta adequada com base nos pedidos de um cliente.

Para criar e alterar o número de unidades reservadas para transmissão em fluxo, faça o seguinte:

1. Na janela **Definições**, clique em **Pontos finais de transmissão em fluxo**. 
2. Clique no ponto final de transmissão em fluxo predefinido. 
   
    A janela **DETALHES DO PONTO FINAL DE TRANSMISSÃO EM FLUXO PREDEFINIDO** é apresentada.
3. Para especificar o número de unidades de transmissão em fluxo, deslize o controlo de deslize **Unidades de transmissão em fluxo**.
   
    ![Unidades de transmissão em fluxo](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Clique no botão **Guardar** para guardar as alterações.
   
   > [!NOTE]
   > A alocação de quaisquer novas unidades pode demorar até 20 minutos a concluir.
   > 
   > 

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio
1. Crie uma nova Aplicação de Consola C# no Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1. Introduza o **Nome**, **Localização** e **Nome da solução** e, em seguida, clique em **OK**.
2. Utilize o pacote NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar **Extensões do SDK do .NET dos Serviços de Multimédia do Azure**.  As Extensões do SDK do .NET dos Media Services são um conjunto de métodos de extensão e funções de programa auxiliar que irão simplificar o seu código e facilitar o desenvolvimento com os Media Services. Ao instalar este pacote, também é instalado o **SDK do .NET dos Media Services** e são adicionadas todas as outras dependências necessárias.
3. Adicione uma referência à assemblagem System.Configuration. Esta assemblagem contém a classe **System.Configuration.ConfigurationManager** que é utilizada para aceder aos ficheiros de configuração, por exemplo, App.config.
4. Abra o ficheiro App.config (adicione o ficheiro ao seu projeto caso não tenha sido adicionado por predefinição) e adicione uma secção *appSettings* ao ficheiro. Configure os valores do nome de conta e a chave de conta da sua conta de Media Services do Azure, conforme mostrado no exemplo seguinte. Para obter o nome de conta e as informações da chave, aceda ao [portal do Azure](https://portal.azure.com/) e selecione a sua conta AMS. Em seguida, selecione **Definições** > **Chaves**. A janela Gerir chaves mostra o nome da conta e as chaves primária e secundária são apresentadas.
   
        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
   
        </configuration>
5. Substitua as declarações **using** existentes no início do ficheiro Program.cs pelo seguinte código.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
6. Crie uma nova pasta no diretório dos projetos e copie um ficheiro .mp4 ou .wmv que pretende codificar e transmitir ou transferir progressivamente. Neste exemplo, é utilizado o caminho "C:\VideoFiles".

## <a name="connect-to-the-media-services-account"></a>Ligar à conta de Media Services
Quando utilizar os Media Services com .NET, deve utilizar a classe **CloudMediaContext** na maioria das tarefas de programação dos Media Services: ligar à conta de Media Services; criar, atualizar, aceder e eliminar os seguintes objetos: elementos, ficheiros de elementos, tarefas, políticas de acesso, localizadores, etc.

Substitua a classe Program predefinida com o seguinte código. O código demonstra como ler os valores de ligação a partir do ficheiro App.config e como criar o objeto **CloudMediaContext** para se ligar aos Media Services. Para obter mais informações sobre como se ligar aos Media Services, consulte [Ligar aos Media Services com o SDK do .NET dos Media Services](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Os métodos de chamadas de função **Main** que serão definidos posteriormente nesta secção.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Criar um novo elemento e carregar um ficheiro de vídeo
Nos Media Services, pode carregar (ou inserir) os seus ficheiros digitais num elemento. A entidade **Elemento** pode conter ficheiros de vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e legendas (e os metadados relativos a esses ficheiros).  Assim que os ficheiros são carregados, o seu conteúdo é armazenado em segurança na nuvem para processamento adicional e a transmissão em fluxo. Os ficheiros no elemento são denominados **Ficheiros de Elemento**.

O método **UploadFile** definido abaixo chama **CreateFromFile** (definido nas Extensões do SDK do .NET). **CreateFromFile** cria um novo elemento no qual o ficheiro de origem especificado é carregado.

O método **CreateFromFile** recorre a **AssetCreationOptions** que permite especificar uma das seguintes opções de criação do elemento:

* **Nenhum** - Não é utilizada qualquer encriptação. Este é o valor predefinido. Tenha em atenção que, ao utilizar esta opção, o seu conteúdo não está protegido enquanto estiver em trânsito ou inativo no armazenamento.
  Se planear distribuir um MP4 utilizando uma transferência progressiva, utilize esta opção.
* **StorageEncrypted** - Utilize esta opção para encriptar o seu conteúdo transparente localmente utilizando a encriptação de 256 bit norma AES (Advanced Encryption Standard), posteriormente, esta carrega-o para o Storage do Azure onde é armazenado encriptados e permanece inativo. Os elementos protegidos com Encriptação do Storage são desencriptados automaticamente e colocados num sistema de ficheiros encriptados antes da codificação, sendo opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal caso de utilização da Encriptação do Storage ocorre quando pretende proteger os seus ficheiros de multimédia de entrada de alta qualidade inativos no disco com uma encriptação forte.
* **CommonEncryptionProtected** - Utilize esta opção se estiver a carregar conteúdo que já foi encriptado e protegido com Encriptação Comum ou PlayReady DRM (por exemplo, Transmissão em Fluxo Uniforme protegida com PlayReady DRM).
* **EnvelopeEncryptionProtected** – Utilize esta opção se estiver a carregar HLS encriptado com AES. Tenha em atenção que os ficheiros têm de ser codificados e encriptados pelo Gestor de Transformação.

O método **CreateFromFile** também permite especificar uma chamada de retorno para comunicar o progresso do carregamento do ficheiro.

No exemplo seguinte, especificamos **Nenhum** para as opções do elemento.

Adicione o seguinte método à classe Program.

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


## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificar o ficheiro de origem para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável
Após a inserção dos elementos nos Media Services, a multimédia pode ser codificada, formatada, receber uma marca de água digital e assim sucessivamente, antes de ser distribuída aos clientes. Estas atividades são agendadas e executadas em várias instâncias de função de segundo plano para assegurar um elevado desempenho e disponibilidade. Estas atividades são denominadas Tarefas, cada uma é composta por Tarefas atómicas que fazem o trabalho real no Ficheiro de elemento.

Tal como mencionado anteriormente, ao trabalhar com os Media Services do Azure, uma das situações mais comuns é a distribuição de transmissão em fluxo de velocidade de transmissão adaptável para os seus clientes. Os Media Services podem empacotar dinamicamente um conjunto de ficheiro MP4 de velocidade de transmissão adaptável num dos seguintes formatos: HTTP Live Streaming (HLS), transmissão em Fluxo Uniforme, MPEG DASH e HDS (para detentores de licença do Adobe PrimeTime/acesso apenas).

Para tirar partido do empacotamento dinâmico, tem de fazer o seguinte:

* Codificar ou transcodificar o ficheiro (origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de Transmissão em Fluxo Uniforme de velocidade de transmissão adaptável.  
* Obter, pelo menos, uma unidade de transmissão em fluxo para o ponto final de transmissão em fluxo a partir do qual planeia distribuir o conteúdo.

O código seguinte mostra como submeter uma tarefa de codificação. A tarefa contém uma tarefa que especifica a transcodificação do ficheiro de mezanino num conjunto de MP4s de velocidade de transmissão adaptável utilizando um **Codificador de Multimédia Standard**. O código submete a tarefa e aguarda até que esta esteja concluída.

Assim que a tarefa esteja concluída, será possível transmitir o seu elemento ou transferir progressivamente ficheiros MP4 que foram criados no seguimento da transcodificação
Tenha em atenção que não é necessário ter mais do que 0 unidades de transmissão em fluxo para transferir progressivamente os ficheiros MP4.

Adicione o seguinte método à classe Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicar o elemento e obter os URLs para transmissão em fluxo e transferência progressiva
Para transmitir ou transferir um elemento, primeiro tem de o "publicar" através da criação de um localizador. Os localizadores fornecem acesso aos ficheiros contidos no elemento. Os Media Services suportam dois tipos de localizadores: localizadores OnDemandOrigin, utilizados para transmitir multimédia (por exemplo, MPEG DASH, HLS ou Transmissão em Fluxo Uniforme) e localizadores de Assinatura de Acesso (SAS), utilizados para transferir ficheiros de multimédia.

Depois de criar os localizadores, pode criar os URLs que são utilizados para transmitir ou transferir os seus ficheiros.

Um URL de transmissão em fluxo para uma Transmissão em Fluxo Uniforme tem o seguinte formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Um URL de transmissão em fluxo para HLS tem o seguinte formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Um URL de transmissão em fluxo para MPEG DASH tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Um URL de SAS utilizado para transferir ficheiros tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

As extensões do SDK do .NET de Media Services fornecem métodos de programa auxiliar convenientes que devolvem URLs formatados para o elemento publicado.

O código seguinte utiliza Extensões do SDK do .NET para criar localizadores e obter URLs de transmissão em fluxo e transferência progressiva. O código também mostra como transferir ficheiros para uma pasta local.

Adicione o seguinte método à classe Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## <a name="test-by-playing-your-content"></a>Testar com a reprodução do seu conteúdo
Depois de executar o programa definido na secção anterior, os URLs semelhantes ao seguinte serão apresentados na janela da consola.

URLs de transmissão em fluxo adaptável:

Transmissão em Fluxo Uniforme

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URLs de transferência progressiva (áudio e vídeo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Para transmitir o seu vídeo, utilize o [Leitor dos Media Services do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar as transferências progressivas, cole um URL num browser (por exemplo, Internet Explorer, Chrome ou Safari).

## <a name="next-steps-media-services-learning-paths"></a>Passos Seguintes: percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Está à procura de outra coisa?
Se este tópico não continha o que pretendia, algo está em falta ou não correspondeu de alguma forma às suas necessidades, envie-nos os seus comentários através do thread Disqus abaixo.

<!-- Anchors. -->


<!-- URLs. -->
[Instalador de Plataforma Web]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/



<!--HONumber=Nov16_HO2-->


