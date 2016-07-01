<properties
    pageTitle="Utilizar Encriptação Comum PlayReady e/ou Widevine Dinâmico"
    description="Os Media Services do Microsoft Azure permitem entregar MPEG-DASH, Transmissão em Fluxo Uniforme e transmissões em fluxo Http-Live-Streaming (HLS) protegidas com o Microsoft PlayReady DRM. Também permite entregar DASH encriptado com Widevine DRM Este tópico mostra como encriptar de forma dinâmica com PlayReady e Widevine DRM."
    services="media-services"
    documentationCenter=""
    authors="Juliako,Mingfeiy"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/03/2016" 
    ms.author="juliako"/>


#Utilizar Encriptação Comum PlayReady e/ou Widevine Dinâmico

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Os Media Services do Microsoft Azure permitem entregar MPEG-DASH, Transmissão em Fluxo Uniforme e transmissões em fluxo HTTP-Live-Streaming (HLS) protegidas com o [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). Também permitem entregar transmissões em fluxo DASH encriptadas com licenças Widevine DRM. Tanto PlayReady como Widevine são encriptados de acordo com a especificação de Encriptação Comum (ISO/IEC 23001 7 CENC). Pode utilizar [SDK .NET do AMS](https://www.nuget.org/packages/windowsazure.mediaservices/) (começando com a versão 3.5.1) ou a API REST para configurar o seu AssetDeliveryConfiguration para utilizar Widevine.

Os Media Services fornecem um serviço para entrega de licenças PlayReady e Widevine DRM. Os Media Services também fornecem APIs que permitem configurar os direitos e as restrições que pretende para o tempo de execução de PlayReady ou Widevine DRM a impor quando um utilizador reproduz conteúdo protegido. Quando um utilizador solicita um conteúdo DRM protegido, a aplicação de leitor solicitará uma licença do serviço de licença do AMS. O serviço de licença do AMS irá emitir uma licença para o leitor se está autorizado. Uma licença PlayReady ou Widevine contém a chave de desencriptação que pode ser utilizada pelo leitor de cliente para desencriptar e transmitir em fluxo o conteúdo.


Também pode utilizar os seguintes parceiros de AMS para o ajudar a fornecer licenças Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/). Para obter mais informações, consulte: integração com [Axinom](media-services-axinom-integration.md) e [castLabs](media-services-castlabs-integration.md).

Os Media Services suportam várias formas de autorização de utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização: aberto ou token restrito. A política de token restrito tem de ser acompanhada por um token emitido por um Serviço de Token Seguro (STS). Os Media Services suportam tokens no formato [Web Tokens Simples](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [Web Token JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obter mais informações, consulte Configurar a política de autorização da chave de conteúdo.

Para tirar partido da encriptação dinâmica, tem de ter um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Transmissão em Fluxo Uniforme de múltipla transmissão. Terá também de configurar as políticas de entrega para o elemento (descrito mais adiante neste tópico). Em seguida, com base no formato especificado no URL de transmissão em fluxo, o servidor de Transmissão em Fluxo a Pedido irá garantir que a transmissão é entregue no protocolo que escolheu. Desta forma, o empacotamento dinâmico só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento, os Media Services irão compilar e disponibilizar a resposta HTTP adequada com base em cada pedido de um cliente.

Este tópico seria útil para os programadores que funcionam em aplicações que entregam multimédia protegida com vários DRMs, tais como PlayReady e Widevine. O tópico mostra como configurar o serviço de entrega de licença PlayReady com políticas de autorização para que apenas os clientes autorizados possam receber licenças PlayReady ou Widevine. Este também mostra como utilizar a encriptação dinâmica com PlayReady ou Widevine DRM sobre DASH.

>[AZURE.NOTE]Para começar a utilizar a encriptação dinâmica, tem primeiro de obter, pelo menos, uma unidade de escala (também conhecida como unidade de transmissão em fluxo). Para obter mais informações, consulte [Como escalar um Serviço de Multimédia](media-services-manage-origins.md#scale_streaming_endpoints).


##Transferir exemplo

Pode transferir a amostra descrita neste artigo [aqui](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

##Configurar a Encriptação Comum Dinâmica e os Serviços de Entrega de Licença DRM

A seguir são apresentados os passos gerais que precisa de realizar ao proteger os seus elementos com PlayReady, através do serviço de entrega de licença de Media Services e também da encriptação dinâmica.

1. Crie um elemento e carregue ficheiros no elemento. 
1. Codifique o elemento que contém o ficheiro para o MP4 de velocidade de transmissão adaptável definido.
1. Crie uma chave de conteúdo e associe-a com elemento codificado. Nos Media Services, a chave de conteúdo contém a chave de encriptação do elemento. 
1. Configure a política de autorização da chave de conteúdo. A política de autorização da chave de conteúdo tem de ser configurada por si e cumprida pelo cliente para que a chave de conteúdo seja entregue ao cliente.

    Ao criar a política de autorização da chave de conteúdo, tem de especificar o seguinte: método de entrega (PlayReady ou Widevine), restrições (aberto ou token) e informações específicas para o tipo de entrega de chave que define a forma como esta é entregue ao cliente (modelo de licença [PlayReady](media-services-playready-license-template-overview.md) ou [Widevine](media-services-widevine-license-template-overview.md)). 
1. Configure a política de entrega para um elemento. A configuração da política de entrega inclui: protocolo entrega (por exemplo, MPEG DASH, HLS, HDS, Transmissão em Fluxo Uniforme ou todos), o tipo de encriptação dinâmica (por exemplo, Encriptação Comum), URL de aquisição de licença PlayReady ou Widevine. 
 
    Pode aplicar uma política diferente para cada protocolo no mesmo elemento. Por exemplo, pode aplicar encriptação PlayReady para Uniforme/DASH e Envelope AES para HLS. Quaisquer protocolos que não estão definidos numa política de entrega (por exemplo, adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é quando não há qualquer política de entrega de elemento definida. Em seguida, todos os protocolos serão permitidos.
1. Crie um localizador OnDemand para obter um URL de transmissão em fluxo.

Irá encontrar um exemplo .NET concluído no final do tópico.

A imagem seguinte demonstra o fluxo de trabalho descrito acima. Aqui, o token é utilizado para autenticação.

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

O resto deste tópico fornece explicações detalhadas, exemplos de códigos e ligações para tópicos que mostram como atingir as tarefas descritas acima. 

##Limitações atuais

Se adicionar ou atualizar uma política de entrega de elemento, tem de eliminar o localizador associado (se aplicável) e criar um novo localizador.

Limitação ao encriptar com Widevine com os Media Services do Azure: atualmente, não são suportados várias chaves de conteúdo. 

##Criar um elemento e carregar ficheiros no elemento.

Para poder gerir, codificar e transmitir em fluxo os seus vídeos, primeiro tem de carregar o conteúdo para os Media Services do Microsoft Azure. Assim que seja carregado, o seu conteúdo é armazenado em segurança na nuvem para processamento adicional e a transmissão em fluxo. 

Para obter informações detalhadas, consulte [Carregar Ficheiros para uma conta de Media Services](media-services-dotnet-upload-files.md).

##Codificar o elemento que contém o ficheiro para o MP4 de velocidade de transmissão adaptável definido

Com a encriptação dinâmica, apenas tem de criar um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Transmissão em Fluxo Uniforme de múltipla transmissão. Em seguida, com base no formato especificado no manifesto e pedido de fragmento, o servidor de Transmissão em Fluxo a Pedido irá garantir que recebe a transmissão em fluxo no protocolo que escolheu. Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente. Para obter mais informações, consulte o tópico [Descrição Geral de Empacotamento Dinâmico](media-services-dynamic-packaging-overview.md).

Para obter instruções sobre como codificar, consulte [Como codificar um elemento utilizando um Codificador de Multimédia Standard](media-services-dotnet-encode-with-media-encoder-standard.md).
    

##<a id="create_contentkey"></a>Criar uma chave de conteúdo e associe-a com elemento codificado

Nos Media Services, a chave de conteúdo contém a chave na qual pretende encriptar um elemento.

Para obter informações detalhadas, consulte [Criar chave de conteúdo](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Configurar a política de autorização da chave de conteúdo

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo tem de ser configurada por si e cumprida pelo cliente (leitor) para que a chave seja entregue ao cliente. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização: aberto ou token restrito.

Para obter informações detalhadas, consulte [Configurar a Política de Autorização da Chave de Conteúdo](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Configurar a política de entrega de elemento 

Configure a política de entrega para o seu elemento. Alguns dos aspetos da configuração de política de entrega de elementos incluem:

- O URL de aquisição da licença da DRM. 
- O protocolo de entrega do elemento, (por exemplo, MPEG DASH, HLS, HDS, Transmissão em Fluxo Uniforme ou todos). 
- O tipo de encriptação dinâmica (neste caso, a Encriptação Comum). 

Para obter informações detalhadas, consulte [Configurar a política de entrega de elemento](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Criar um localizador de transmissão em fluxo OnDemand para obter um URL de transmissão em fluxo

Terá de fornecer aos seus utilizadores o URL de transmissão em fluxo Uniforme, DASH ou HLS.

>[AZURE.NOTE]Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar um localizador existente (se aplicável) e criar um novo localizador.

Para obter instruções sobre como publicar um elemento e compilar um URL de transmissão em fluxo, consulte [Compilar um URL de transmissão em fluxo](media-services-deliver-streaming-content.md).

##Obter um token de teste

Obtenha um token de teste baseado na restrição de token que foi utilizada para a política de autorização de chave.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

    
Pode utilizar o [Leitor AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para testar a sua transmissão em fluxo.

##<a id="example"></a>Exemplo


O exemplo seguinte demonstra a funcionalidade que foi introduzida no SDK de Media Services do Azure para .Net-versão 3.5.2 (especificamente, a capacidade de definir um modelo de licença Widevine e pedir uma licença Widevine a partir dos Media Services do Azure). O seguinte comando do pacote Nuget foi utilizado para instalar o pacote:

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Crie um novo projeto de Consola.
1. Utilize NuGet para instalar e adicionar SDK .NET de Media Services do Azure.
2. Adicione referências adicionais: System.Configuration.
2. Adicione o ficheiro de configuração que contém o nome da conta e as informações da chave:
    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. Obter, pelo menos, uma unidade de transmissão em fluxo para o ponto final de transmissão em fluxo a partir do qual planeia distribuir o conteúdo. Para obter mais informações, consulte: [configurar pontos finais de transmissão em fluxo](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Substitua o código no seu ficheiro Program.cs com o código mostrado nesta secção.
    
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
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
        
        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
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
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
                     
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
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
        
                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);
        
                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
        
                    Console.WriteLine("Upload {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    locator.Delete();
                    policy.Delete();
        
                    return inputAsset;
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";
            
                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));
            
                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
            
                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
            
                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),    AssetCreationOptions.StorageEncrypted);
            
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
            
                    return job.OutputMediaAssets[0];
                }
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
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
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("", 
                            ContentKeyDeliveryType.Widevine, 
                            restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;
        
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
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
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
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
        
                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.
        
                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                    //It contains a field for a custom data string between the license server and the application 
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
        
                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users. 
                    //It contains the data on the content key in the license and any rights or restrictions to be 
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client) 
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
        
                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;
        
                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                    // It grants the user the ability to playback the content subject to the zero or more restrictions 
                    // configured in the license and on the PlayRight itself (for playback specific policy). 
                    // Much of the policy on the PlayRight has to do with output restrictions 
                    // which control the types of outputs that the content can be played over and 
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                    //then the DRM runtime will only allow the video to be displayed over digital outputs 
                    //(analog video outputs won’t be allowed to pass the content).
        
                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                    // If the output protections are configured too restrictive, 
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
        
                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
        
                    responseTemplate.LicenseTemplates.Add(licenseTemplate);
        
                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }
        
        
                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };
        
                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    // Get the PlayReady license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
                
                    // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
                    // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
                    // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
                    // to append /? KID =< keyId > to the end of the url when creating the manifest.
                    // As a result Widevine license acquisition URL will have KID appended twice, 
                    // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.
            
                    Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    UriBuilder uriBuilder = new UriBuilder(widevineUrl);
                    uriBuilder.Query = String.Empty;
                    widevineUrl = uriBuilder.Uri;
        
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
        
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
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


##Percursos de aprendizagem dos Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Enviar comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Consultar também

[CENC com Múltipla DRM e Controlo de Acesso](media-services-cenc-with-multidrm-access-control.md)

[Configurar empacotamento Widevine com AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Anunciar os serviços de entrega de licença Widevine da Google nos Media Services do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)



<!--HONumber=Jun16_HO2-->


