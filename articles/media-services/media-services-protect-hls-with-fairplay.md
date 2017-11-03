---
title: "Proteger HLS conteúdo com o Microsoft PlayReady ou Apple do FairPlay - Azure | Microsoft Docs"
description: "Este tópico fornece uma descrição geral e mostra como utilizar Media Services do Azure para encriptar de forma dinâmica o conteúdo de HTTP Live Streaming (HLS) com o do FairPlay da Apple. Também mostra como utilizar o serviço de entrega de licença de Media Services para fornecer licenças do FairPlay aos clientes."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 895d6307b1cef74e195cc2ffd8dbef4196e97b1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Proteger a sua HLS conteúdo com do FairPlay da Apple ou Microsoft PlayReady
Media Services do Azure permite-lhe encriptar de forma dinâmica o conteúdo de HTTP Live Streaming (HLS), utilizando os seguintes formatos:  

* **Chave não encriptada envelope AES-128**

    O segmento completo é encriptado utilizando o **AES-128 CBC** modo. A desencriptação da transmissão em fluxo é suportada pelo iOS e leitor dos X nativamente. Para obter mais informações, consulte [encriptação dinâmica de utilizar AES-128 e o serviço de entrega de chave](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Os exemplos de áudio e vídeos individuais são encriptados utilizando a **AES-128 CBC** modo. **Transmissão em fluxo do FairPlay** (FPS) está integrado em sistemas de operativos de dispositivos, com suporte nativo em dispositivos iOS e Apple TV. Safari nos X permite FPS utilizando o suporte de interface de extensões de suporte de dados encriptados (EME).
* **Microsoft PlayReady**

A imagem seguinte mostra o **HLS + do FairPlay ou PlayReady encriptação dinâmica** fluxo de trabalho.

![Diagrama de fluxo de trabalho de encriptação dinâmica](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Este tópico demonstra como utilizar os Media Services para encriptar de forma dinâmica o conteúdo HLS do FairPlay da Apple. Também mostra como utilizar o serviço de entrega de licença de Media Services para fornecer licenças do FairPlay aos clientes.

> [!NOTE]
> Se pretender também encriptar o seu conteúdo HLS com PlayReady, terá de criar uma chave de conteúdo comuns e associe-o seu elemento. Terá também de configurar a política de autorização da chave de conteúdo, conforme descrito em [PlayReady utilizando a encriptação comum dinâmica](media-services-protect-with-drm.md).
>
>

## <a name="requirements-and-considerations"></a>Requisitos e considerações

É necessário o seguinte ao utilizar os Media Services para fornecer HLS encriptado com do FairPlay e para fornecer licenças do FairPlay:

  * Uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Uma conta dos Media Services. Para criar um, consulte [criar uma conta de Media Services do Azure no portal do Azure](media-services-portal-create-account.md).
  * Inscrever-se com [Apple Development programa](https://developer.apple.com/).
  * A Apple requer o proprietário do conteúdo obter o [pacote de implementação](https://developer.apple.com/contact/fps/). Estado que já implementada módulo de segurança da chave (KSM) com os Media Services e que está a pedir o pacote FPS final. Existem instruções do pacote FPS final gerar certificação e obter a chave de segredo de aplicação (peça ao). Utilize peça ao configurar do FairPlay.
  * Versão do SDK .NET dos Media Services do Azure **3.6.0** ou posterior.

Os seguintes procedimentos tem de ser definidos no lado de entrega de chave de Media Services:

  * **Aplicação certificação (AC)**: Este é um ficheiro. pfx que contém a chave privada. Pode criar este ficheiro e encriptar com uma palavra-passe.

       Quando configura uma política de entrega de chave, tem de fornecer essa palavra-passe e o ficheiro. pfx em formato Base64.

      Os passos seguintes descrevem como gerar um ficheiro de certificado. pfx do FairPlay:

    1. Instale o OpenSSL https://slproweb.com/products/Win32OpenSSL.html.

        Ir para a pasta onde estão o certificado do FairPlay e outros ficheiros fornecidos pela Apple.
    2. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro. cer para um ficheiro. pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509-informar der-no fairplay.cer-out do fairplay out.pem
    3. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro. pem para um ficheiro. pfx com a chave privada. A palavra-passe para o ficheiro. pfx, em seguida, é pedida pelo OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-exportar - out do fairplay out.pfx-inkey privatekey.pem-do fairplay out.pem - passin file:privatekey-pem-pass.txt
  * **Palavra-passe de aplicação Cert**: A palavra-passe para criar o ficheiro. pfx.
  * **ID de palavra-passe de aplicação Cert**: tem de carregar a palavra-passe, semelhante à forma como carregam outras chaves dos Media Services. Utilize o **ContentKeyType.FairPlayPfxPassword** valor de enumeração para obter o ID de serviços de suporte de dados Este é o que precisam de utilizar dentro da opção de política de entrega de chave.
  * **IV**: Este é um valor aleatório de 16 bytes. Tem de corresponder a iv na política de entrega de elemento. Gerar o iv e colocá-la em ambos os locais: a política de entrega de elemento e a opção de política de entrega de chave.
  * **PEÇA**: esta chave é recebida ao gerar a certificação através do portal de programador da Apple. Cada equipa de desenvolvimento irá receber uma peça ao exclusiva. Guarde uma cópia da peça ao e armazene-o num local seguro. Terá de configurar a peça ao como FairPlayAsk aos Media Services mais tarde.
  * **PEDIR ID**: este ID é obtido quando carregar peça para os serviços de suporte de dados. Tem de carregar peça ao utilizando o **ContentKeyType.FairPlayAsk** valor de enumeração. Como resultado, o ID de serviços de suporte de dados é devolvido e, este é o que deve ser utilizado ao definir a opção de política de entrega de chave.

Os seguintes procedimentos tem de ser definidos ao lado do cliente FPS:

  * **Aplicação certificação (AC)**: Este é um ficheiro de.cer/.der que contém a chave pública, que utiliza o sistema operativo para encriptar algumas payload. Tem de conhecer porque é necessário pelo leitor de Media Services. O serviço de entrega de chave desencripta-la utilizando a chave privada correspondente.

Reproduzir uma sequência de encriptado do FairPlay, obter um peça real ao primeiro e, em seguida, gere um certificado real. Este processo cria todos os três partes:

  * ficheiro. der
  * ficheiro. pfx
  * palavra-passe para o PFX

Os seguintes clientes suportam HLS com **AES-128 CBC** encriptação: Safari nos X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Configurar do FairPlay dinâmicos encriptação e a licença serviços de entrega
Seguem-se passos gerais para proteger os seus elementos do FairPlay utilizando o serviço de entrega de licença de Media Services bem como através de encriptação dinâmica.

1. Criar um elemento e carregar ficheiros para o elemento.
2. Codificar o elemento que contém o ficheiro para o definir MP4 de velocidade de transmissão adaptável.
3. Crie uma chave de conteúdo e associe-a com elemento codificado.  
4. Configure a política de autorização da chave de conteúdo. Especifique o seguinte:

   * O método de entrega (neste caso, do FairPlay).
   * Configuração de opções de política do FairPlay. Para obter mais informações sobre como configurar do FairPlay, consulte o **ConfigureFairPlayPolicyOptions()** método no exemplo abaixo.

     > [!NOTE]
     > Normalmente, iria querer configurar opções de política do FairPlay apenas uma vez, porque terá apenas um conjunto de uma certificação e uma peça ao.
     >
     >
   * Restrições (abertas ou tokens).
   * Informações específicas para o tipo de entrega de chave que define a forma como a chave é entregue ao cliente.
5. Configure a política de entrega de elemento. A configuração de política de entrega inclui:

   * O protocolo de entrega (HLS).
   * O tipo de encriptação dinâmica (encriptação comum de CBC).
   * O URL de aquisição de licença.

     > [!NOTE]
     > Se pretender fornecer um fluxo que é encriptado com do FairPlay e outro sistema de gestão de direitos digitais (DRM), tem de configurar políticas de entrega separado:
     >
     > * Um IAssetDeliveryPolicy configurar dinâmica transmissão em fluxo adaptável através de HTTP (DASH) com comuns encriptação (CENC) (PlayReady + Widevine) e uniforme com PlayReady
     > * Outro IAssetDeliveryPolicy configurar do FairPlay para HLS
     >
     >
6. Crie um localizador OnDemand para obter um URL de transmissão em fluxo.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Utilize a entrega de chave do FairPlay pelas aplicações de leitor
Pode desenvolver aplicações do leitor, utilizando o SDK do iOS. Para conseguir reproduzir o conteúdo do FairPlay, tem de implementar o protocolo do exchange de licença. Este protocolo não é especificado pela Apple. É como enviar pedidos de entrega de chave até cada aplicação. O serviço de entrega de chave do FairPlay de serviços de suporte de dados espera SPC fique como uma mensagem de post codificado www-form-url, o seguinte formato:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player não suporta a reprodução do FairPlay a box. Para obter a reprodução do FairPlay no MAC OS X, obtenha o leitor de exemplo da conta de programador da Apple.
>
>

## <a name="streaming-urls"></a>URLs de transmissão em fluxo
Se o seu elemento foi encriptado com mais do que um DRM, deve utilizar uma etiqueta de encriptação no URL de transmissão em fluxo: (formato = 'm3u8-aapl' encriptação = 'xxx').

Aplicam as seguintes considerações:

* Apenas zero ou um tipo de encriptação pode ser especificado.
* O tipo de encriptação não tem de ser especificado no URL, se apenas uma encriptação foi aplicada ao elemento.
* O tipo de encriptação é sensível a maiúsculas e minúsculas.
* Podem ser especificados os seguintes tipos de encriptação:  
  * **cenc**: encriptação comum (PlayReady ou Widevine)
  * **cbcs-aapl**: FairPlay
  * **CBC**: encriptação de envelope AES

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 
2. Adicione os elementos seguintes a **appSettings** definidos no ficheiro app.config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

## <a name="example"></a>Exemplo

O exemplo seguinte demonstra a capacidade de utilizar os Media Services para entregar o conteúdo encriptado com do FairPlay. Esta funcionalidade foi introduzida no SDK de Media Services do Azure para .NET versão 3.6.0. 

Substitua o código no seu ficheiro Program.cs com o código mostrado nesta secção.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

Certifique-se de que atualiza as variáveis para apontar para as pastas onde se encontram os seus ficheiros de entrada.

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Threading;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
    using Newtonsoft.Json;
    using System.Security.Cryptography.X509Certificates;

    namespace DynamicEncryptionWithFairPlay
    {
        class Program
        {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
            Console.WriteLine();

            if (tokenRestriction)
            tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
            AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
            // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
            // back into a TokenRestrictionTemplate class instance.
            TokenRestrictionTemplate tokenTemplate =
                TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

            // Generate a test token based on the the data in the given TokenRestrictionTemplate.
            // Note, you need to pass the key id Guid because we specified
            // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
            Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
            string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                        DateTime.UtcNow.AddDays(365));
            Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
            Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
            Console.WriteLine("File does not exist.");
            return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Token Authorization Policy",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                        Requirements = tokenTemplateString,
                    }
                    };

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }


        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
            rng.GetBytes(returnValue);
            }

            return returnValue;
        }
        }
    }


## <a name="next-steps-media-services-learning-paths"></a>Passos seguintes: percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
