---
title: "Loja do Windows Azure AD introdução | Microsoft Docs"
description: "Aplicações da loja do Windows de compilação que integram com o Azure AD para início de sessão e chamar do Azure AD protegido APIs com OAuth."
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6b5189dc06d7f8b0ed4426944948b904feba847e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-with-windows-store-apps"></a>Integrar o Azure AD com aplicações da loja Windows
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Loja Windows 8.1 e projetos de versão anterior não são suportados no Visual Studio 2017.  Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

Se estiver a desenvolver aplicações na loja Windows, o Azure Active Directory (Azure AD) faz com que simples e fácil de autenticar os utilizadores com as respetivas contas do Active Directory. Ao integrar com o Azure AD, uma aplicação em segurança pode consumir quaisquer web API que esteja protegida pelo Azure AD, como as APIs do Office 365 ou a API do Azure.

Aplicações da loja Windows ambiente de trabalho que necessitam de aceder a recursos protegidos, o Azure AD fornece o Active Directory Authentication Library (ADAL). É o único objetivo da ADAL para o tornar mais fácil para a aplicação obter os tokens de acesso. Para demonstrar como é fácil, este artigo mostra como criar uma aplicação da loja Windows de DirectorySearcher que:

* Obtém acesso tokens para chamar a API do Azure AD Graph, utilizando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Procura um diretório para os utilizadores com um nome principal de determinado utilizador (UPN).
* Utilizadores de sinais enviados.

## <a name="before-you-get-started"></a>Antes de começar
* Transferir o [projeto de estrutura](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip), ou transfira a [exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip). Cada transferência é uma solução do Visual Studio 2015.
* Também precisa de um inquilino do Azure AD na qual pretende criar utilizadores e registar a aplicação. Se ainda não tiver um inquilino, [saber como obter um](active-directory-howto-tenant.md).

Quando estiver pronto, siga os procedimentos nas três secções seguintes.

## <a name="step-1-register-the-directorysearcher-app"></a>Passo 1: Registar a aplicação de DirectorySearcher
Para ativar a aplicação obter os tokens, terá primeiro de registá-lo no seu inquilino do Azure AD e conceder permissão para aceder à API do Azure AD Graph. Eis como:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. Em seguida, sob o **diretório** lista, selecione o inquilino do Active Directory onde é necessário registar a aplicação.
3. Clique em **mais serviços** no painel esquerdo e, em seguida, selecione **do Azure Active Directory**.
4. Clique em **registos de aplicação**e, em seguida, selecione **adicionar**.
5. Siga as instruções para criar um **aplicação cliente nativa**.
  * **Nome** descreve a aplicação aos utilizadores.
  * **URI de redirecionamento** é uma combinação de esquema e a cadeia que utiliza o Azure AD para devolver respostas token. Introduza um valor do marcador de posição por agora (por exemplo, **http://DirectorySearcher**). Irá substituir o valor mais tarde.
6. Após concluir o registo, o Azure AD atribui a aplicação um ID de aplicação único. Copie o valor no **aplicação** separador, porque irá precisar dele mais tarde.
7. No **definições** página, selecione **permissões obrigatórias**e, em seguida, selecione **adicionar**.
8. Para o **do Azure Active Directory** aplicação, selecione **Microsoft Graph** como a API.
9. Em **permissões delegadas**, adicione o **aceder ao diretório como o utilizador com sessão iniciada** permissão. Se o fizer, permite que a aplicação consultar a Graph API para os utilizadores.

## <a name="step-2-install-and-configure-adal"></a>Passo 2: Instalar e configurar a ADAL
Agora que tem uma aplicação no Azure AD, pode instalar ADAL e escrever o seu código relacionadas com identidade. Para ativar a ADAL comunicar com o Azure AD, conceda-lhe algumas informações sobre o registo de aplicação.

1. Adicione ADAL para o projeto de DirectorySearcher utilizando a consola do Gestor de pacotes.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. No projeto DirectorySearcher, abra MainPage.xaml.cs.
3. Substitua os valores no **os valores de configuração** região com os valores que introduziu no portal do Azure. Seu código se refere a estes valores, sempre que utilizar a ADAL.
  * O *inquilino* é o domínio de inquilino do Azure AD (por exemplo, contoso.onmicrosoft.com).
  * O *clientId* é o ID de cliente da aplicação e que copiou do portal.
4. Agora tem de detetar a chamada de retorno de URI para a sua aplicação da loja Windows. Definir um ponto de interrupção nesta linha no `MainPage` método:
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. Compilar a solução, certificando-se de que todas as referências do pacote são restaurados. Se quaisquer pacotes estão em falta, abra o Gestor de pacotes NuGet e restaurá-las.
6. Execute a aplicação e copie o valor da `redirectUri` quando é atingido o ponto de interrupção. O valor deve ter um aspeto semelhante a seguinte:

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. Reverter o **definições** separador da aplicação no portal do Azure, adicione um **RedirectUri** com o valor anterior.  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Passo 3: Utilizar a ADAL para obter os tokens do Azure AD
O princípio básico atrás ADAL é que sempre que a aplicação tem um token de acesso, basta chama `authContext.AcquireToken(…)`, e o resto efetua a ADAL.  

1. Inicializar a aplicação `AuthenticationContext`, que é a classe principal da ADAL. Esta ação transfere a ADAL as coordenadas que necessita para comunicar com o Azure AD e informe-lo como colocar em cache de tokens.

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. Localize o `Search(...)` método, o que é invocado quando os utilizadores clicarem o **pesquisa** botão na IU da aplicação. Este método envia um pedido de get para a Azure AD Graph API a consulta para os utilizadores cujo UPN começa com o termo de pesquisa especificada. Para consultar a Graph API, incluir um token de acesso do pedido **autorização** cabeçalho. Este é onde ADAL é apresentada no.

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    Quando a aplicação solicita um token ao chamar `AcquireTokenAsync(...)`, ADAL tenta devolver um token sem pedir ao utilizador as credenciais. Se ADAL determina que o utilizador tem de iniciar sessão para obter um token, apresenta uma caixa de diálogo de início de sessão, recolhe as credenciais do utilizador e devolve um token após a autenticação é concluída com êxito. Se a ADAL não é possível devolver um token por qualquer motivo, o *AuthenticationResult* estado é um erro.
3. Agora está na altura de utilizar o token de acesso que acabou de adquirir. Também no `Search(...)` método, anexe o token para o pedido de get Graph API no **autorização** cabeçalho:

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. Pode utilizar o `AuthenticationResult` objetos para apresentar informações sobre o utilizador na aplicação, tais como o ID do utilizador:

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. Também pode utilizar a ADAL para a sessão dos utilizadores fora da aplicação. Quando o utilizador clica o **terminar sessão** botão, certifique-se de que a chamada seguinte `AcquireTokenAsync(...)` mostra uma vista de início de sessão. Com a ADAL, esta ação é tão fácil como limpar a cache de token:

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>Passos seguintes
Agora tem uma aplicação da loja Windows que pode autenticar os utilizadores, em segurança chamar as APIs de utilização de OAuth 2.0 web e obter informações básicas sobre o utilizador.

Se ainda não já preenchidos do inquilino com utilizadores, agora é o tempo para o fazer.
1. Executar a aplicação de DirectorySearcher e, em seguida, inicie sessão com um dos utilizadores.
2. Procurar outros utilizadores com base no respetivo UPN.
3. Feche a aplicação e execute novamente o mesmo. Repare como a sessão do utilizador permanece intacta.
4. Terminar sessão clicando para apresentar a barra inferior e, em seguida, inicie sessão novamente como outro utilizador.

ADAL torna mais fácil incorporar todas estas funcionalidades comuns da identidade da aplicação. -Encarrega-se de todo o trabalho de dirty para si, tais como a gestão da cache, suporte de protocolo de OAuth, que apresenta um início de sessão IU, o utilizador e a atualização expirou tokens. É necessário saber apenas uma única chamada API, `authContext.AcquireToken*(…)`.

Para referência, transfira o [exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip) (sem os valores de configuração).

Agora pode passar para os cenários de identidade adicionais. Por exemplo, experimente [proteger uma API Web do .NET com o Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
