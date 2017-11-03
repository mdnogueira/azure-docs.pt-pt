---
title: "Configurar políticas de entrega de elemento com o .NET SDK | Microsoft Docs"
description: "Este tópico mostra como configurar políticas de entrega de elemento diferentes com o SDK .NET do Azure Media Services."
services: media-services
documentationcenter: 
author: Mingfeiy
manager: cfowler
editor: 
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/13/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 282fd9e24dc147e31613469926128894d48366f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurar políticas de entrega de elemento com o .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Descrição geral
Se planear recursos de entrega encriptado, um dos passos no fluxo de trabalho de entrega de conteúdos de Media Services está a configurar políticas de entrega de elementos. A política de entrega de elemento indica aos Media Services como pretende que os para o seu elemento a entregar: para o protocolo de transmissão em fluxo deve seu elemento ser dinamicamente compactado (por exemplo, MPEG DASH, HLS, transmissão em fluxo uniforme ou todos), pretende encriptar de forma dinâmica ou não o elemento e como (envelope ou encriptação comum).

Este tópico aborda porquê e como criar e configurar políticas de entrega de elemento.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
>
>Além disso, para poder utilizar o empacotamento dinâmico e a encriptação dinâmica seu elemento tem de conter um conjunto de MP4s de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável.


Pode aplicar políticas diferentes para o mesmo elemento. Por exemplo, pode aplicar encriptação PlayReady para encriptação de transmissão em fluxo uniforme e AES Envelope para MPEG DASH e HLS. Quaisquer protocolos que não estão definidos numa política de entrega (por exemplo, adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é quando não há qualquer política de entrega de elemento definida. Em seguida, todos os protocolos serão permitidos.

Se pretender fornecer um recurso encriptados de armazenamento, é necessário configurar a política de entrega de elementos. Antes do elemento possa ser transmitido, o servidor de transmissão em fluxo remove a encriptação de armazenamento e fluxos de conteúdo através da política de entrega especificado. Por exemplo, para fornecer o seu elemento encriptado com a chave de encriptação do envelope Advanced Encryption Standard (AES), defina o tipo de política para **DynamicEnvelopeEncryption**. Para remover a encriptação de armazenamento e transmitir em fluxo o elemento na limpar, defina o tipo de política para **NoDynamicEncryption**. Veja a seguir exemplos mostram como configurar estes tipos de política.

Dependendo de como configurar a política de entrega de elemento conseguiriam dinamicamente o pacote, dinamicamente encriptar e transmitir os seguintes protocolos de transmissão em fluxo: transmissão em fluxo uniforme, HLS, transmissão e MPEG DASH.

A lista seguinte mostra os formatos que utilizar para transmitir em fluxo uniforme, HLS e travessão.

Transmissão em fluxo uniforme:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{de transmissão em fluxo ponto final dos serviços de suporte de dados de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{de transmissão em fluxo ponto final dos serviços de suporte de dados de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


## <a name="considerations"></a>Considerações
* Não é possível eliminar um AssetDeliveryPolicy associado um recurso enquanto existe um localizador OnDemand (transmissão em fluxo) para esse recurso. Recomenda-se remover a política do recurso antes de eliminar a política.
* Um localizador de transmissão em fluxo não é possível criar um recurso encriptados de armazenamento quando não está definida nenhuma política de entrega de elemento.  Se o elemento não está encriptado de armazenamento, o sistema permitirá criar um localizador e transmitir em fluxo o elemento no limpar sem uma política de entrega de elemento.
* Pode ter várias políticas de entrega de elemento associadas um único recurso mas só é possível especificar uma forma de lidar com uma determinada AssetDeliveryProtocol.  Que significa que o se tentar ligar duas políticas de entrega que especifique o protocolo de AssetDeliveryProtocol.SmoothStreaming resultará num erro porque o sistema não souber que aquela que pretende que se aplicam quando um cliente faz um pedido de transmissão em fluxo uniforme.
* Se tiver um recurso com um localizador de transmissão em fluxo existente, não é possível ligar uma nova política para o elemento (pode desassociar uma política existente do elemento, ou atualizar uma política de entrega associada com o elemento).  Primeiro tem de remover o localizador de transmissão em fluxo, ajustar as políticas e, em seguida, voltar a criar o localizador de transmissão em fluxo.  Pode utilizar o mesmo locatorId ao recriar o localizador de transmissão em fluxo, mas deve assegurar que não irá causar problemas para os clientes, uma vez que pode ser colocado na cache por origem ou uma CDN a jusante.

## <a name="clear-asset-delivery-policy"></a>Política de entrega de elemento encriptado

O seguinte **ConfigureClearAssetDeliveryPolicy** método especifica não aplicar encriptação dinâmica e para fornecer o fluxo em qualquer um dos seguintes protocolos: MPEG DASH, HLS, transmissão e transmissão em fluxo uniforme protocolos. Poderá aplicar esta política para os recursos de armazenamento encriptado.

Para informações sobre quais os valores que pode especificar quando criar um AssetDeliveryPolicy, consulte o [tipos utilizados quando se definem AssetDeliveryPolicy](#types) secção.

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de elemento DynamicCommonEncryption

O seguinte **CreateAssetDeliveryPolicy** método cria o **AssetDeliveryPolicy** que está configurado para aplicar a encriptação comum dinâmica (**DynamicCommonEncryption**) num protocolo de transmissão em fluxo uniforme (serão impedidos outros protocolos de transmissão em fluxo). O método aceita dois parâmetros: **Asset** (o recurso ao qual pretende aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do **CommonEncryption** tipo, para obter mais informações, consulte: [criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para informações sobre quais os valores que pode especificar quando criar um AssetDeliveryPolicy, consulte o [tipos utilizados quando se definem AssetDeliveryPolicy](#types) secção.

    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }

Também o Media Services do Azure permite-lhe adicionar encriptação Widevine. O exemplo seguinte demonstra PlayReady e Widevine a ser adicionado à política de entrega de elemento.

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
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

> [!NOTE]
> Encriptar com Widevine, só será possível entregar DASH a utilizar. Certifique-se especificar DASH no protocolo de entrega de elemento.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de elemento DynamicEnvelopeEncryption
O seguinte **CreateAssetDeliveryPolicy** método cria o **AssetDeliveryPolicy** que está configurado para aplicar a encriptação dinâmica envelope (**DynamicEnvelopeEncryption**) para protocolos de transmissão em fluxo uniforme, HLS e travessão (se optar por não especificar alguns protocolos, serão bloqueados da transmissão em fluxo). O método aceita dois parâmetros: **Asset** (o recurso ao qual pretende aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do **EnvelopeEncryption** tipo, Para obter mais informações, consulte: [criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Para informações sobre quais os valores que pode especificar quando criar um AssetDeliveryPolicy, consulte o [tipos utilizados quando se definem AssetDeliveryPolicy](#types) secção.   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


## <a id="types"></a>Tipos de utilizado quando se definem AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

A enumeração seguinte descreve os valores que pode definir para o protocolo de entrega de elemento.

    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

A enumeração seguinte descreve os valores que pode definir para o tipo de política de entrega de elemento.  

    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

A enumeração seguinte descreve os valores que pode utilizar para configurar o método de fornecimento da chave de conteúdo para o cliente.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

A enumeração seguinte descreve os valores que pode definir para configurar as chaves utilizadas para obter a configuração específicos para uma política de entrega de elemento.

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

