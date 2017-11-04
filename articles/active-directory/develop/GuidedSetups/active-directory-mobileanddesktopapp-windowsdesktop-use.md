---
title: "Azure AD v2 Windows ambiente de trabalho começar - utilize | Microsoft Docs"
description: "Como aplicações de .NET de ambiente de trabalho do Windows (XAML) podem chamar uma API que necessitam de tokens de acesso ao ponto final do Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 826ba0a00b26993d4f37f0a8ce587d7bb77e7eb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utilizar a biblioteca de autenticação da Microsoft (MSAL) para obter um token para a Microsoft Graph API

Esta secção mostra como utilizar MSAL para obter um token a Microsoft Graph API.

1.  No `MainWindow.xaml.cs`, adicione a referência da biblioteca MSAL à classe:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Substitua <code>MainWindow</code> classe código com:
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
            }
            catch (MsalException msalex)
            {
                ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
            }
        }
        catch (Exception ex)
        {
            ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
            return;
        }

        if (authResult != null)
        {
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Mais Informações
#### <a name="getting-a-user-token-interactive"></a>Obter um token de utilizador interativa
Chamar o `AcquireTokenAsync` método resulta numa janela de pedir ao utilizador para iniciar sessão. Aplicações normalmente necessitam de um utilizador iniciar sessão interativamente na primeira vez que necessitam para aceder a recursos protegidos, ou quando uma operação automática ao adquirir um token falhar (por exemplo, palavra-passe do utilizador expirou).

#### <a name="getting-a-user-token-silently"></a>A obter um utilizador token automaticamente
`AcquireTokenSilentAsync`processa aquisições token e a renovação sem qualquer interação do utilizador. Depois de `AcquireTokenAsync` é executado pela primeira vez, `AcquireTokenSilentAsync` método é a habitual utilizado para obter os tokens utilizados para aceder a recursos protegidos para chamadas subsequentes - como as chamadas para pedir ou renovar tokens são efetuadas automaticamente.
Eventualmente, `AcquireTokenSilentAsync` falhará – por exemplo, o utilizador foi terminada, ou foi alterada a palavra-passe noutro dispositivo. Quando MSAL Deteta que é possível resolver o problema, exigindo que uma ação interativa, é acionado um `MsalUiRequiredException`. A aplicação pode processar esta exceção de duas formas:

1.  Efetuar uma chamada contra `AcquireTokenAsync` imediatamente, o que resulta numa pedir ao utilizador para início de sessão. Este padrão é normalmente utilizado nas aplicações online em que não existe nenhum conteúdo offline na aplicação disponível para o utilizador. O exemplo gerado por esta configuração orientada utiliza este padrão: pode vê-lo no prazo de ação primeiro executar o exemplo: porque nenhum utilizador alguma vez utilizado a aplicação, `PublicClientApp.Users.FirstOrDefault()` irá conter um valor nulo e um `MsalUiRequiredException` exceção será emitida. O código no exemplo, em seguida, processa a exceção ao chamar `AcquireTokenAsync` resultavam pedir ao utilizador para início de sessão.

2.  Aplicações também podem efetuar uma indicação de visual para o utilizador que um interativa início de sessão é necessário, para que o utilizador pode seleccionar no momento certo para iniciar sessão ou a aplicação pode tentar novamente `AcquireTokenSilentAsync` numa altura posterior. Isto é normalmente utilizado quando o utilizador pode utilizar outras funcionalidades da aplicação sem ser interrompidos - por exemplo, não há conteúdo offline na aplicação. Neste caso, o utilizador pode decidir quando pretende iniciar sessão para aceder ao recurso protegido ou para atualizar as informações Desatualizadas ou a aplicação pode decidir repetir `AcquireTokenSilentAsync` quando a rede é restaurada após está temporariamente indisponível.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chamar o Microsoft Graph API utilizando o token obtido apenas

1. Adicione o método novo abaixo à sua `MainWindow.xaml.cs`. O método é utilizado para efetuar um `GET` pedido em relação a Graph API utilizando um cabeçalho de autorizar:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Obter mais informações sobre como efetuar uma chamada REST em relação a uma API protegida

Nesta aplicação de exemplo, o `GetHttpContentWithToken` método é utilizado para efetuar um HTTP `GET` contra um recurso protegido que necessita de um token de pedido e, em seguida, devolver o conteúdo para o autor da chamada. Este método adiciona o token adquirido no *cabeçalho de autorização de HTTP*. Para este exemplo, o recurso é o Microsoft Graph API *-me* ponto final – apresenta informações de perfil do utilizador.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicione um método para terminar sessão de utilizador

1. Adicione o método seguinte à sua `MainWindow.xaml.cs` para terminar sessão de utilizador:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Obter mais informações sobre o fim de sessão

`SignOutButton_Click`Remove o utilizador da cache de utilizador MSAL – isto informará MSAL de forma eficaz para se esqueça do utilizador atual para que um pedido futuro para adquirir um token apenas terá êxito se for efetuada para ser interativo.
Apesar da aplicação neste exemplo suporta um único utilizador, MSAL suporta cenários em que várias contas podem ser com sessão iniciada em simultâneo – um exemplo é uma aplicação de e-mail em que um utilizador tem várias contas.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Apresentar as informações do Token básicas

1. Adicione o seguinte método para a sua `MainWindow.xaml.cs` para apresentar informações básicas sobre o token:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>Mais Informações

Tokens adquirido através de *OpenID Connect* também conter um pequeno subconjunto de informações relevantes para o utilizador. `DisplayBasicTokenInfo`Mostra informações básicas contidas no token: por exemplo, nome a apresentar do utilizador e ID, bem como a data de expiração do token e a cadeia que representa o acesso do token próprio. Estas informações são apresentadas para ver. Pode clicar no *chamar Microsoft Graph API* botão várias vezes e ver que o mesmo token foi reutilizado em pedidos subsequentes. Também pode ver a data de expiração a ser expandida quando MSAL decide está na altura de renovar o token.
<!--end-collapse-->

