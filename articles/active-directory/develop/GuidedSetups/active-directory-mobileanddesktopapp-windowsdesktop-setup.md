---
title: "Azure AD v2 Windows ambiente de trabalho ao obter iniciada - o programa de configuração | Microsoft Docs"
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
ms.openlocfilehash: 4065727aef04d7969d438c6ef79127bb44568be1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
## <a name="set-up-your-project"></a>Configurar o projeto

Esta secção fornece instruções passo a passo para saber como criar um novo projeto para demonstrar como integrar uma aplicação .NET de ambiente de trabalho do Windows (XAML) com *início de sessão com a Microsoft* que possa consultar APIs da Web que necessita de um token.

A aplicação criada por este guia expõe um botão para graph e mostrar os resultados no ecrã e botão de início de sessão.

> Prefere transferir o projeto do Visual Studio este exemplo em vez disso? [Transferir um projeto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip) e avance para o [passo da configuração](#create-an-application-express) para configurar o exemplo de código antes de executar.


### <a name="create-your-application"></a>Criar a sua aplicação
1. No Visual Studio:`File` > `New` > `Project`<br/>
2. Em *modelos*, selecione`Visual C#`
3. Selecione `WPF App` (ou *aplicação WPF* consoante a versão do seu Visual Studio)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Adicionar a biblioteca de autenticação da Microsoft (MSAL) ao seu projeto
1. No Visual Studio:`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Copie/cole o seguinte na janela da consola do Gestor de pacotes:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> O pacote acima instala a biblioteca de autenticação da Microsoft (MSAL). MSAL processa a aquisição, colocação em cache e atualizar toskens de utilizador utilizado para acesso a APIs protegidas pelo Azure Active Directory v2.

## <a name="add-the-code-to-initialize-msal"></a>Adicione o código para inicializar MSAL
Este passo irá ajudá-lo a criar uma classe para processar interação com MSAL biblioteca, tais como o processamento de tokens.

1. Abra o `App.xaml.cs` de ficheiros e adicionar a referência da biblioteca MSAL para a classe:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Atualize a classe de aplicação para o seguinte:
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>Criar a IU da aplicação
A secção abaixo mostra como uma aplicação pode consultar um servidor de back-end protegidos, como o Microsoft Graph. Um ficheiro de MainWindow.xaml automaticamente deverá ser criado como parte do seu modelo de projeto. Abrir este ficheiro este ficheiro e, em seguida, siga as instruções abaixo:

Substitua a sua aplicação `<Grid>` com ser o seguinte:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
