---
title: "As alterações efetuadas a um projeto de end WebApi quando ligar ao Azure AD | Microsoft Docs"
description: Descreve o que acontece ao seu projeto de end WebApi quando ligar ao Azure AD utilizando o Visual Studio
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 086e5a9622cad681cd282345d97e0c28ee7de2fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu ao meu projeto end WebApi (serviço ligado da Visual Studio do Azure Active Directory)
> [!div class="op_single_selector"]
> * [Introdução](vs-active-directory-webapi-getting-started.md)
> * [O que aconteceu](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Foram adicionadas referências
### <a name="nuget-package-references"></a>Referências de pacote NuGet
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>Referências de .NET
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>Alterações do código
### <a name="code-files-were-added-to-your-project"></a>Foram adicionados ficheiros de código ao projeto
Uma classe de startup da autenticação, **App_Start/Startup.Auth.cs** foi adicionado ao seu projeto que contém a lógica de arranque para a autenticação do Azure AD.

### <a name="startup-code-was-added-to-your-project"></a>Código de arranque foi adicionado ao seu projeto
Se já tinha uma classe de Startup no seu projeto, a **configuração** método foi atualizado para incluir uma chamada para `ConfigureAuth(app)`. Caso contrário, uma classe de Startup foi adicionada ao seu projeto.

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>O ficheiro App. config ou Web. config tem valores de configuração de novo.
Foram adicionadas as seguintes entradas de configuração.

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>Foi criada uma aplicação do Azure AD
Aplicação do Azure AD foi criada no diretório que selecionou no assistente.

[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se a opção posso marcada *desativar a autenticação de contas de utilizador individuais*, as alterações adicionais foram efetuadas à minha projeto?
As referências de pacote NuGet foram removidas e os ficheiros foram removidos e cópia de segurança. Dependendo do Estado do seu projeto, poderá ter de remover referências adicionais ou ficheiros manualmente ou modificar o código conforme apropriado.

### <a name="nuget-package-references-removed-for-those-present"></a>Referências de pacote NuGet removidas (para as presente)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>Ficheiros de código de uma cópia de segurança e remover (para as presente)
Cada um dos seguintes ficheiros foi uma cópia de segurança e removida do projeto. Ficheiros de cópia de segurança estão localizados numa pasta 'Cópia de segurança' na raiz do diretório do projeto.

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>Ficheiros de código de cópia de segurança (para as presente)
Cada um dos seguintes ficheiros tiver sido feita antes de a ser substituído. Ficheiros de cópia de segurança estão localizados numa pasta 'Cópia de segurança' na raiz do diretório do projeto.

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Se a opção posso marcada *ler os dados de diretório*, as alterações adicionais foram efetuadas à minha projeto?
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Foram feitas alterações adicionais ao seu App. config ou Web. config
Foram adicionadas as seguintes entradas de configuração adicionais.

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>A aplicação do Active Directory do Azure foi atualizada
A aplicação do Active Directory do Azure foi atualizada para incluir o *ler os dados de diretório* permissão e uma chave adicional que foi criado, em seguida, que utilizou como o *ida: palavra-passe* no `web.config` ficheiro.

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

