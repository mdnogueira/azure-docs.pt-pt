---
title: "Conector do Proxy de aplicações do Azure AD de instalação silenciosa | Microsoft Docs"
description: "Aborda como executar uma instalação autónoma do Azure AD de conector de Proxy de aplicações para fornecer acesso remoto seguro às suas aplicações no local."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cf3058832ba656a1a18aea194bf4e5ce4e800e76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Criar um script de instalação automática para o conector do Proxy de aplicações do Azure AD

Este tópico ajuda a criar um script do Windows PowerShell que permite a instalação automática e o registo para o conector do Proxy de aplicações do Azure AD.

Esta capacidade é útil quando pretender:

* Instale o conector em servidores do Windows que não tem interface de utilizador ativado ou que não é possível aceder com o ambiente de trabalho remoto.
* Instalar e registar muitos conetores em simultâneo.
* Integre a instalação do conector e o registo como parte de outro procedimento.
* Crie uma imagem de servidor padrão que contém os bits de conector, mas não está registada.

Para o [conector do Proxy de aplicação](application-proxy-understand-connectors.md) funcione, tem de ser registado no diretório do Azure AD através de um administrador global e a palavra-passe. Normalmente esta informação é introduzida durante a instalação do conector numa caixa de diálogo de pop-up, mas pode utilizar o PowerShell para automatizar este processo em vez disso.

Existem dois passos para uma instalação autónoma. Em primeiro lugar, instale o conector. Segundo, registe o conector com o Azure AD. 

## <a name="install-the-connector"></a>Instalar o conector
Utilize os seguintes passos para instalar o conector sem registando:

1. Abra uma linha de comandos.
2. Execute o seguinte comando, no qual o /q significa silenciosos instalação. Uma instalação silenciosos não solicita-lhe para aceitar o contrato de licença de utilizador final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registar o conector com o Azure AD
Existem dois métodos que pode utilizar para registar o conector:

* Registar o conector através de um objeto de credencial do Windows PowerShell
* Registar o conector utilizando um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registar o conector através de um objeto de credencial do Windows PowerShell
1. Criar um objeto de credenciais do Windows PowerShell `$cred` que contém um nome de utilizador administrativo e a palavra-passe para o seu diretório. Execute o seguinte comando, substituindo  *\<username\>*  e  *\<palavra-passe\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Aceda a **conector do Proxy da aplicação AAD C:\Program Files\Microsoft** e execute o seguinte script utilizando o `$cred` objeto que criou:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>Registar o conector utilizando um token criado offline
1. Crie um token offline utilizando a classe de AuthenticationContext com os valores neste fragmento de código:

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }


2. Assim que tiver o token, crie um SecureString utilizando o token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Execute o seguinte comando do Windows PowerShell, substituindo \<inquilino GUID\> com o seu ID de diretório:

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>Passos seguintes 
* [Publicar aplicações com o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
* [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
* [Resolver problemas com o Proxy da aplicação](active-directory-application-proxy-troubleshoot.md)


