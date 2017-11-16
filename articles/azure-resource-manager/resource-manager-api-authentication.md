---
title: "Autenticação do Active Directory do Azure e o Resource Manager | Microsoft Docs"
description: "Guia do programador para autenticação com a API do Azure Resource Manager e o Azure Active Directory para integrar uma aplicação a outras subscrições do Azure."
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2017
ms.author: dugill;tomfitz
ms.openlocfilehash: 0b7ddaa7e8a98cdff0e92c87f8a1f7e24efbd67e
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Utilize o Gestor de recursos autenticação API para subscrições de acesso
## <a name="introduction"></a>Introdução
Se for um programador de software que tem de criar uma aplicação que gere os recursos do Azure de um cliente, este artigo mostra como autenticar com as APIs do Gestor de recursos do Azure e obter acesso aos recursos em outras subscrições.

A aplicação pode aceder a APIs do Resource Manager em duas formas:

1. **Utilizador + acesso de aplicação**: para aplicações que acedem aos recursos em nome de um utilizador com sessão iniciada. Esta abordagem funciona para as aplicações, tais como web apps e ferramentas de linha de comandos que lidam com apenas "interativo"gestão de recursos do Azure.
2. **Acesso só de aplicação**: para aplicações que executam os serviços de daemon e tarefas agendadas. A identidade da aplicação é concedida acesso direto aos recursos. Esta abordagem funciona para as aplicações que precisam de longa duração acesso (automático) sem interface para o Azure.

Este artigo fornece instruções passo a passo para criar uma aplicação que utiliza ambos os métodos de autorização. Mostra como efetuar cada passo com a REST API ou c#. A aplicação ASP.NET MVC completa está disponível em [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>O que faz a aplicação web
A aplicação web:

1. Inicia sessão um utilizador do Azure.
2. Pede-lhe utilizador para conceder o acesso de aplicação web para o Gestor de recursos.
3. Obtém o token de acesso de aplicação de utilizador + para aceder ao Gestor de recursos.
4. Utiliza o token (a partir do passo 3) para atribuir o principal de serviço da aplicação a uma função na subscrição. Este passo permite o acesso de longa duração de aplicação à subscrição.
5. Obtém acesso só de aplicação token.
6. Utiliza o token (a partir do passo 5) para gerir os recursos na subscrição através do Resource Manager.

Eis o fluxo da aplicação web.

![Fluxo de autenticação de Gestor de recursos](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Como um utilizador, forneça o ID de subscrição para a subscrição que pretende utilizar:

![Forneça o ID de subscrição](./media/resource-manager-api-authentication/sample-ux-1.png)

Selecione a conta a utilizar para iniciar sessão.

![Selecione a conta](./media/resource-manager-api-authentication/sample-ux-2.png)

Indique as suas credenciais.

![Forneça as credenciais](./media/resource-manager-api-authentication/sample-ux-3.png)

Conceda o acesso de aplicação às suas subscrições do Azure:

![Conceder acesso](./media/resource-manager-api-authentication/sample-ux-4.png)

Gerir as subscrições ligadas:

![Ligar a subscrição](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registar a aplicação
Antes de iniciar a codificação, registe a sua aplicação web com o Azure Active Directory (AD). O registo de aplicação cria uma identidade central para a sua aplicação no Azure AD. Realiza as informações básicas sobre a sua aplicação, como o ID de cliente OAuth, URLs de resposta e as credenciais que a aplicação utiliza para autenticar e acesso a APIs do Azure Resource Manager. O registo de aplicações também regista vários delegadas permissões que a aplicação que necessita ao aceder a Microsoft APIs em nome do utilizador.

Porque a aplicação acede a outra subscrição, tem de configurá-lo como uma aplicação multi-inquilino. Para passar a validação, forneça um domínio associado ao seu Azure Active Directory. Para ver os domínios associados com o Azure Active Directory, inicie sessão no portal.

O exemplo seguinte mostra como registar a aplicação com o Azure PowerShell. Tem de ter a versão mais recente (Agosto de 2016) do Azure PowerShell para este comando funcione.

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true

Para iniciar sessão como aplicação AD, é necessário o ID da aplicação e a palavra-passe. Para ver o ID da aplicação que é devolvido do comando anterior, utilize:

    $app.ApplicationId

O exemplo seguinte mostra como registar a aplicação ao utilizar a CLI do Azure.

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Os resultados incluem o AppId, o que precisa de quando a autenticação da aplicação.

### <a name="optional-configuration---certificate-credential"></a>Configuração opcional - credenciais de certificado
Azure AD também suporta credenciais de certificado para aplicações: criar um certificado autoassinado, mantenha a chave privada e adicione a chave pública para o seu registo de aplicações do Azure AD. Para autenticação, a aplicação envia um payload pequeno para o Azure AD assinado utilizando a chave privada e do Azure AD valida a assinatura utilizando a chave pública que registado.

Para obter informações sobre como criar uma aplicação AD com um certificado, consulte [utilize o Azure PowerShell para criar um principal de serviço para aceder a recursos](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority) ou [CLI do Azure de utilização para criar um principal de serviço para aceder a recursos](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Obter ID de inquilino do ID de subscrição
Para pedir um token que pode ser utilizado para chamar o Gestor de recursos, a aplicação tem de saber o ID de inquilino do inquilino do Azure AD que aloja a subscrição do Azure. Provavelmente, os utilizadores saibam que os respetivos IDs de subscrição, mas poderão não saber respetivo inquilino IDs para o Azure Active Directory. Para obter o ID de inquilino do utilizador, pedir ao utilizador para o ID de subscrição. Fornecer essa subscrição ID ao enviar um pedido acerca da subscrição:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

O pedido falha porque o utilizador não registou ainda, mas pode obter o ID do inquilino da resposta. Em que exceção, obter o ID do inquilino a partir do valor de cabeçalho de resposta para **WWW-Authenticate**. Vê esta implementação no [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) método.

## <a name="get-user--app-access-token"></a>Obter o token de acesso de aplicação de utilizador +
A aplicação redireciona o utilizador para o Azure AD com um OAuth 2.0 autorizar pedido - para autenticar as credenciais do utilizador e voltar a um código de autorização. A aplicação utiliza o código de autorização para aceder um token para o Resource Manager. O [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) método cria o pedido de autorização.

Este artigo mostra os pedidos de REST API para autenticar o utilizador. Também pode utilizar bibliotecas de programa auxiliar para efetuar a autenticação no seu código. Para obter mais informações sobre estas bibliotecas, consulte [bibliotecas de autenticação do Azure Active Directory](../active-directory/active-directory-authentication-libraries.md). Para obter orientações sobre como integrar a gestão de identidades numa aplicação, consulte [guia para programadores do Azure Active Directory](../active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Pedido de autenticação (OAuth 2.0)
Emita um abrir ID Connect/OAuth2.0 autorizar o pedido para o ponto final do Azure AD autorizar:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Os parâmetros de cadeia de consulta que estão disponíveis para este pedido descritos o [pedir um código de autorização](../active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) artigo.

O exemplo seguinte mostra como OAuth2.0 autorização do pedido:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD autentica o utilizador e, se necessário, pede ao utilizador para conceder permissão para a aplicação. Devolve o código de autorização para o URL de resposta da aplicação. Consoante o solicitado response_mode, do Azure AD ou envia os dados na cadeia de consulta ou como dados de post.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Pedido de autenticação (Abrir ID Connect)
Se não apenas pretende aceder ao Gestor de recursos do Azure em nome do utilizador, mas também permitir que o utilizador iniciar sessão na sua aplicação utilizando a respetiva conta do Azure AD, emita uma abra ligar autorizar pedidos de ID. Com abrir ID Connect, a aplicação também recebe uma id_token do Azure AD que a aplicação pode utilizar para o utilizador iniciar sessão.

Os parâmetros de cadeia de consulta que estão disponíveis para este pedido descritos o [enviar o pedido de início de sessão](../active-directory/develop/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) artigo.

Um exemplo de pedido abrir ID Connect é:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD autentica o utilizador e, se necessário, pede ao utilizador para conceder permissão para a aplicação. Devolve o código de autorização para o URL de resposta da aplicação. Consoante o solicitado response_mode, do Azure AD ou envia os dados na cadeia de consulta ou como dados de post.

Um exemplo de resposta abrir ID Connect é:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Pedido de token (OAuth2.0 código conceder fluxo)
Agora que a aplicação tiver recebido o código de autorização do Azure AD, está na altura de aceder a token para o Azure Resource Manager.  Publique um OAuth2.0 código conceder Token pedido para ponto final do Token do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros de cadeia de consulta que estão disponíveis para este pedido descritos o [utilizar o código de autorização](../active-directory/develop/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) artigo.

O exemplo seguinte mostra um pedido de token de concessão do código com credenciais de palavra-passe:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Ao trabalhar com as credenciais do certificado, crie um Token Web JSON (JWT) e o início de sessão (RSA SHA256) utilizando a chave privada da credencial de certificado da sua aplicação. Os tipos de afirmação para o token são apresentados na [afirmações token JWT](../active-directory/develop/active-directory-protocols-oauth-code.md#jwt-token-claims). Para referência, consulte o [código da biblioteca de autenticação do Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) para assinar os tokens JWT de asserção de cliente.

Consulte o [abrir ID Connect spec](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para obter detalhes sobre autenticação de cliente.

O exemplo seguinte mostra um pedido de token de concessão do código com credenciais de certificado:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Um exemplo de resposta para o token de concessão do código:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Processar resposta de token de concessão do código
Uma resposta com êxito de token contém (utilizador + aplicação) token de acesso para o Azure Resource Manager. A aplicação utiliza este token de acesso para aceder ao Gestor de recursos em nome do utilizador. A duração de tokens de acesso emitidos pelo Azure AD é uma hora. Não é provável que a aplicação web precisa de renovar (utilizador + aplicação) token de acesso. Se precisar de renovar o token de acesso, utilize o token de atualização que a aplicação recebe na resposta token. Publique um OAuth2.0 Token pedido para ponto final do Token do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros a utilizar com o pedido de atualização são descritos nas [atualizar o token de acesso](../active-directory/develop/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

O exemplo seguinte mostra como utilizar a atualização token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Embora a atualização de tokens pode ser utilizada para obter os tokens de acesso novo para o Azure Resource Manager, não são adequadas para acesso offline pela sua aplicação. A duração de tokens de atualização é limitada e tokens de atualização estão vinculados ao utilizador. Se o utilizador sai da organização, a aplicação utilizando o token de atualização perde o acesso. Esta abordagem não é adequada para as aplicações que são utilizadas pelo equipas para gerir os seus recursos do Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Verifique se o utilizador pode atribuir acesso à subscrição
A aplicação agora tem um token para aceder ao Gestor de recursos do Azure em nome do utilizador. O passo seguinte consiste em ligar a aplicação à subscrição. Depois de ligar, a aplicação pode gerir essas subscrições, mesmo quando o utilizador não está presente (acesso offline de longo prazo).

Para cada subscrição ligar, chame o [permissões do Gestor de recursos de lista](https://docs.microsoft.com/rest/api/authorization/permissions) API para determinar se o utilizador tem direitos de gestão de acesso para a subscrição.

O [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) implementa o método de aplicação de exemplo de ASP.NET MVC esta chamada.

O exemplo seguinte mostra como pedir permissões de um utilizador de uma subscrição. 83cfe939-2402-4581-b761-4f59b0a041e4 é o ID da subscrição.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Um exemplo de resposta para obter as permissões de utilizador na subscrição é:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

As permissões API devolve vários permissões. Cada permissão é composta por ações permitidas (**ações**) e não permitida ações (**notactions**). Se uma ação está presente nas ações permitidas de qualquer permissão e não está presente nas ações não permitidas dessa permissão, o utilizador tem permissão para efetuar essa ação. **Microsoft.Authorization/RoleAssignments/Write** é a ação que concede que acesso de direitos de gestão. A aplicação tem de analisar o resultado de permissões para procurar uma correspondência de regex na cadeia ação no **ações** e **notactions** cada permissão.

## <a name="get-app-only-access-token"></a>Obtenha o token de acesso só de aplicação
Agora, sabe se o utilizador pode atribuir acesso à subscrição do Azure. Os passos seguintes são:

1. Atribua a função RBAC adequada para a identidade da aplicação na subscrição.
2. Valide a atribuição de acesso através da consulta para a permissão da aplicação na subscrição ou ao aceder ao Gestor de recursos com o token de aplicação.
3. Registe a ligação na sua estrutura de dados de aplicações "ligadas subscrições" - a persistência de ID de subscrição.

Vamos ver quanto mais próximo, o primeiro passo. Para atribuir a função RBAC adequada para a identidade da aplicação, tem de determinar:

* O ID de objeto da identidade da aplicação no Azure Active Directory do utilizador
* O identificador da função RBAC pedidas pela aplicação na subscrição

Quando a sua aplicação efetua a autenticação de um utilizador de um Azure AD, cria um objeto principal do serviço para a sua aplicação em que o Azure AD. Azure permite que as funções do RBAC que seja atribuído ao principais de serviço para conceder acesso direto para as aplicações correspondentes nos recursos do Azure. Esta ação é exatamente o que pretende fazer. Consulta a AD Graph API do Azure para determinar o identificador do principal de serviço da sua aplicação ao utilizador com sessão iniciada 's do Azure AD.

Só tem um token de acesso para o Azure Resource Manager - terá um novo token de acesso para chamar a API do Azure AD Graph. Todas as aplicações no Azure AD tem permissão para consultar o seu próprio objeto principal do serviço, forma um token de acesso só de aplicação é suficiente.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obter acesso só de aplicação token para AD Graph API do Azure
Para autenticar a sua aplicação e obter um token para AD Graph API do Azure, emitir um pedido de token de fluxo de OAuth2.0 de concessão de credencial de cliente para ponto final de tokens do AD do Azure (**https://login.microsoftonline.com/ {directory_domain_name} OAuth2/Token**).

O [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) método da aplicação de exemplo de ASP.net MVC obtém acesso só de aplicação token para a Graph API utilizando a biblioteca de autenticação do Active Directory para o .NET.

Os parâmetros de cadeia de consulta que estão disponíveis para este pedido descritos o [pedir um Token de acesso](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) artigo.

Um exemplo de pedido de token de concessão de credencial de cliente:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Um exemplo de resposta para o token de concessão de credencial de cliente:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obtenha o ObjectId de principal de serviço de aplicações no utilizador do Azure AD
Agora, utilize o token de acesso só de aplicação para consultar o [principais de serviço do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API para determinar o ID de objeto do principal de serviço da aplicação no diretório.

O [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) método da aplicação de exemplo de ASP.net MVC implementa esta chamada.

O exemplo seguinte mostra como pedir principal de serviço de uma aplicação. a0448380-c346-4f9f-b897-c18733de9394 é o ID de cliente da aplicação.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

O exemplo seguinte mostra uma resposta ao pedido de serviço de uma aplicação principal

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obter o identificador de funções do RBAC do Azure
Para atribuir a função RBAC adequada ao seu serviço principal, tem de determinar o identificador da função do RBAC do Azure.

A função RBAC adequada para a sua aplicação:

* Se a sua aplicação monitoriza apenas a subscrição, sem efetuar alterações, necessita apenas permissões de leitor na subscrição. Atribuir o **leitor** função.
* Se a subscrição, a criação/modificação/eliminar entidades, gere a sua aplicação Azure requer que um das permissões de contribuinte.
  * Para gerir um determinado tipo de recurso, atribua as funções de contribuinte de recurso específico (contribuinte de Máquina Virtual, contribuinte de rede Virtual, contribuinte de conta de armazenamento, etc.)
  * Para gerir a qualquer tipo de recurso, atribua o **contribuinte** função.

A atribuição de função para a sua aplicação é visível para utilizadores, por isso, selecione o necessário de menor privilégio.

Chamar o [definição de função do Gestor de recursos API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) lista todas as funções do RBAC do Azure e pesquisa, em seguida, iterar o resultado para localizar a definição de função pretendida por nome.

O [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) implementa o método de aplicação de exemplo de ASP.net MVC esta chamada.

O exemplo de pedido seguinte mostra como obter o identificador de funções do RBAC do Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb é o ID da subscrição.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A resposta é o seguinte formato:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Não é necessário chamar esta API numa base contínua. Depois de ter determinado o GUID conhecido da definição de função, pode construir o ID de definição de função como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Seguem-se os identificadores de frequentemente utilizadas funções incorporadas:

| Função | GUID |
| --- | --- |
| Leitor |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Contribuinte |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Contribuinte de Máquina Virtual |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Contribuinte de rede virtual |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Contribuinte de Conta de Armazenamento |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Contribuinte de Web site |de139f84-1756-47ae-9be6-808fbbe84772 |
| Contribuinte de Plano Web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Contribuinte do SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Contribuinte de Base de Dados SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Atribuir funções RBAC para a aplicação
Tem tudo o que precisa para atribuir a função RBAC adequada para o principal de serviço utilizando o [Resource Manager criar a atribuição de função](https://docs.microsoft.com/rest/api/authorization/roleassignments) API.

O [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) implementa o método de aplicação de exemplo de ASP.net MVC esta chamada.

Um exemplo de pedido para atribuir a função RBAC para a aplicação:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

O pedido, são utilizados os seguintes valores:

| GUID | Descrição |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |o ID de subscrição |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |o ID de objeto do principal de serviço da aplicação |
| acdd72a7-3385-48ef-bd42-f606fba81ae7 |o ID da função de leitor |
| 4f87261d-2816-465D-8311-70a27558df4c |um novo guid criado para a nova atribuição de função |

A resposta é o seguinte formato:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obtenha o acesso só de aplicação token para o Azure Resource Manager
Para validar essa aplicação tem o pretendido aceder a subscrição, execute uma tarefa de teste na subscrição utilizando um token de só de aplicação.

Para obter um token de acesso só de aplicação, siga as instruções da secção [obter acesso só de aplicação token para o Azure AD Graph API](#app-azure-ad-graph), com um valor diferente para o parâmetro de recurso:

    https://management.core.windows.net/

O [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) método da aplicação de exemplo de ASP.NET MVC obtém acesso só de aplicação token para o Azure Resource Manager utilizando a biblioteca de autenticação do Active Directory para o .net.

#### <a name="get-applications-permissions-on-subscription"></a>Obter as permissões da aplicação na subscrição
Para verificar se a aplicação tem acesso pretendido numa subscrição do Azure, também pode chamar o [permissões do Gestor de recursos](https://docs.microsoft.com/rest/api/authorization/permissions) API. Esta abordagem é semelhante à como a determinar se o utilizador tem direitos de gestão de acesso para a subscrição. No entanto, neste momento, chame as API de permissões com o token de acesso só de aplicação que recebeu no passo anterior.

O [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) implementa o método de aplicação de exemplo de ASP.NET MVC esta chamada.

## <a name="manage-connected-subscriptions"></a>Gerir subscrições ligadas
Quando a função RBAC adequada é atribuída ao serviço da sua aplicação principal na subscrição, a aplicação pode manter monitorização/geri-lo utilizando tokens de acesso só de aplicação para o Azure Resource Manager.

Se um proprietário da subscrição remove a atribuição de função da sua aplicação utilizar o portal ou ferramentas de linha de comandos, a aplicação já não é possível aceder a essa subscrição. Nesse caso, deverá notificar o utilizador que a ligação com a subscrição foi severed a partir de fora da aplicação e atribuir-lhes uma opção para "reparação" a ligação. "Reparação" seria voltar a criar a atribuição de função que tenha sido eliminada offline.

Tal como ativado o utilizador para ligar a subscrições para a sua aplicação, tem de permitir o utilizador se desligue demasiado subscrições. Um acesso gestão ponto de vista de desligar significa remover a atribuição de função que tenha de principal de serviço da aplicação na subscrição. Opcionalmente, qualquer Estado da aplicação para a subscrição pode ser removido demasiado.
Apenas os utilizadores com permissão de gestão de acesso na subscrição são capazes de desligar a subscrição.

O [RevokeRoleFromServicePrincipalOnSubscription método](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) de ASP.net MVC a aplicação de exemplo implementa esta chamada.

Já está - os utilizadores podem agora facilmente ligar e gerir as suas subscrições do Azure com a sua aplicação.
