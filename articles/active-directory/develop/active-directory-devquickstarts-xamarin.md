---
title: "Azure AD Xamarin, introdução | Microsoft Docs"
description: "Crie aplicações de Xamarin que integram com o Azure AD para início de sessão e chamar APIs do Azure AD protegida com OAuth."
services: active-directory
documentationcenter: xamarin
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 54ee403f283bc5dc79911e2e813dd513ff595828
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-with-xamarin-apps"></a>Integrar o Azure AD com aplicações Xamarin
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Com o Xamarin, pode escrever as aplicações móveis em c# que podem ser executados no iOS, Android e Windows (dispositivos móveis e PCs). Se estiver a criar uma aplicação utilizando Xamarin, Azure Active Directory (Azure AD) permite simples autenticar os utilizadores com as respetivas contas do Azure AD. A aplicação com a segurança também pode consumir quaisquer web API que esteja protegida pelo Azure AD, como as APIs do Office 365 ou a API do Azure.

Para aplicações Xamarin que precisam de aceder a recursos protegidos, o Azure AD fornece o Active Directory Authentication Library (ADAL). É o único objetivo ADAL torna mais fácil para aplicações obter os tokens de acesso. Para demonstrar como é fácil, este artigo mostra como criar aplicações DirectorySearcher que:

* Execute em dispositivos iOS, Android, ambiente de trabalho do Windows, Windows Phone e loja Windows.
* Utilize uma biblioteca de classe portátil único (PCL) para autenticar os utilizadores e obter tokens para a Azure AD Graph API.
* Procure um diretório para os utilizadores com um UPN indicado.

## <a name="before-you-get-started"></a>Antes de começar
* Transferir o [projeto de estrutura](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), ou transfira a [exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cada transferência é uma solução do Visual Studio 2013.
* Também precisa de um inquilino do Azure AD na qual pretende criar utilizadores e registar a aplicação. Se ainda não tiver um inquilino, [saber como obter um](active-directory-howto-tenant.md).

Quando estiver pronto, siga os procedimentos das secções junto quatro.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Passo 1: Configurar o ambiente de desenvolvimento de Xamarin
Porque este tutorial inclui projetos para iOS, Android e Windows, terá de Visual Studio e Xamarin. Para criar o ambiente necessário, concluir o processo no [definir configurar e instalar o Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) no MSDN. As instruções incluem material que poderá consultar para saber mais sobre o Xamarin enquanto aguarda para as instalações seja concluída.

Após concluir a configuração, abra a solução no Visual Studio. Aqui, encontrará seis projetos: cinco específicos da plataforma projetos e um PCL, DirectorySearcher.cs, que será partilhada em todas as plataformas.

## <a name="step-2-register-the-directorysearcher-app"></a>Passo 2: Registar a aplicação de DirectorySearcher
Para ativar a aplicação obter os tokens, terá primeiro de registá-lo no seu inquilino do Azure AD e conceder permissão para aceder à API do Azure AD Graph. Eis como:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. Em seguida, sob o **diretório** lista, selecione o inquilino do Active Directory onde é necessário registar a aplicação.
3. Clique em **mais serviços** no painel esquerdo e, em seguida, selecione **do Azure Active Directory**.
4. Clique em **registos de aplicação**e, em seguida, selecione **adicionar**.
5. Para criar uma nova **aplicação cliente nativa**, siga as instruções.
  * **Nome** descreve a aplicação aos utilizadores.
  * **URI de redirecionamento** é uma combinação de esquema e a cadeia que utiliza o Azure AD para devolver respostas token. Introduza um valor (por exemplo, http://DirectorySearcher).
6. Depois de concluído o registo, o Azure AD atribui a aplicação um ID de aplicação único. Copie o valor da **aplicação** separador, porque irá precisar dele mais tarde.
7. No **definições** página, selecione **permissões obrigatórias**e, em seguida, selecione **adicionar**.
8. Selecione **Microsoft Graph** como a API. Em **permissões delegadas**, adicione o **ler dados de diretório** permissão.  
Esta ação permite a aplicação consultar a Graph API para os utilizadores.

## <a name="step-3-install-and-configure-adal"></a>Passo 3: Instalar e configurar a ADAL
Agora que tem uma aplicação no Azure AD, pode instalar ADAL e escrever o seu código relacionadas com identidade. Para ativar a ADAL comunicar com o Azure AD, conceda-lhe algumas informações sobre o registo de aplicação.

1. Adicione ADAL para o projeto de DirectorySearcher utilizando a consola do Gestor de pacotes.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Tenha em atenção que as duas referências de biblioteca estão adicionadas para cada projeto: a parte PCL da ADAL e uma parte específica da plataforma.
2. No projeto DirectorySearcherLib, abra DirectorySearcher.cs.
3. Substitua os valores de membro de classe com os valores que introduziu no portal do Azure. Seu código se refere a estes valores, sempre que utilizar a ADAL.

  * O *inquilino* é o domínio de inquilino do Azure AD (por exemplo, contoso.onmicrosoft.com).
  * O *clientId* é o ID de cliente da aplicação e que copiou do portal.
  * O *returnUri* é o URI que introduziu no portal (por exemplo, http://DirectorySearcher) de redirecionamento.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Passo 4: Utilizar a ADAL para obter os tokens do Azure AD
Quase todo de lógica de autenticação da aplicação reside na `DirectorySearcher.SearchByAlias(...)`. Tudo o que é necessário em projetos de plataforma específica está a transmitir um parâmetro a nível contextual do `DirectorySearcher` PCL.

1. Abra DirectorySearcher.cs e, em seguida, adicione um novo parâmetro para o `SearchByAlias(...)` método. `IPlatformParameters`é o parâmetro contextual que contém os objetos específicos da plataforma que tem de efetuar a autenticação ADAL.

    ```C#
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inicializar `AuthenticationContext`, que é a classe principal da ADAL.  
Esta ação transfere a ADAL as coordenadas que necessita para comunicar com o Azure AD.
3. Chamar `AcquireTokenAsync(...)`, que aceita o `IPlatformParameters` de objeto e invoca o fluxo de autenticação que é necessário para devolver um token para a aplicação.

    ```C#
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)`tenta primeiro devolveu um token para o recurso pedido (a Graph API neste caso) sem pedir que os utilizadores introduzam as suas credenciais (através de colocação em cache ou atualizar antigos tokens). Conforme necessário, mostra os utilizadores a página de início de sessão do Azure AD antes de adquirir o token de pedido.
4. Anexar o token de acesso para o pedido da Graph API no **autorização** cabeçalho:

    ```C#
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

É tudo para a `DirectorySearcher` PCL e a aplicação do relacionadas com a identidade do código. Tudo o que é chamada permanece o `SearchByAlias(...)` método nas vistas de cada plataforma e, sempre que necessário, para adicionar código para processar corretamente o ciclo de vida de IU.

### <a name="android"></a>Android
1. No mainactivity. CS, adicione uma chamada para `SearchByAlias(...)` no botão de clique processador:

    ```C#
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Substituir o `OnActivityResult` ciclo de vida método para reencaminhar qualquer autenticação redireciona para o método adequado. ADAL fornece um método de programa auxiliar para este no Android:

    ```C#
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Ambiente de trabalho do Windows
No MainWindow.xaml.cs, efetuar uma chamada para `SearchByAlias(...)` transferindo uma `WindowInteropHelper` no ambiente de trabalho `PlatformParameters` objeto:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
No DirSearchClient_iOSViewController.cs, o iOS `PlatformParameters` objeto aceita uma referência para o controlador de vista:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
No Windows Universal, abra o MainPage.xaml.cs e, em seguida, implementar o `Search` método. Este método utiliza um método de programa auxiliar num projeto partilhado ao atualizar a IU conforme necessário.

```C#
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>Passos seguintes
Agora tem uma aplicação Xamarin que pode autenticar os utilizadores e chamar com segurança as APIs web através da utilização de OAuth 2.0 em cinco diferentes plataformas.

Se ainda não já preenchidos do inquilino com utilizadores, agora é o tempo para o fazer.

1. Executar a aplicação de DirectorySearcher e, em seguida, inicie sessão com um dos utilizadores.
2. Procurar outros utilizadores com base no respetivo UPN.

ADAL torna mais fácil incorporar funcionalidades de identidade comuns na aplicação. -Encarrega-se de todo o trabalho de dirty para si, tais como a gestão da cache, suporte de protocolo de OAuth, que apresenta um início de sessão IU, o utilizador e a atualização expirou tokens. É necessário saber apenas uma única chamada API, `authContext.AcquireToken*(…)`.

Para referência, transfira o [exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (sem os valores de configuração).

Agora pode passar para os cenários de identidade adicionais. Por exemplo, experimente [proteger uma API Web do .NET com o Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
