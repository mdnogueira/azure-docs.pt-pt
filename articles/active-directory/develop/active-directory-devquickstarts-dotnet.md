---
title: "Azure AD .NET, introdução | Microsoft Docs"
description: "Como criar uma aplicação de ambiente de trabalho de Windows de .NET que se integra com o Azure AD para início de sessão e as chamadas do Azure AD protegido APIs com OAuth."
services: active-directory
documentationcenter: .net
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7a252e0e5243c7b7489373845531cb913ca1f6aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Integrar o Azure AD para uma aplicação de ambiente de trabalho WPF do Windows
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Se estiver a desenvolver uma aplicação de ambiente de trabalho, do Azure AD torna simples e fácil para si autenticar os utilizadores com as respetivas contas do Active Directory.  Também permite que a aplicação de forma segura consumir qualquer API web protegida pelo Azure AD, como as APIs do Office 365 ou a API do Azure.

Para .NET nativos clientes que necessitam de aceder a recursos protegidos, o Azure AD fornece a biblioteca de autenticação do Active Directory ou a ADAL.  Objetivo único da ADAL no vida é facilitar a sua aplicação obter os tokens de acesso.  Para demonstrar tal como é fácil, aqui iremos irá criar uma aplicação de lista de tarefas do WPF .NET que:

* Obtém acesso tokens para chamar o Azure AD Graph API utilizando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Procura um diretório para os utilizadores com um alias especificado.
* Utilizadores de sinais enviados.

Para criar a aplicação de trabalho completo, terá:

1. Registe a sua aplicação com o Azure AD.
2. Instalar e configurar a ADAL.
3. Utilize a ADAL para obter os tokens do Azure AD.

Para começar, [transferir a estrutura de aplicação](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) ou [transferir o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Também terá um inquilino do Azure AD, onde pode criar utilizadores e registar uma aplicação.  Se ainda não tiver um inquilino, [saber como obter um](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Registar a aplicação de DirectorySearcher
Para ativar a sua aplicação obter os tokens, primeiro tem de registá-lo no seu inquilino do Azure AD e conceder permissão para aceder à API do Azure AD Graph:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique na sua conta e, no **diretório** lista, escolha o inquilino do Active Directory onde pretende registar a sua aplicação.
3. Clique em **mais serviços** na navegação esquerda e escolha **do Azure Active Directory**.
4. Clique em **registos de aplicação** e escolha **adicionar**.
5. Siga as instruções e crie um novo **aplicação cliente nativa**.
  * O **nome** da aplicação irá descrever a aplicação aos utilizadores finais
  * O **Uri de redirecionamento** é uma combinação de esquema e a cadeia do Azure AD irá utilizar para devolver respostas token.  Introduza um valor específico da aplicação, por exemplo, `http://DirectorySearcher`.
6. Depois de concluir o registo, AAD atribuirá a aplicação um ID de aplicação único.  Irá precisar deste valor nas secções seguintes, por isso, copie-o partir da página da aplicação.
7. Do **definições** página, escolha **permissões obrigatórias** e escolha **adicionar**. Selecione o **Microsoft Graph** como a API e adicione o **ler dados de diretório** permissão em **permissões delegadas**.  Isto irá ativar a sua aplicação consultar a Graph API para os utilizadores.

## <a name="2-install--configure-adal"></a>2. Instalar e configurar a ADAL
Agora que tem uma aplicação no Azure AD, pode instalar ADAL e escrever o seu código relacionadas com identidade.  Da ADAL conseguir comunicar com o Azure AD, terá de fornecê-lo com algumas informações sobre o registo da aplicação.

* Comece por adicionar ADAL para o projeto de DirectorySearcher utilizando a consola do Gestor de pacotes.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* No projeto DirectorySearcher, abra `app.config`.  Substitua os valores de elementos no `<appSettings>` secção para refletir os valores de entrada no Portal do Azure.  O código será referenciar estes valores, sempre que utilizar a ADAL.
  * O `ida:Tenant` é o domínio de inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com
  * O `ida:ClientId` é o ID de cliente da aplicação que copiou do portal.
  * O `ida:RedirectUri` é o redirecionamento url registado no portal.

## <a name="3----use-adal-to-get-tokens-from-aad"></a>3.    Utilizar a ADAL para obter os Tokens do AAD
O princípio básico atrás ADAL é que sempre que a aplicação tem um token de acesso, basta chama `authContext.AcquireTokenAsync(...)`, e o resto efetua a ADAL.  

* No `DirectorySearcher` projeto, abra `MainWindow.xaml.cs` e localize o `MainWindow()` método.  O primeiro passo é ao inicializar a sua aplicação `AuthenticationContext` -ADAL da classe principal.  Este é onde passa ADAL as coordenadas que necessita para comunicar com o Azure AD e informe-lo como colocar em cache de tokens.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Localizar agora o `Search(...)` método, o que irá ser invocado quando os utilizador cliks "Procurar" botão na IU da aplicação.  Este método envia um pedido GET para a Azure AD Graph API a consulta para os utilizadores cujo UPN começa com o termo de pesquisa especificada.  Mas para consultar a Graph API, terá de incluir um access_token no `Authorization` cabeçalho do pedido - esta é onde ADAL é apresentada no.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
* Quando a aplicação solicita um token ao chamar `AcquireTokenAsync(...)`, ADAL tentará devolveu um token sem pedir ao utilizador as credenciais.  Se ADAL determina que o utilizador tem de iniciar sessão para obter um token, este irá apresentar uma caixa de diálogo de início de sessão, recolher as credenciais do utilizador e devolveu um token após a autenticação com êxito.  Se a ADAL não é possível devolver um token por qualquer motivo, irá gerar um `AdalException`.
* Tenha em atenção que o `AuthenticationResult` objeto contém uma `UserInfo` objeto que pode ser utilizado para recolher informações poderá ter a sua aplicação.  No DirectorySearcher, `UserInfo` é utilizado para personalizar a IU da aplicação com o id do utilizador.
* Quando o utilizador clica no botão "Terminar sessão", queremos assegurar que a chamada seguinte `AcquireTokenAsync(...)` pedirá ao utilizador para iniciar sessão.  Com a ADAL, este é tão fácil como limpar a cache de token:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* No entanto, se o utilizador não clique no botão "Terminar sessão", irá pretender manter a sessão do utilizador para a próxima vez que executam o DirectorySearcher.  Quando inicia a aplicação, pode verificar a cache de token da ADAL para um token existente e atualize a IU em conformidade.  No `CheckForCachedToken()` método, efetuar outra chamada para `AcquireTokenAsync(...)`, desta vez transmitir no `PromptBehavior.Never` parâmetro.  `PromptBehavior.Never`informará o ADAL que o utilizador não deve ser pedido para iniciar sessão e a ADAL em vez disso, deve acionar uma excepção, se for não é possível devolver um token.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Parabéns! Agora tem um trabalho de aplicações de .NET WPF que tenham a capacidade de autenticar os utilizadores, de forma segura chamar APIs da Web através de OAuth 2.0 e obter informações básicas sobre o utilizador.  Se ainda não o fez, agora é o tempo para preencher o seu inquilino com alguns utilizadores.  Executar a aplicação de DirectorySearcher e inicie sessão com uma esses utilizadores.  Procurar outros utilizadores com base no respetivo UPN.  Feche a aplicação e execute-a novamente.  Repare como a sessão do utilizador permanece intacta.  Terminar sessão e inicie sessão novamente como outro utilizador.

ADAL torna mais fácil incorporar todas estas funcionalidades de identidade comuns na sua aplicação.  Encarrega de todo o trabalho de dirty para si - gestão de cache, o suporte de protocolo de OAuth, apresentando o utilizador com um início de sessão IU, atualizar tokens expirados e muito mais.  É tudo o que realmente precisa de saber uma única chamada API, `authContext.AcquireTokenAsync(...)`.

Para referência, o exemplo concluído (sem os valores de configuração) é fornecido [aqui](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Agora pode passar para cenários adicionais.  Poderá querer experimentar:

[Proteger uma API com o Azure AD de Web do .NET >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

