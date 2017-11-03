---
title: "O Azure Active Directory B2C: Proteger o seu serviço RESTful utilizando certificados de cliente"
description: "Proteger o seu trocas de afirmações de REST API personalizadas no seu Azure AD B2C utilizando certificados de cliente"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 867484799020a4e65844523a88240b3d550c69f7
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Proteger o seu serviço RESTful utilizando certificados de cliente
Um artigo relacionado, é [criar um serviço RESTful](active-directory-b2c-custom-rest-api-netfw.md) que interage com o Azure Active Directory B2C (Azure AD B2C).

Neste artigo, irá aprender a restringir o acesso à sua aplicação web do Azure (RESTful API) utilizando um certificado de cliente. Este mecanismo denomina-se a autenticação mútua TLS, ou *autenticação de certificado de cliente*. Apenas os serviços que têm os certificados adequados, como o Azure AD B2C, podem aceder ao seu serviço.

>[!NOTE]
>Também pode proteger o seu serviço RESTful utilizando [autenticação básica HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). No entanto, a autenticação básica do HTTP é considerada menos segura através de um certificado de cliente. É a nossa recomendação para proteger o serviço RESTful, utilizando a autenticação de certificado de cliente, tal como descrito neste artigo.

Detalhes deste artigo como:
* Configure a aplicação web para utilizar a autenticação de certificado de cliente.
* Carregue o certificado para as chaves de política do Azure AD B2C.
* Configure a política personalizada para utilizar o certificado de cliente.

## <a name="prerequisites"></a>Pré-requisitos
* Concluir os passos a [trocas de afirmações de integrar o API de REST](active-directory-b2c-custom-rest-api-netfw.md) artigo.
* Obter um certificado válido (um ficheiro. pfx com uma chave privada).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Passo 1: Configurar uma aplicação web para autenticação de certificados de cliente
Para configurar **App Service do Azure** para exigir certificados de cliente, definir a aplicação web `clientCertEnabled` definição do site *verdadeiro*. Para efetuar esta alteração, tem de utilizar a API REST. A definição está disponível através de experiência de gestão no portal do Azure. Para localizar a definição na sua aplicação RESTful **definições** menu, em **ferramentas de desenvolvimento**, selecione **Explorador de recursos**.

>[!NOTE]
>Certifique-se de que o plano do App Service do Azure Standard ou superior. Para obter mais informações, consulte [descrição geral dos planos do App Service do Azure](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).


Utilize [Explorador de recursos do Azure (pré-visualização)](https://resources.azure.com) para definir o **clientCertEnabled** propriedade *verdadeiro*, conforme mostrado na imagem seguinte:

![Definição clientCertEnabled através do Explorador de recursos do Azure](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

>[!NOTE]
>Para obter mais informações sobre a definição de **clientCertEnabled** propriedade, consulte [autenticação mútua do TLS configurar para aplicações web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

>[!TIP]
>Em alternativa, para facilitar o craft a chamada de REST API, pode utilizar o [ARMClient](https://github.com/projectkudu/ARMClient) ferramenta.

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Passo 2: Carregue o certificado para as chaves de política do Azure AD B2C
Depois de definir `clientCertEnabled` para *verdadeiro*, a comunicação com a API RESTful requer um certificado de cliente. Para obter, carregar e armazenar o certificado de cliente no seu inquilino do Azure AD B2C, efetue o seguinte: 
1. No seu inquilino do Azure AD B2C, selecione **definições do B2C** > **identidade experiência Framework**.

2. Para ver as chaves que estão disponíveis no seu inquilino, selecione **política chaves**.

3. Selecione **Adicionar**.  
    O **criar uma chave** é aberta a janela.

4. No **opções** caixa, selecione **carregar**.

5. No **nome** caixa, escreva **B2cRestClientCertificate**.  
    O prefixo *B2C_1A_* é adicionado automaticamente.

6. No **carregamento de ficheiros** caixa, selecione o ficheiro. pfx do certificado com uma chave privada.

7. No **palavra-passe** caixa, escreva a palavra-passe do certificado.

    ![Carregar política chave](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Selecione **Criar**.

8. Para ver as chaves que estão disponíveis no seu inquilino e confirme que criou o `B2C_1A_B2cRestClientCertificate` principais, selecione **política chaves**.

## <a name="step-3-change-the-technical-profile"></a>Passo 3: Alterar o perfil técnico
Para suportar a autenticação de certificado de cliente na sua política personalizada, altere o perfil técnico efetuando o seguinte procedimento:

1. No seu diretório de trabalho, abra o *TrustFrameworkExtensions.xml* ficheiro de política de extensão.

2. Procure o `<TechnicalProfile>` nó que inclui `Id="REST-API-SignUp"`.

3. Localize o `<Metadata>` elemento.

4. Alterar o *AuthenticationType* para *ClientCertificate*, da seguinte forma:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Imediatamente após o fecho `<Metadata>` elemento, adicione o seguinte fragmento XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Depois de adicionar o fragmento, o seu perfil técnica deverá ter um aspeto semelhante ao seguinte código XML:

    ![Definir ClientCertificate autenticação XML elementos](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Passo 4: Carregar a política para o seu inquilino

1. No [portal do Azure](https://portal.azure.com), mude para o [contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, selecione **do Azure AD B2C**.

2. Selecione **identidade experiência Framework**.

3. Selecione **todas as políticas**.

4. Selecione **carregar política**.

5. Selecione o **substituir a política se existir** caixa de verificação.

6. Carregar o *TrustFrameworkExtensions.xml* de ficheiros e, em seguida, certifique-se de que transfere a validação.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Passo 5: Testar a política personalizada utilizando o executar agora
1. Abra **definições do Azure AD B2C**e, em seguida, selecione **identidade experiência Framework**.

    >[!NOTE]
    >Executar agora requer, pelo menos, uma aplicação para preregistered no inquilino. Para aprender a registar aplicações, consulte o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.

2. Abra **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) entidade confiadora que carregado e, em seguida, selecione **executar agora**.

3. O processo de teste, escrevendo **teste** no **o nome fornecido** caixa.  
    O Azure AD B2C apresenta uma mensagem de erro na parte superior da janela.    

    ![Testar a sua identidade API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. No **o nome fornecido** caixa, escreva um nome (diferente de "teste").  
    O Azure AD B2C se inscreve o utilizador e, em seguida, envia um número de loyalty à sua aplicação. Tenha em atenção o número neste exemplo JWT:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Se receber a mensagem de erro, *o nome não é válido, forneça um nome válido*, significa que o do Azure AD B2C chama com êxito o serviço RESTful enquanto-apresentado o certificado de cliente. O passo seguinte é para validar o certificado.

## <a name="step-6-add-certificate-validation"></a>Passo 6: Adicionar validação de certificado
O certificado de cliente do Azure AD B2C envia ao seu serviço RESTful não tem de passar pela validação pela plataforma Web Apps do Azure, exceto para verifique se o certificado existe. Validar o certificado é da responsabilidade da aplicação web. 

Nesta secção, adicione o código ASP.NET de exemplo que valida as propriedades do certificado para efeitos de autenticação.

> [!NOTE]
>Para obter mais informações sobre como configurar o App Service do Azure para autenticação de certificados de cliente, consulte [autenticação mútua do TLS configurar para aplicações web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 adicionar definições da aplicação para o ficheiro do seu projeto Web. config
No projeto do Visual Studio que criou anteriormente, adicione as seguintes definições de aplicação para o *Web. config* ficheiro após o `appSettings` elemento:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Substituir o certificado **nome do requerente**, **nome do emissor**, e **thumbprint do certificado** valores com os valores de certificado.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 adicionar a função de IsValidClientCertificate
Abra o *Controllers\IdentityController.cs* de ficheiros e, em seguida, adicionar ao `Identity` a seguinte função de controlador de classe: 

```C#
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

No código de exemplo anterior, podemos aceitar o certificado como válido apenas se as seguintes condições são cumpridas:
* O certificado não expirou e está ativo para a hora atual no servidor.
* O `Subject` nome do certificado é igual do `ClientCertificate:Subject` valor de definição de aplicação.
* O `Issuer` nome do certificado é igual do `ClientCertificate:Issuer` valor de definição de aplicação.
* O `thumbprint` do certificado é igual do `ClientCertificate:Thumbprint` valor de definição de aplicação.

>[!IMPORTANT]
>Consoante a sensibilidade do seu serviço, poderá ter de adicionar mais validações. Por exemplo, poderá ter de testar se o certificado está encadeado para uma autoridade de raiz fidedigna, validar o nome da organização emissor e assim sucessivamente.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 chamada à função de IsValidClientCertificate
Abra o *Controllers\IdentityController.cs* ficheiro e, em seguida, no início a `SignUp()` de função, adicione o seguinte fragmento de código: 

```C#
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Depois de adicionar o fragmento, o `Identity` controlador deverá ter um aspeto semelhante ao seguinte código:

![Adicione o código de validação de certificado](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Passo 7: Publicar o seu projeto para o Azure e testá-lo
1. No **Explorador de soluções**, clique com botão direito do **Contoso.AADB2C.API** projeto e, em seguida, selecione **publicar**.

2. Repita "Passo 6" e voltar a política personalizada com a validação de certificado de teste. Tente executar a política e certifique-se de que tudo funciona depois de adicionar a validação.

3. No seu *Web. config* de ficheiros, altere o valor de `ClientCertificate:Subject` para **inválido**. Execute novamente a política e deverá ver uma mensagem de erro.

4. Altere o valor de volta para **válido**e certifique-se de que a política pode chamar a API REST.

Se precisar de resolver problemas relacionados com este passo, consulte [recolher registos utilizando o Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Transferir os ficheiros de política concluída e o código
* Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) explicação passo a passo, recomendamos que crie o seu cenário utilizando os seus próprios ficheiros de política personalizada. Para sua referência, fornecemos [ficheiros de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Pode transferir o código de conclusão de [solução do Visual Studio de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
