---
title: "Telefone do Azure AD Windows introdução | Microsoft Docs"
description: "Como criar uma aplicação do Windows Phone que se integra com o Azure AD para início de sessão e as chamadas do Azure AD protegido APIs com OAuth."
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 66f5ac20-5e1f-4b9d-bb99-9b3305e26416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 03c4b6d225dce99d79ef6c1ba2af43af8dea3eae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-with-a-windows-phone-app"></a>Integrar o Azure AD com uma aplicação do Windows Phone
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> O Visual Studio 2017 não suporta projetos do Windows Phone 8.1 e de versões anteriores.  Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

Se estiver a desenvolver uma aplicação do Windows Phone 8.1, do Azure AD torna simples e fácil para si autenticar os utilizadores com as respetivas contas do Active Directory.  Também permite que a aplicação de forma segura consumir qualquer API web protegida pelo Azure AD, como as APIs do Office 365 ou a API do Azure.

> [!NOTE]
> Código de exemplo utiliza a ADAL v 2.0.  Para a tecnologia mais recente, poderá querer em vez disso, experimente a nossa [Tutorial Universal do Windows com o ADAL v 3.0](active-directory-devquickstarts-windowsstore.md).  Se, de facto, que está a criar uma aplicação para Windows Phone 8.1, este é o local certo.  ADAL v 2.0 é ainda totalmente suportado e é a forma recomendada de desenvolvimento de aplicações agianst Windows Phone 8.1 com o Azure AD.
> 
> 

Para .NET nativos clientes que necessitam de aceder a recursos protegidos, o Azure AD fornece a biblioteca de autenticação do Active Directory ou a ADAL.  Objetivo único da ADAL no vida é facilitar a sua aplicação obter os tokens de acesso.  Para demonstrar tal como é fácil, aqui iremos irá criar uma aplicação do Windows Phone 8.1 "diretório Searcher" que:

* Obtém acesso tokens para chamar o Azure AD Graph API utilizando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Procura um diretório para os utilizadores com um UPN indicado.
* Utilizadores de sinais enviados.

Para criar a aplicação de trabalho completo, terá:

1. Registe a sua aplicação com o Azure AD.
2. Instalar e configurar a ADAL.
3. Utilize a ADAL para obter os tokens do Azure AD.

Para começar, [transferir um projeto de estrutura](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) ou [transferir o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Cada uma é uma solução do Visual Studio 2013.  Também terá um inquilino do Azure AD, onde pode criar utilizadores e registar uma aplicação.  Se ainda não tiver um inquilino, [saber como obter um](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>1. Registar a aplicação de Searcher de diretório
Para ativar a sua aplicação obter os tokens, primeiro tem de registá-lo no seu inquilino do Azure AD e conceder permissão para aceder à API do Azure AD Graph:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique na sua conta e, no **diretório** lista, escolha o inquilino do Active Directory onde pretende registar a sua aplicação.
3. Clique em **mais serviços** na navegação esquerda e escolha **do Azure Active Directory**.
4. Clique em **registos de aplicação** e escolha **adicionar**.
5. Siga as instruções e crie um novo **aplicação cliente nativa**.
  * O **nome** da aplicação irá descrever a aplicação aos utilizadores finais
  * O **Uri de redirecionamento** é uma combinação de esquema e a cadeia do Azure AD irá utilizar para devolver respostas token.  Introduza um valor do marcador de posição para agora, por exemplo, `http://DirectorySearcher`.  Iremos irá substituir este valor mais tarde.
6. Depois de concluir o registo, AAD atribuirá a aplicação um ID de aplicação único.  Irá precisar deste valor nas secções seguintes, por isso, copie-o de separador da aplicação.
7. Do **definições** página, escolha **permissões obrigatórias** e escolha **adicionar**. Selecione o **Microsoft Graph** como a API e adicione o **ler dados de diretório** permissão em **permissões delegadas**.  Isto irá ativar a sua aplicação consultar a Graph API para os utilizadores.

## <a name="2-install--configure-adal"></a>2. Instalar e configurar a ADAL
Agora que tem uma aplicação no Azure AD, pode instalar ADAL e escrever o seu código relacionadas com identidade.  Da ADAL conseguir comunicar com o Azure AD, terá de fornecê-lo com algumas informações sobre o registo da aplicação.

* Comece por adicionar ADAL para o projeto de DirectorySearcher utilizando a consola do Gestor de pacotes.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* No projeto DirectorySearcher, abra `MainPage.xaml.cs`.  Substitua os valores no `Config Values` região para refletir os valores de entrada no Portal do Azure.  O código será referenciar estes valores, sempre que utilizar a ADAL.
  * O `tenant` é o domínio de inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com
  * O `clientId` é o ID de cliente da aplicação que copiou do portal.
* Agora tem de detetar o uri de chamada de retorno para a sua aplicação do Windows Phone.  Definir um ponto de interrupção nesta linha no `MainPage` método:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
* Execute a aplicação e copie reservados o valor da `redirectUri` quando é atingido o ponto de interrupção.  Deve ser algo semelhante ao seguinte

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

* Reverter o **configurar** separador da sua aplicação no Portal de gestão do Azure, substitua o valor da **RedirectUri** com este valor.  

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Utilizar a ADAL para obter os Tokens do AAD
O princípio básico atrás ADAL é que sempre que a aplicação tem um token de acesso, basta chama `authContext.AcquireToken(…)`, e o resto efetua a ADAL.  

* O primeiro passo é ao inicializar a sua aplicação `AuthenticationContext` -ADAL da classe principal.  Este é onde passa ADAL as coordenadas que necessita para comunicar com o Azure AD e informe-lo como colocar em cache de tokens.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

* Localizar agora o `Search(...)` método, o que irá ser invocado quando os utilizador cliks "Procurar" botão na IU da aplicação.  Este método envia um pedido GET para a Azure AD Graph API a consulta para os utilizadores cujo UPN começa com o termo de pesquisa especificada.  Mas para consultar a Graph API, terá de incluir um access_token no `Authorization` cabeçalho do pedido - esta é onde ADAL é apresentada no.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
* Se a autenticação interativa, é necessária, ADAL irá utilizar o Mediador de autenticação de Web do Windows Phone (WAB) e [modelo de continuação](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) para apresentar o início de sessão do AD do Azure na página.  Quando o utilizador inicia sessão, a aplicação tem de passar ADAL os resultados da interação WAB.  Isto é tão simple como implementar o `ContinueWebAuthentication` interface:

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

* Agora está na altura de utilizar o `AuthenticationResult` que devolvido ADAL para a sua aplicação.  No `QueryGraph(...)` chamada de retorno, anexar access_token obteve para o pedido GET no cabeçalho de autorização:

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
* Também pode utilizar o `AuthenticationResult` objetos para apresentar informações sobre o utilizador na sua aplicação. No `QueryGraph(...)` método, utilize o resultado para mostrar o id do utilizador na página:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
* Por fim, pode utilizar a ADAL para assinar o utilizador fora bem a aplicação.  Quando o utilizador clica no botão "Terminar sessão", queremos assegurar que a chamada seguinte `AcquireTokenSilentAsync(...)` irá falhar.  Com a ADAL, este é tão fácil como limpar a cache de token:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Parabéns! Agora de ter uma aplicação do Windows Phone que tenha a capacidade de autenticar os utilizadores, de forma segura chamar APIs da Web através de OAuth 2.0 e obter informações básicas sobre o utilizador.  Se ainda não o fez, agora é o tempo para preencher o seu inquilino com alguns utilizadores.  Executar a aplicação de DirectorySearcher e inicie sessão com uma esses utilizadores.  Procurar outros utilizadores com base no respetivo UPN.  Feche a aplicação e execute-a novamente.  Repare como a sessão do utilizador permanece intacta.  Terminar sessão e inicie sessão novamente como outro utilizador.

ADAL torna mais fácil incorporar todas estas funcionalidades de identidade comuns na sua aplicação.  Encarrega de todo o trabalho de dirty para si - gestão de cache, o suporte de protocolo de OAuth, apresentando o utilizador com um início de sessão IU, atualizar tokens expirados e muito mais.  É tudo o que realmente precisa de saber uma única chamada API, `authContext.AcquireToken*(…)`.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Agora pode passar para os cenários de identidade adicionais.  Poderá querer experimentar:

[Proteger uma API com o Azure AD de Web do .NET >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

