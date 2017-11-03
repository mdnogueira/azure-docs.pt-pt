---
title: "As alterações efetuadas a um projeto MVC quando ligar ao Azure AD | Microsoft Docs"
description: "Descreve o que acontece ao seu projeto MVC quando ligar ao Azure AD utilizando os serviços do Visual Studio ligado"
services: active-directory
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 095411a7fc854f4dce11921adb0f57c5389a8e13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu ao meu projeto MVC (serviço ligado da Visual Studio do Azure Active Directory)?
> [!div class="op_single_selector"]
> * [Introdução](vs-active-directory-dotnet-getting-started.md)
> * [O que aconteceu](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Foram adicionadas referências
### <a name="nuget-package-references"></a>Referências de pacote NuGet
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Referências de .NET
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System**
* **System.IdentityModel.Tokens.Jwt**
* **System**

## <a name="code-has-been-added"></a>Foi adicionado código
### <a name="code-files-were-added-to-your-project"></a>Foram adicionados ficheiros de código ao projeto
Uma classe de startup da autenticação, **App_Start/Startup.Auth.cs** foi adicionado ao seu projeto que contém a lógica de arranque para a autenticação do Azure AD. Além disso, foi adicionada uma classe de controlador, Controllers/AccountController.cs que contém **SignIn()** e **SignOut()** métodos. Por fim, uma vista parcial, **Views/Shared/_LoginPartial.cshtml** foi adicionado que contenha uma hiperligação de ação para início de sessão/SignOut.

### <a name="startup-code-was-added-to-your-project"></a>Código de arranque foi adicionado ao seu projeto
Se já tinha uma classe de Startup no seu projeto, a **configuração** método foi atualizado para incluir uma chamada para **ConfigureAuth(app)**. Caso contrário, uma classe de Startup foi adicionada ao seu projeto.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>O App. config ou Web. config tem novos valores de configuração
Foram adicionadas as seguintes entradas de configuração.

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>Foi criada uma aplicação do Azure Active Directory (AD)
Aplicação do Azure AD foi criada no diretório que selecionou no assistente.

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se a opção posso marcada *desativar a autenticação de contas de utilizador individuais*, as alterações adicionais foram efetuadas à minha projeto?
As referências de pacote NuGet foram removidas e os ficheiros foram removidos e cópia de segurança. Dependendo do Estado do seu projeto, poderá ter de remover referências adicionais ou ficheiros manualmente ou modificar o código conforme apropriado.

### <a name="nuget-package-references-removed-for-those-present"></a>Referências de pacote NuGet removidas (para as presente)
* **Microsoft.AspNet.Identity.Core**
* **Microsoft.AspNet.Identity.EntityFramework**
* **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Ficheiros de código de uma cópia de segurança e remover (para as presente)
Cada um dos seguintes ficheiros foi uma cópia de segurança e removida do projeto. Ficheiros de cópia de segurança estão localizados numa pasta 'Cópia de segurança' na raiz do diretório do projeto.

* **App_Start\IdentityConfig.CS**
* **Controllers\ManageController.CS**
* **Models\IdentityModels.CS**
* **Models\ManageViewModels.CS**

### <a name="code-files-backed-up-for-those-present"></a>Ficheiros de código de cópia de segurança (para as presente)
Cada um dos seguintes ficheiros tiver sido feita antes de a ser substituído. Ficheiros de cópia de segurança estão localizados numa pasta 'Cópia de segurança' na raiz do diretório do projeto.

* **Startup.CS**
* **App_Start\Startup.auth.CS**
* **Controllers\AccountController.CS**
* **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se a opção posso marcada *ler os dados de diretório*, as alterações adicionais foram efetuadas à minha projeto?
Referências adicionais foram adicionadas.

### <a name="additional-nuget-package-references"></a>Referências de pacote NuGet adicionais
* **EntityFramework**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **System.Spatial**

### <a name="additional-net-references"></a>Referências adicionais do .NET
* **EntityFramework**
* **EntityFramework.SqlServer**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
* **System.Spatial**

### <a name="additional-code-files-were-added-to-your-project"></a>Ficheiros de código adicionais foram adicionados ao seu projeto
Foram adicionados dois ficheiros para suportar a colocação em cache de token: **Models\ADALTokenCache.cs** e **Models\ApplicationDbContext.cs**.  Um controlador adicional e a vista foram adicionados para ilustrar ao aceder aos informações de perfil de utilizador através do gráfico APIs do Azure.  Estes ficheiros são **Controllers\UserProfileController.cs** e **Views\UserProfile\Index.cshtml**.

### <a name="additional-startup-code-was-added-to-your-project"></a>Códigos de arranque adicionais foi adicionado ao seu projeto
No **startup.auth.cs** de ficheiros, um novo **OpenIdConnectAuthenticationNotifications** objeto foi adicionado à **notificações** membro o **OpenIdConnectAuthenticationOptions**.  Isto é permitir receber o código de OAuth e trocá-lo para um token de acesso.

### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Foram feitas alterações adicionais ao seu App. config ou Web. config
Foram adicionadas as seguintes entradas de configuração adicionais.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Foram adicionadas as seguintes secções de configuração e a cadeia de ligação.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


### <a name="your-azure-active-directory-app-was-updated"></a>A aplicação do Active Directory do Azure foi atualizada
A aplicação do Active Directory do Azure foi atualizada para incluir o *ler os dados de diretório* permissão e uma chave adicional que foi criado, em seguida, que utilizou como o *ida: ClientSecret* no **Web. config** ficheiro.

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

