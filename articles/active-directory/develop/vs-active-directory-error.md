---
title: "Como diagnosticar erros com o Assistente de ligação do Active Directory do Azure"
description: "O Assistente de ligação do Active Directory detetou um tipo de autenticação incompatível"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/05/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 4f29f62b2996cae98b02c1ed5fcb59eca09301ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connection-wizard"></a>Erros de diagnóstico com o Assistente de ligação do Active Directory do Azure
Ao detetar o código de autenticação anterior, o assistente detetou um tipo de autenticação incompatível.   

## <a name="what-is-being-checked"></a>O que está a ser modificado?
**Nota:** para detetar corretamente o código de autenticação anterior num projeto, o projeto tem de ser criado.  Se encontrou o erro e não tiver um código de autenticação anterior no seu projeto, reconstruir e tente novamente.

### <a name="project-types"></a>Tipos de projeto
O assistente verifica o tipo de projeto que estiver a desenvolver, de modo que pode inserir a lógica de autenticação correto no projeto.  Se não houver qualquer controlador que derive `ApiController` no projeto, o projeto é considerado um projeto de end WebAPI.  Se existirem apenas os controladores que derivem de `MVC.Controller` no projeto, o projeto é considerado um projeto MVC.  Tudo o resto não é suportado pelo assistente.

### <a name="compatible-authentication-code"></a>Código de autenticação compatível
O assistente também verifica a existência de definições de autenticação que foram anteriormente configuradas com o assistente ou que são compatíveis com o assistente.  Se todas as definições estão presentes, é considerada um caso re-entrant e abrir o assistente apresenta as definições.  Se apenas algumas das definições estiverem presentes, é considerada um caso de erro.

Num projeto MVC, o assistente verifica a existência de qualquer uma das seguintes definições, que resultam da utilização anterior do assistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o assistente verifica a existência de qualquer uma das seguintes definições num projeto Web API, que resultam da utilização anterior do assistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

### <a name="incompatible-authentication-code"></a>Código de autenticação incompatível
Por fim, o assistente tenta detetar as versões do código de autenticação que foram configuradas com versões anteriores do Visual Studio. Se receber este erro, significa que projecto contém um tipo de autenticação incompatível. O assistente Deteta os seguintes tipos de autenticação de versões anteriores do Visual Studio:

* Autenticação do Windows 
* Contas de utilizador individuais 
* Contas organizacionais 

Para detetar a autenticação do Windows num projeto MVC, o assistente procura o `authentication` elemento a partir do seu **Web. config** ficheiro.

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;authentication mode="Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Para detetar a autenticação do Windows num projeto Web API, o assistente procura o `IISExpressWindowsAuthentication` elemento a partir do seu projeto **. csproj** ficheiro:

<pre>
    &lt;Project&gt;
        &lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;enabled&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup>
    &lt;/Project&gt;
</pre>

Para detetar a autenticação de contas de utilizador individuais, o assistente procura o elemento de pacote a partir do seu **Packages** ficheiro.

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /&gt;</span>
    &lt;/packages&gt;
</pre>

Para detetar um formulário antigo de autenticação da conta institucional, o assistente procura o seguinte elemento de **Web. config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatíveis e execute novamente o assistente.

Para obter mais informações, consulte [cenários de autenticação para o Azure AD](active-directory-authentication-scenarios.md).

#<a name="next-steps"></a>Passos seguintes
- [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md)