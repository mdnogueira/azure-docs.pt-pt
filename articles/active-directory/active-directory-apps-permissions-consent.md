---
title: "Aplicações, permissões e consentimento no Azure Active Directory.| Microsoft Docs"
description: "O Azure AD Connect irá integrar os diretórios no local ao Azure Active Directory. Isto permite-lhe fornecer uma identidade comum para as aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD."
keywords: "introdução ao Azure AD, aplicações, o que é o Azure AD Connect, instalar o active directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/31/2017
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 6f6baf5e1538fb280a899065c64ca5688473c04a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Aplicações, permissões e consentimento no Azure Active Directory
No Azure Active Directory, pode adicionar aplicações ao seu diretório.  As aplicações podem variar consoante o tipo de aplicação.  Para ver as aplicações no portal clássico, selecione um diretório e escolha aplicações.

![](media/active-directory-apps-permissions-consent/apps1.png)

> [!IMPORTANT]
> A Microsoft recomenda que faça a gestão do Azure AD com o [centro de administração do Azure AD](https://aad.portal.azure.com) no portal do Azure em vez de utilizar o portal clássico do Azure referenciado neste artigo.

## <a name="types-of-apps"></a>Tipos de aplicações

1. **Aplicações de inquilino único** </br>
    - **Aplicações de inquilino único** - Frequentemente designadas por aplicações de linha de negócio (LOB). Este é o caso em que alguém na organização desenvolve a sua própria aplicação e gostaria de permitir que os utilizadores na organização iniciem sessão na aplicação.
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **Aplicações de Proxy de Aplicação** - Quando expõe uma aplicação no local com o Proxy de Aplicação do Azure AD, uma aplicação de inquilino único é registada no seu inquilino (além do serviço Proxy de Aplicação). Esta aplicação é o que representa a sua aplicação no local em todas as interações de nuvem (por exemplo, autenticação). (O Proxy de Aplicação requer o Azure AD Básico ou superior.)


2. **Aplicações multi-inquilino**
    - **Aplicações multi-inquilino que outras pessoas podem consentir** - semelhantes às "aplicações de inquilino único desenvolvidas pela sua organização". A principal diferença (além da lógica na aplicação propriamente dita) é que os utilizadores de outros inquilinos também podem dar consentimento e iniciar sessão na aplicação.</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **Aplicações multi-inquilino desenvolvidas por terceiros, que a Contoso pode consentir**. (Ou "aplicações autorizadas", para abreviar.) Este é o aspeto negativo das "aplicações multi-inquilino desenvolvidas pela sua organização". Quando outra organização desenvolve uma aplicação multi-inquilino, os utilizadores da sua organização podem autorizar a aplicação e iniciar sessão na mesma.
    - **Aplicações originais da Microsoft** - Aplicações que representam serviços da Microsoft. O consentimento é impulsionado pelo facto de se inscrever no serviço. Por vezes, existe UX e lógica especiais para certas aplicações originais, que muitas vezes são utilizadas ao estabelecer políticas em torno do acesso à aplicação.</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **Aplicações pré-integradas** - Aplicações disponíveis na Galeria de Aplicação do Azure AD, que pode adicionar ao seu diretório para fornecer início de sessão único (e, em alguns casos, aprovisionamento) para aplicações SaaS populares.
    - **Início de sessão único do Azure AD**: o SSO "verdadeiro", para as aplicações que podem ser integradas com o Azure AD, através de um protocolo de início de sessão suportado, tal como SAML 2.0 ou OpenID Connect. O assistente orienta-o ao longo da configuração.
    - **Início de sessão único de palavra-passe**: o Azure AD armazena em segurança as credenciais do utilizador para a aplicação e as credenciais são “injetadas” no formulário de início de sessão pela extensão de browser Acesso da Aplicação do Azure AD. Também conhecido como "cofres de palavras-passe".

## <a name="permissions"></a>Permissões

Quando uma aplicação é registada, o utilizador que faz o registo da aplicação (ou seja, o programador) define as permissões às quais a aplicação precisa de acesso e os recursos. (Os recursos são definidos como outras aplicações.) Por exemplo, alguém que cria uma aplicação de leitor de correio, afirmaria que a aplicação exige a permissão "Aceder a caixas de correio como o utilizador com sessão iniciada" no recurso "Exchange Online do Office 365":
    
![](media/active-directory-apps-permissions-consent/apps6.png)

Para que uma aplicação (o cliente) peça uma determinada permissão a partir de outra aplicação (o recurso), o programador da aplicação de recurso define as permissões existentes. No nosso exemplo, a Microsoft, a proprietária da aplicação de recurso "Exchange Online do Office 365", definiu uma permissão com o nome "Aceder a caixas de correio como o utilizador com sessão iniciada".

Ao definir permissões, o programador da aplicação tem de definir se é possível dar consentimento à permissão ou se esta precisa de consentimento do administrador. Isto permite que os programadores permitam aos utilizadores dar consentimento nas suas próprias aplicações, pedindo apenas permissões de baixa sensibilidade, mas exijam que os administradores deem consentimento a permissões mais sensíveis. Por exemplo, a aplicação de recurso "Azure Active Directory" foi definida de modo a que os utilizadores possam dar consentimento a aplicações, pedindo permissões só de leitura limitadas.  No entanto, o consentimento de administrador é preciso para as permissões de leitura total e todas as permissões de escrita.

Visto que os clientes nativos não são autenticados, uma aplicação definida como uma aplicação cliente nativa só pode solicitar permissões delegadas. Isto significa que tem de existir sempre um utilizador real envolvido durante a obtenção de um token. As aplicações Web e as APIs Web (clientes confidenciais), têm sempre de ser autenticadas com o Azure AD ao obter um token de acesso. Ou seja, também têm a possibilidade de solicitar permissões só de aplicação. Por exemplo, se um serviço de back-end tiver de ser autenticado noutro serviço de back-end. As aplicações que solicitam permissões só de aplicação precisam sempre de consentimento de administrador.

Resumindo:



- Uma aplicação (cliente) declara as permissões de que precisa para outras aplicações (recursos).
- Uma aplicação (recurso) declara as permissões que são expostas a outras aplicações (clientes).
- Uma permissão pode ser uma permissão só de aplicação ou uma permissão delegada.
- Uma permissão delegada pode ser marcada como "permite consentimento do utilizador" ou "precisa de consentimento do administrador".
- Uma aplicação pode comportar-se como um cliente (ao declarar que necessita de permissões para um recurso), como um recurso (ao declarar as permissões que expõe) ou ambos.

## <a name="controls"></a>Controlos

Segue-se uma lista dos diferentes controlos de administrador disponíveis para todos estes comportamentos. É possível aceder aos controlos de administrador no portal clássico a partir da opção Configurar sob o diretório.

![](media/active-directory-apps-permissions-consent/apps7.png)

No portal do Azure, em **Gerir**, **Definições de utilizador**.

![](media/active-directory-apps-permissions-consent/apps11.png)



- Pode controlar se os utilizadores podem dar consentimento a aplicações:

No portal clássico, selecione **Os utilizadores podem conceder permissões a aplicações para acederem aos seus dados.**
![](media/active-directory-apps-permissions-consent/apps8.png)

No portal do Azure, selecione **Os utilizadores podem permitir que as aplicações acedam aos seus dados**.
![](media/active-directory-apps-permissions-consent/apps12.png)



- Pode controlar se os utilizadores podem registar as suas próprias aplicações LOB de inquilino único: no portal clássico, selecione **Os utilizadores podem adicionar aplicações integradas.**
![](media/active-directory-apps-permissions-consent/apps9.png)

No portal do Azure, selecione **Os utilizadores podem permitir que as aplicações acedam aos seus dados**.
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>Mesmo se permitir que os utilizadores registem aplicações LOB de inquilino único, existem limites para o que pode ser registado.  
>Por exemplo, programadores que não são administradores de diretório.
>
>- Os utilizadores não podem tornar uma aplicação de inquilino único numa aplicação multi-inquilino.
>- Ao registar aplicações LOB de inquilino único, os utilizadores não podem solicitar permissões só de aplicação para outras aplicações.
>- Ao registar aplicações LOB de inquilino único, os utilizadores não podem pedir permissões delegadas para outras aplicações se essas permissões precisarem de consentimento de administrador.
>- Os utilizadores não podem fazer alterações a aplicações das quais não são proprietários.



- Pode controlar se os utilizadores podem adicionar aplicações pré-integradas que utilizem SSO de palavra-passe (também conhecidas como "cofres de palavras-passe") ![](media/active-directory-apps-permissions-consent/apps10.png)



- Pode controlar em que condições é possível aceder às aplicações (ou seja, acesso condicional). Tenha em atenção que este tipo de acesso aplica-se à aplicação cliente e à aplicação de recurso. Por isso, digamos que define uma política de acesso condicional que afirma que a aplicação "Exchange Online do Office 365" só pode ser acedida a partir de computadores conformes.  Esta política também é aplicada quando um utilizador tenta utilizar uma aplicação cliente que pede permissões para o Exchange Online.



- Tem visibilidade das aplicações para as quais foi dado consentimento e das aplicações que estão a ser utilizadas.

1.  Quando um utilizador dá consentimento a uma aplicação, é criado um objeto ServicePrincipal no inquilino. A criação de ServicePrincipal está incluída no relatório de auditoria.
2.  Os relatórios de atividade de início de sessão do utilizador indicam em que aplicação o utilizador está a iniciar sessão. 

## <a name="example"></a>Exemplo

Por exemplo, vejamos a aplicação “FabrikamMail for Office 365”, na qual que reparou que os utilizadores no seu inquilino estão a iniciar sessão. "FabrikamMail" é uma aplicação de leitor de correio para Android, publicada pela "Fabrikam, Inc.". Esta aplicação insere-se na categoria “aplicações multi-inquilino desenvolvidas por terceiros, que a Contoso pode consentir”.

Se os utilizadores tiverem permissão para dar consentimento, recebem um pedido de consentimento da primeira vez que iniciarem sessão: ![](media/active-directory-apps-permissions-consent/apps14.png)

"Acesso às suas caixas de correio" é a cadeia de consentimento destinada ao utilizador para a permissão "Aceder a caixas de correio como o utilizador com sessão iniciada" exposta pelo "Exchange Online do Office 365" (ou seja, o Exchange).

Pode ver as permissões ao procurar o objeto ServicePrincipal para o Exchange (o recurso), que foi adicionado quando se inscreveu no Office 365. Pode considerar o objeto ServicePrincipal de uma "instância" da aplicação no seu inquilino, que é utilizado para gravar diferentes opções e configurações.  Para ver isto, utilize `Get-AzureADServicePrincipal` no PowerShell.

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

O consentimento é iniciado quando o utilizador clica em "Aceitar". Em primeiro lugar, é criado um objeto ServicePrincipal para “FabrikamMail for Office 365” no inquilino. O ServicePrincipal tem um aspeto semelhante ao seguinte:

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

O consentimento de uma aplicação cria uma ligação Oauth2PermissionGrant entre o seguinte:
  
- o objeto de utilizador
- as aplicações cliente ServicePrincipalName (SPN)
- as aplicações de recurso ServicePrincipalName (SPN)
- permissões na aplicação de recurso.  

No caso de FabrikamMail, tem um aspeto semelhante ao seguinte:

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId** é o ID de objeto do principal de serviço de FabrikamMail (aquele que acabou de ser criado), **PrincipalId** é o ID de objeto do utilizador (do utilizador que consentiu), **ResourceId** é o ID de objeto do principal de serviço do Exchange, Scope é a permissão no Exchange à qual foi dado consentimento).

Se os utilizadores não tiverem permissão para dar consentimento, verão um ecrã que diz que essa permissão é precisa.

