---
title: B2C do Azure Active Directory | Microsoft Docs
description: "Como criar uma aplicação de ambiente de trabalho do Windows que inclui o início de sessão, inscrição, e gestão de perfis utilizando o Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9da14362-8216-4485-960e-af17cd5ba3bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.openlocfilehash: 8e2b5c704230ee2ba1395dc76a1551aaa8e7af7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>O Azure AD B2C: Criar uma aplicação de ambiente de trabalho do Windows
Ao utilizar o Azure Active Directory (Azure AD) B2C, pode adicionar as funcionalidades de gestão de identidade poderosas self-service para a sua aplicação de ambiente de trabalho em poucos passos. Este artigo irá mostrar como criar uma aplicação de "lista de tarefas".NET Windows Presentation Foundation (WPF) que inclui a inscrição, início de sessão de utilizador e gestão de perfis. A aplicação irá incluir suporte para inscrição e o início de sessão utilizando um nome de utilizador ou o e-mail. Irá também inclui suporte para inscrição e o início de sessão através da utilização de contas de redes sociais como o Facebook e Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C
Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino.  Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## <a name="create-an-application"></a>Criar uma aplicação
Em seguida, precisa de criar uma aplicação no diretório do B2C. Isto proporciona ao Azure AD as informações de que necessita para comunicar de forma segura com a sua aplicação. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md).  É necessário:

* Incluir um **cliente nativo** na aplicação.
* Copiar o **URI de redirecionamento** `urn:ietf:wg:oauth:2.0:oob`. É o URL predefinido para este exemplo de código.
* Copiar a **ID da Aplicação** atribuída à aplicação. Precisará dele mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as políticas
No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade: inscrever-se, iniciar sessão e Editar perfil. Tem de criar uma política para cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Quando cria as três políticas, certifique-se de que:

* Escolher a **Inscrição de ID de utilizador** ou **Inscrição de email** no painel de fornecedores de identidade.
* Escolher o **Nome a apresentar** e outros atributos de inscrição na sua política de inscrição.
* Escolher as afirmações **Nome a apresentar** e **ID de objeto** como afirmações de aplicação em cada política. Também pode escolher outras afirmações.
* Copiar o **Nome** de cada política depois de a criar. Deve ter o prefixo `b2c_1_`.  Precisará destes nomes de políticas mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, está pronto para criar a sua aplicação.

## <a name="download-the-code"></a>Transferir o código
O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para criar o exemplo à medida que avança, pode [transferir o projeto de estrutura como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

A aplicação concluída está também [disponível como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou no `complete` ramo do mesmo repositório.

Depois de transferir o código de exemplo, abra o ficheiro de .sln do Visual Studio para começar a utilizar. O `TaskClient` projeto é a aplicação de ambiente de trabalho do WPF que o utilizador interage com. Para efeitos deste tutorial, aquele invoca uma API, alojado no Azure, que armazena a lista de tarefas de cada utilizador da web de back-end tarefas.  Não é necessário criar a API web, já que poderemos ter funcionar para si.

Para saber como uma API web em segurança autentica o pedidos utilizando o Azure AD B2C, veja o [web API introdução artigo](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Executar políticas
A aplicação comunica com o Azure AD B2C através do envio de mensagens de autenticação que especificar a política que pretende para ser executado como parte do pedido de HTTP. Para aplicações de ambiente de trabalho de .NET, pode utilizar a biblioteca de autenticação da Microsoft (MSAL) de pré-visualização para enviar mensagens de autenticação OAuth 2.0, execute as políticas e obter tokens chamar as APIs web.

### <a name="install-msal"></a>Instalar MSAL
Adicionar MSAL para o `TaskClient` projeto utilizando a consola do Gestor de pacotes do Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Introduza os seus detalhes do B2C
Abra o ficheiro `Globals.cs` e substitua cada um dos valores de propriedade com os seus próprios. Esta classe é utilizada ao longo `TaskClient` para valores de referência utilizada frequentemente.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>Criar o PublicClientApplication
A classe principal da MSAL `PublicClientApplication`. Esta classe representa a sua aplicação no sistema do Azure AD B2C. Quando app initalizes, crie uma instância de `PublicClientApplication` no `MainWindow.xaml.cs`. Isto pode ser utilizado em toda a janela.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Iniciar um fluxo de inscrição
Quando um utilizador optar por participar ativamente para sinais cópias de segurança, que pretende iniciar um fluxo de inscrição que utiliza a política de inscrição que criou. Ao utilizar MSAL, apenas a chamar `pca.AcquireTokenAsync(...)`. Os parâmetros a transmitir ao `AcquireTokenAsync(...)` determinar qual token receber, a política utilizada no pedido de autenticação e muito mais.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Iniciar um fluxo de início de sessão
Pode iniciar um fluxo de início de sessão da mesma forma que o se iniciar um fluxo de inscrição. Quando um utilizador inicia sessão, efetue a mesma chamada para MSAL, desta vez utilizando a política de início de sessão:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Iniciar um fluxo de edição de perfil
Novamente, pode executar uma política de perfil de editar o mesmo modo:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Todas as nestes casos, MSAL está devolve um token no `AuthenticationResult` ou emite uma exceção. Sempre que a obter um token de MSAL, pode utilizar o `AuthenticationResult.User` objeto para atualizar os dados de utilizador na aplicação, tais como a IU. ADAL também coloca em cache o token para utilização em outras partes da aplicação.

### <a name="check-for-tokens-on-app-start"></a>Verifique a existência de tokens no início da aplicação
Também pode utilizar MSAL para controlar o estado de início de sessão do utilizador.  Nesta aplicação, queremos permanecer com sessão iniciada, mesmo depois de serem feche a aplicação e volte a abrir o utilizador.  Novamente dentro de `OnInitialized` substituição, utilize do MSAL `AcquireTokenSilent` tokens em cache de método para procurar:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Chamar a API de tarefa
Agora, utilizou MSAL para executar as políticas e obter tokens.  Quando pretender utilizar um estes tokens para chamar a API de tarefa, pode utilizar novamente do MSAL `AcquireTokenSilent` tokens em cache de método para procurar:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Quando a chamada para `AcquireTokenSilentAsync(...)` for bem sucedida e um token for encontrado na cache, pode adicionar o token para o `Authorization` cabeçalho do pedido HTTP. A API web de tarefas irá utilizar este cabeçalho para autenticar o pedido ler a lista de tarefas do utilizador:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>O utilizador terminar sessão
Por fim, pode utilizar MSAL para terminar uma sessão de utilizador com a aplicação quando o utilizador seleciona **terminar sessão**.  Quando utilizar MSAL, isto é conseguido ao desmarcar todos os tokens da cache de token:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo
Por fim, crie e execute o exemplo.  Inscreva-se a aplicação utilizando um nome de utilizador ou endereço de correio eletrónico. Termine sessão e inicie sessão novamente como o mesmo utilizador. Edite perfil desse utilizador. Termine sessão e inscrever-se através de um utilizador diferente.

## <a name="add-social-idps"></a>Adicionar IDPs redes sociais
Atualmente, a aplicação suporta apenas inscrição de utilizador e o início de sessão que utilizam **contas locais**. Estas são contas armazenadas no diretório do B2C que utilizam um nome de utilizador e palavra-passe. Ao utilizar o Azure AD B2C, pode adicionar suporte para outros fornecedores de identidade (IDPs) sem alterar qualquer do seu código.

Para adicionar redes sociais IDPs à sua aplicação, comece por seguir as instruções detalhadas nestes artigos. Para cada IDP que pretende suportar, tem de registar uma aplicação nesse sistema e obter um ID de cliente.

* [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
* [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
* [Configurar o Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
* [Configurar LinkedIn como um IDP](active-directory-b2c-setup-li-app.md)

Depois de adicionar os fornecedores de identidade para o seu diretório do B2C, tem de editar cada um dos seus três políticas para incluir IDPs novo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de guardar as suas políticas, execute novamente a aplicação. Deverá ver os novo IDPs adicionados como início de sessão e as experiências de opções de inscrição em cada de identidade.

Pode experimentar com as políticas e observar os efeitos na sua aplicação de exemplo. Adicionar ou remover IDPs, manipular afirmações de aplicação ou altere os atributos de inscrição. Experimentação até que pode ver como MSAL, pedidos de autenticação e políticas associar em conjunto.

Para referência, o exemplo concluído [é fornecido como um ficheiro. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Também pode clonar a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
