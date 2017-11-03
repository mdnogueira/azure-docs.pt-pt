---
title: "Encriptar o conteúdo com a encriptação de armazenamento através da API de REST do AMS"
description: "Saiba como encriptar o seu conteúdo com a encriptação de armazenamento utilizando as APIs REST do AMS."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: 1979f5bf5e8cab88dab5fba49018afacf24504b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Encriptar o conteúdo com a encriptação de armazenamento

É altamente recomendado para encriptar o conteúdo localmente utilizando AES 256 bits encriptação e, em seguida, carregue-o no Storage do Azure onde será armazenado encriptados em pausa.

Este artigo fornece uma descrição geral de encriptação de armazenamento do AMS e mostra como carregar o conteúdo do armazenamento encriptado:

* Crie uma chave de conteúdo.
* Crie um elemento. Defina o AssetCreationOption para StorageEncryption ao criar o elemento.
  
     Ativos encriptados têm de ser associado com chaves de conteúdo.
* Ligar a chave de conteúdo para o elemento.  
* Definir a encriptação relacionadas com parâmetros AssetFile entidades.

## <a name="considerations"></a>Considerações 

Se pretender fornecer um recurso encriptados de armazenamento, é necessário configurar a política de entrega de elementos. Antes do elemento possa ser transmitido, o servidor de transmissão em fluxo remove a encriptação de armazenamento e fluxos de conteúdo através da política de entrega especificado. Para obter mais informações, consulte [configurar políticas de entrega de elemento](media-services-rest-configure-asset-delivery-policy.md).

Ao aceder a entidades nos Media Services, tem de definir campos de cabeçalho específicos e os valores no seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST de serviços de suporte de dados](media-services-rest-how-to-use.md). 

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à API do AMS, consulte [aceder à API de serviços de suporte de dados do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Depois de ligar com êxito a https://media.windows.net, receberá um redirecionamento 301 especificando noutro URI de serviços de suporte de dados. Tem de se as chamadas subsequentes para o novo URI.

## <a name="storage-encryption-overview"></a>Descrição geral de encriptação de armazenamento
Aplica-se da encriptação de armazenamento do AMS **AES CTR** encriptação de modo a todo o ficheiro.  O modo de AES CTR é uma cifra de bloco pode encriptar os dados de comprimento arbitrário sem necessidade de preenchimento. Funciona ao encriptar o bloco um contador com o algoritmo de AES e, em seguida, XOR utilize a saída do AES com os dados para encriptar ou desencriptar.  O bloco de contador utilizado é construído ao copiar o valor da InitializationVector para bytes 0 a 7 do valor do contador e bytes 8 a 15 do valor do contador estão definidos como zero. O bloco de contador de bytes de 16 bytes 8 a 15 (ou seja, os menos significativos bytes) são utilizados como um simples de 64 bits número inteiro não assinado que é incrementado por um para cada bloco de dados subsequente processado e é mantido na ordem de bytes de rede. Tenha em atenção que se este número inteiro atingir o valor máximo (0xFFFFFFFFFFFFFFFF) incrementando-repõe o contador de bloqueio para zero (bytes 8 a 15) sem afetar outros 64 bits do contador (ou seja, bytes 0 a 7).   Para poder manter a segurança de encriptação de modo AES CTR, o valor de InitializationVector para um determinado identificador da chave para cada chave de conteúdo deverá ser exclusivo para cada ficheiro e ficheiros deverá ser inferior a 2 ^ 64 blocos de comprimento.  Isto é para garantir que um valor de contador nunca é reutilizado com uma chave especificada. Para mais informações sobre o modo CTR, consulte [nesta página wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (artigo do wiki utiliza o termo "Nonce" em vez de "InitializationVector").

Utilize **encriptação de armazenamento** para encriptar o seu conteúdo transparente localmente utilizando AES 256 bit de encriptação e, em seguida, carregue-o ao Storage do Azure onde é armazenado encriptados em descanso ao. Os elementos protegidos com encriptação do storage são desencriptados automaticamente colocados no sistema de ficheiros encriptados antes da codificação e opcionalmente encriptados novamente antes de serem carregados novamente como um novo elemento de saída. O principal motivo para a encriptação de armazenamento é quando pretender proteger os ficheiros de suporte de dados de entrada de alta qualidade com uma encriptação forte Inativos no disco.

Para fornecer um recurso encriptados de armazenamento, tem de configurar política de entrega de elementos para que os Media Services confie como pretende distribuir os seus conteúdos. Antes do elemento possa ser transmitido, o servidor de transmissão em fluxo remove a encriptação de armazenamento e fluxos de conteúdo através da política de entrega especificado (por exemplo, AES, encriptação comum ou sem encriptação).

## <a name="create-contentkeys-used-for-encryption"></a>Criar ContentKeys utilizada para encriptação
Ativos encriptados têm de ser associada à chave de encriptação de armazenamento. Tem de criar a chave de conteúdo a ser utilizada para encriptação antes de criar os ficheiros de recurso. Esta secção descreve como criar uma chave de conteúdo.

Seguem-se passos gerais para a geração de chaves de conteúdo que irá associar recursos que pretende que sejam encriptados. 

1. Para a encriptação de armazenamento, gere aleatoriamente uma chave AES de 32-byte. 
   
    Esta será a chave de conteúdo para o seu elemento, o que significa que todos os ficheiros associados esse recurso, terá de utilizar a mesma chave de conteúdo durante a desencriptação. 
2. Chamar o [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) e [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) métodos para obter o certificado x. 509 correto, que tem de ser utilizado para encriptar a chave de conteúdo.
3. Encriptar o chave de conteúdo com a chave pública do certificado x. 509. 
   
   SDK .NET dos Media Services utiliza RSA com OAEP ao efetuar a encriptação.  Pode ver um exemplo de .NET no [EncryptSymmetricKeyData função](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Crie um valor de soma de verificação calculado com o identificador da chave e a chave de conteúdo. O seguinte exemplo de .NET calcula a soma de verificação utilizando o GUID parte do identificador da chave e a chave de limpar conteúdo.

        public static string CalculateChecksum(byte[] contentKey, Guid keyId)
        {
            const int ChecksumLength = 8;
            const int KeyIdLength = 16;

            byte[] encryptedKeyId = null;

            // Checksum is computed by AES-ECB encrypting the KID
            // with the content key.
            using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
            {
                rijndael.Mode = CipherMode.ECB;
                rijndael.Key = contentKey;
                rijndael.Padding = PaddingMode.None;

                ICryptoTransform encryptor = rijndael.CreateEncryptor();
                encryptedKeyId = new byte[KeyIdLength];
                encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
            }

            byte[] retVal = new byte[ChecksumLength];
            Array.Copy(encryptedKeyId, retVal, ChecksumLength);

            return Convert.ToBase64String(retVal);
        }

1. Criar a chave de conteúdo com o **EncryptedContentKey** (em cadeia com codificação base64), **ProtectionKeyId**, **ProtectionKeyType**,  **ContentKeyType**, e **soma de verificação** valores receberam nos passos anteriores.

    Para a encriptação de armazenamento, as propriedades seguintes devem ser incluídas no corpo do pedido.

    Propriedade de corpo do pedido    | Descrição
    ---|---
    Id | O Id de ContentKey que geramos ourselves utilizando o seguinte formato, "nb:kid:UUID:<NEW GUID>".
    ContentKeyType | Este é o tipo de chave de conteúdo como um valor inteiro para esta chave de conteúdo. Podemos transmitir o valor 1 para a encriptação de armazenamento.
    EncryptedContentKey | Vamos criar um novo valor de chave conteúdo que é um valor de 256 bits (32 byte). A chave é encriptada utilizando o certificado x. 509 encriptação de armazenamento que obtemos Media Services do Microsoft Azure ao executar um pedido de HTTP GET para o GetProtectionKeyId e GetProtectionKey métodos. Por exemplo, consulte o seguinte código de .NET: o **EncryptSymmetricKeyData** método definido [aqui](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Este é o id de chave de proteção para o certificado x. 509 encriptação de armazenamento que foi utilizado para encriptar o nosso chave de conteúdo.
    ProtectionKeyType | Este é o tipo de encriptação da chave de proteção que foi utilizado para encriptar a chave de conteúdo. Este valor é StorageEncryption(1) para o nosso exemplo.
    Soma de verificação |A MD5 calculada soma de verificação para a chave de conteúdo. Esta é calculada ao encriptar o conteúdo de Id com a chave de conteúdo. O código de exemplo demonstra como calcular a soma de verificação.


### <a name="retrieve-the-protectionkeyid"></a>Obter o ProtectionKeyId
O exemplo seguinte mostra como obter ProtectionKeyId, um thumbprint de certificado, para o certificado que tem de utilizar ao encriptar a chave de conteúdo. Execute este passo para se certificar de que já tenha o certificado adequado no seu computador.

Pedido:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net

Resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Obter o ProtectionKey o ProtectionKeyId
O exemplo seguinte mostra como obter o certificado x. 509 utilizando o ProtectionKeyId recebido no passo anterior.

Pedido:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

Resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Criar a chave de conteúdo
Depois de obter o certificado x. 509 e utilizar a chave pública para encriptar a chave de conteúdo, crie um **ContentKey** entidade e defina a propriedade de valores em conformidade.

Um dos valores que tem de definir quando criar o conteúdo da chave é do tipo. Em caso da encriptação de armazenamento, o valor é '1'. 

O exemplo seguinte mostra como criar um **ContentKey** com um **ContentKeyType** definido para a encriptação de armazenamento ("1") e o **ProtectionKeyType** definido como "0" para indicar que a chave de proteção Id é o thumbprint do certificado x. 509.  

Pedir

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Criar um recurso
O exemplo seguinte mostra como criar um recurso.

**Pedido de HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte:

    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>Associe o ContentKey com um elemento
Depois de criar o ContentKey, associá-lo com o seu elemento utilizando a operação de $links, conforme mostrado no exemplo seguinte:

Pedido:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Resposta:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Criar um AssetFile
O [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entidade representa um ficheiro de vídeo ou áudio que é armazenado num contentor de blob. Um ficheiro de recurso é sempre associado a um recurso e um recurso pode conter um ou vários ficheiros de recurso. A tarefa de codificador de serviços de suporte de dados falha se um objeto de ficheiro de recurso não está associado um ficheiro digital num contentor de blob.

Tenha em atenção que o **AssetFile** instância e o ficheiro de multimédia real são dois objetos distintos. A instância de AssetFile contém metadados sobre o ficheiro de suporte de dados, enquanto o ficheiro de suporte de dados contém o conteúdo de multimédia real.

Depois de carregar o ficheiro de suporte de dados digitais num contentor de blob, irá utilizar o **intercalar** pedido de HTTP para atualizar o AssetFile com as informações sobre o ficheiro de suporte de dados (não mostrado neste tópico). 

**Pedido de HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }

**Resposta de HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
