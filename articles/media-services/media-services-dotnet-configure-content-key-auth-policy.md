---
title: "Configurar a política de autorização da chave de conteúdo utilizando o SDK .NET dos Media Services | Microsoft Docs"
description: "Saiba como configurar uma política de autorização para uma chave de conteúdo utilizando o SDK .NET dos Media Services."
services: media-services
documentationcenter: 
author: Mingfeiy
manager: cfowler
editor: 
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 75dd9107dca215a0b31db3d44bada69210fe9ac6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>Encriptação dinâmica: configurar a política de autorização da chave de conteúdo
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Descrição geral
Serviços de suporte de dados do Microsoft Azure permite-lhe entregar MPEG-DASH, transmissão em fluxo uniforme e HTTP-Live-Streaming (HLS) protegidas com o padrão AES (Advanced Encryption) (utilizando as chaves de encriptação de 128 bits) ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS também permitem entregar transmissões em fluxo DASH encriptados com Widevine DRM. Tanto PlayReady como Widevine são encriptados de acordo com a especificação de Encriptação Comum (ISO/IEC 23001 7 CENC).

Os Media Services também fornecem uma **serviço de entrega de licença/chave** do que os clientes podem obter chaves AES ou licenças PlayReady/Widevine para reproduzir o conteúdo encriptado.

Se pretender que para os serviços de suporte de dados encriptar um recurso, tem de associar uma chave de encriptação (**CommonEncryption** ou **EnvelopeEncryption**) com o elemento (tal como descrito [aqui](media-services-dotnet-create-contentkey.md)) e também configurar políticas de autorização da chave (como descrito neste artigo).

Quando um fluxo é solicitado por um leitor, os Media Services utiliza a chave especificada dinamicamente encriptar o seu conteúdo através da encriptação AES ou DRM. Para desencriptar o fluxo, o leitor solicitará a chave do serviço de entrega de chave. Para decidir se pretende ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter um ou mais restrições de autorização: **abrir** ou **token** restrição. A política de token restrito tem de ser acompanhada por um token emitido por um Serviço de Token Seguro (STS). Os Media Services suportam tokens no **Web Tokens simples** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) formato e **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) formato.

Serviços de suporte de dados não fornece serviços de Token seguro. Pode criar um STS personalizado ou tirar partido das ACS do Microsoft Azure para tokens de problema. O STS tem de ser configurado para criar o token assinado com especificado chave e emitir afirmações que especificou na configuração de restrição de token (conforme descrito neste artigo). O serviço de entrega de chave de Media Services irá devolver a chave de encriptação para o cliente se o token é válido e as afirmações no token correspondem às configurado para a chave de conteúdo.

Para obter mais informações, veja

[Autenticação de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar a aplicação do Azure Media Services OWIN MVC com base no Azure Active Directory e restringir a entrega de chave de conteúdo com base em afirmações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Utilizar o Azure ACS para tokens de problema](http://mingfeiy.com/acs-with-key-services).

### <a name="some-considerations-apply"></a>São aplicáveis algumas considerações:
* Quando a sua conta de AMS é criada um **predefinido** ponto final de transmissão em fluxo é adicionado à sua conta na **parado** estado. Para iniciar o conteúdo de transmissão em fluxo e tirar partido do empacotamento dinâmico e a encriptação dinâmica, o ponto final de transmissão em fluxo tem de ser o **executar** estado. 
* O elemento tem de conter um conjunto de MP4s de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável. Para obter mais informações, consulte [codificar um elemento](media-services-encode-asset.md).
* Carregar e codificar seus ativos utilizando **AssetCreationOptions.StorageEncrypted** opção.
* Se planear ter várias chaves de conteúdo que requerem a mesma configuração de política, é vivamente recomendado para criar uma política de autorização único e reutilizá-lo com várias chaves de conteúdo.
* O serviço de entrega de chave coloca em cache ContentKeyAuthorizationPolicy e os respetivos objetos relacionados (opções de política e restrições) para 15 minutos.  Se criar um ContentKeyAuthorizationPolicy e especifique a utilização de uma restrição "Token", em seguida, testá-lo e, em seguida, atualizar a política a restrição "Abrir", irá demorar cerca de 15 minutos antes da política muda para a versão "Aberta" da política.
* Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar um localizador existente (se aplicável) e criar um novo localizador.
* Atualmente, não é possível encriptar transferências de transferência progressiva.

## <a name="aes-128-dynamic-encryption"></a>Encriptação dinâmica AES-128
### <a name="open-restriction"></a>Restrição aberta
Abra restrição significa que o sistema irá fornecer a chave para qualquer pessoa que faz um pedido de chave. Esta restrição poderão ser úteis para fins de teste.

O exemplo seguinte cria uma política de autorização aberta e adiciona-o para a chave de conteúdo.

    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }


### <a name="token-restriction"></a>Restrição de token
Esta secção descreve como criar uma política de autorização da chave de conteúdo e associe-a com a chave de conteúdo. A política de autorização descreve os requisitos de autorização devem ser cumpridos para determinar se o utilizador está autorizado para receberem a chave (por exemplo, does a lista de "chave de verificação" contém a chave que o token foi assinado com).

Para configurar a opção de restrição de token, terá de utilizar um XML para descrever os requisitos de autorização do token. O XML de configuração de restrição de token tem de estar em conformidade com o esquema XML seguinte.

#### <a id="schema"></a>Esquema de restrição de token
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

Quando configurar o **token** restringido a política, tem de especificar o site primário * * verificação chave * *, **emissor** e **público-alvo** parâmetros. O * * a chave de verificação principal * * contém a chave que o token foi assinado com, **emissor** é o serviço de token seguro que emite o token. O **público-alvo** (por vezes denominado **âmbito**) descreve o token ou o recurso autoriza o token de acesso para o objetivo. O serviço de entrega de chave de Media Services valida que estes valores no token correspondem aos valores no modelo. 

Quando utilizar **SDK de Media Services para .NET**, pode utilizar o **TokenRestrictionTemplate** class para gerar o token de restrição.
O exemplo seguinte cria uma política de autorização com uma restrição de token. Neste exemplo, o cliente terá de apresentar um token que contém: assinatura de chave (VerificationKey), um emissor de token e afirmações necessárias.

    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

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

#### <a id="test"></a>Token de teste
Para obter um token de teste com base na restrição de token que foi utilizada para a política de autorização da chave, efetue o seguinte.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


## <a name="playready-dynamic-encryption"></a>Encriptação PlayReady dinâmico
Os Media Services permite-lhe configurar os direitos e as restrições que pretende para o tempo de execução de PlayReady DRM a impor quando um utilizador está a tentar reproduzir conteúdo protegido. 

Quando proteger o conteúdo com PlayReady, uma das ações tem de especificar na sua política de autorização é uma cadeia XML que define o [o modelo de licença PlayReady](media-services-playready-license-template-overview.md). No SDK de Media Services para .NET, o **PlayReadyLicenseResponseTemplate** e **PlayReadyLicenseTemplate** classes irão ajudar a definir o modelo de licença PlayReady.

[Este tópico](media-services-protect-with-drm.md) mostra como encriptar o seu conteúdo com **PlayReady** e **Widevine**.

### <a name="open-restriction"></a>Restrição aberta
Abra restrição significa que o sistema irá fornecer a chave para qualquer pessoa que faz um pedido de chave. Esta restrição poderão ser úteis para fins de teste.

O exemplo seguinte cria uma política de autorização aberta e adiciona-o para a chave de conteúdo.

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

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

### <a name="token-restriction"></a>Restrição de token
Para configurar a opção de restrição de token, terá de utilizar um XML para descrever os requisitos de autorização do token. A configuração de restrição de token XML tem de estar em conformidade com o esquema XML mostrado na [isto](#schema) secção.

    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

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


Para obter um token de teste com base na restrição de token que foi utilizada para o consulte de política de autorização da chave [isto](#test) secção. 

## <a id="types"></a>Tipos de utilizado quando se definem ContentKeyAuthorizationPolicy
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

### <a id="TokenType"></a>TokenType
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passo seguinte
Agora que configurou a política de autorização da chave de conteúdo, vá para o [como configurar a política de entrega de elemento](media-services-dotnet-configure-asset-delivery-policy.md) tópico.

