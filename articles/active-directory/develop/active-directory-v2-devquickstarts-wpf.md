---
title: "Aplicação nativa do .NET de v 2.0 do Azure Active Directory | Microsoft Docs"
description: "Como criar uma aplicação nativa do .NET que assina os utilizadores com ambos os Account pessoal do Microsoft e contas profissionais ou escolares."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7389f55ee6fef9548abb0ca4ac1bbd0399868d47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Adicionar início de sessão a uma aplicação de ambiente de trabalho do Windows
Com o ponto final v 2.0, pode adicionar rapidamente a autenticação para as aplicações de ambiente de trabalho com suporte para ambas as contas pessoais da Microsoft e contas profissionais ou escolares.  Também permite que a aplicação comunicar de forma segura com um back-end web api, bem como [Microsoft Graph](https://graph.microsoft.io) e alguns do [Unified APIs do Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [!NOTE]
> Nem todos os cenários do Azure Active Directory (AD) e funcionalidades são suportadas pelo ponto final v 2.0.  Para determinar se deve utilizar o ponto final v 2.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

Para [aplicações nativas de .NET que são executadas num dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), o Azure AD fornece a biblioteca de autenticação do Microsoft Identity ou MSAL.  Objetivo único do MSAL na vida é facilitar a sua aplicação obter os tokens para chamar serviços web.  Para demonstrar tal como é fácil, aqui iremos irá criar uma aplicação de lista de tarefas do WPF .NET que:

* Inicia o utilizador & obtém os tokens de acesso a [protocolo de autenticação OAuth 2.0](active-directory-v2-protocols.md).
* Chamadas de forma segura um serviço web de lista de tarefas, que também está protegido por OAuth 2.0 de back-end.
* O utilizador termina a sessão.

## <a name="download-sample-code"></a>Transferir o código de exemplo
O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Para acompanhar, pode [transferir a estrutura da aplicação como um. zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou clonar a estrutura:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

A aplicação concluída é fornecida no final deste tutorial bem.

## <a name="register-an-app"></a>Registar uma aplicação
Criar uma nova aplicação em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou siga estas [detalhadas passos](active-directory-v2-app-registration.md).  Certifique-se de que:

* Copie o **Id da aplicação** atribuída à aplicação, irá precisar das mesmas em breve.
* Adicionar o **Mobile** plataforma para a sua aplicação.

## <a name="install--configure-msal"></a>Instalar e configurar MSAL
Agora que tem uma aplicação, registada com a Microsoft, pode instalar MSAL e escrever o seu código relacionadas com identidade.  Para MSAL conseguir comunicar o ponto final v 2.0, terá de fornecê-lo com algumas informações sobre o registo da aplicação.

* Comece por adicionar MSAL ao projeto TodoListClient utilizando a consola do Gestor de pacotes.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* No projeto TodoListClient, abra `app.config`.  Substitua os valores de elementos no `<appSettings>` secção para refletir os valores de entrada para o portal de registo de aplicação.  O código será referenciar estes valores, sempre que utiliza MSAL.
  
  * O `ida:ClientId` é o **Id da aplicação** da sua aplicação que copiou do portal.
* No projeto TodoList-Service, abra `web.config` na raiz do projeto.  
  
  * Substitua o `ida:Audience` valor com o mesmo **Id da aplicação** do portal.

## <a name="use-msal-to-get-tokens"></a>Utilizar MSAL para obter os tokens
O princípio básico atrás MSAL é que sempre que a aplicação tem um token de acesso, basta chamar `app.AcquireToken(...)`, e MSAL faz o resto.  

* No `TodoListClient` projeto, abra `MainWindow.xaml.cs` e localize o `OnInitialized(...)` método.  O primeiro passo é ao inicializar a sua aplicação `PublicClientApplication` -classe principal do MSAL que representam aplicações nativas.  Este é onde passar MSAL as coordenadas que necessita para comunicar com o Azure AD e informe-lo como colocar em cache de tokens.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* Quando a aplicação é iniciado, queremos de verificação e veja se o utilizador já tem sessão iniciada na aplicação.  No entanto, não queremos invocar uma IU de início de sessão ainda - podemos irá tornar o utilizador, clique em "Iniciar sessão" para o fazer.  Também no `OnInitialized(...)` método:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* Se o utilizador não tem sessão iniciado e clicar no botão "Início de sessão em", queremos invocar um início de sessão da IU e que o utilizador introduza as suas credenciais.  Processador de botão de implementar o início de sessão:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* Se o utilizador com êxito inicia sessão, MSAL irá receber e um token da cache por si e para poder continuar para chamar o `GetTodoList()` método com confiança.  Tudo o que for deixado ao obter tarefas de um utilizador é implementar o `GetTodoList()` método.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Executar
Parabéns! Tem agora uma aplicação .NET WPF de trabalho que tenha a capacidade de autenticar os utilizadores & chamar com segurança APIs da Web através de OAuth 2.0.  Execute os ambos os projetos e inicie sessão com uma conta Microsoft pessoal ou uma conta escolar ou profissional.  Adicione tarefas à lista de tarefas desse utilizador.  Terminar sessão e inicie sessão novamente como outro utilizador para ver a respetiva lista de tarefas.  Feche a aplicação e execute-a novamente.  Repare como a sessão do utilizador permanece intacta - Isto acontece porque a aplicação coloca em cache tokens num ficheiro local.

MSAL torna mais fácil incorporar funcionalidades de identidade comuns na sua aplicação, utilizando as contas pessoais e profissionais.  Encarrega de todo o trabalho de dirty para si - gestão de cache, o suporte de protocolo de OAuth, apresentando o utilizador com um início de sessão IU, atualizar tokens expirados e muito mais.  É tudo o que realmente precisa de saber uma única chamada API, `app.AcquireTokenAsync(...)`.

Para referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um. zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), ou pode cloná-la a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Passos seguintes
Agora pode mover para tópicos mais avançados.  Poderá querer experimentar:

* [Proteger a API Web de TodoListService com o ponto final v 2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Para obter recursos adicionais, consulte:  

* [O guia para programadores de v 2.0 >>](active-directory-appmodel-v2-overview.md)
* [Etiqueta de "msal" StackOverflow >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança dos nossos produtos
Aconselhamo-lo a obter notificações de quando os incidentes de segurança ocorrem, visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrevendo os alertas de aviso de segurança.

