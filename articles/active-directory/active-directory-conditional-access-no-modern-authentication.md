---
title: Configurar o SharePoint Online e Exchange Online para o acesso condicional do Azure Active Directory | Microsoft Docs
description: Saiba como configurar o SharePoint Online e Exchange Online para o acesso condicional do Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5d09021627ca60a94cb1984b29b8dc7913d093d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Configurar o SharePoint Online e Exchange Online para o acesso condicional do Azure Active Directory 

Com [acesso condicional do Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), pode controlar como os utilizadores aceder às suas aplicações em nuvem. Se pretender utilizar o acesso condicional para controlar o acesso ao SharePoint e Exchange online, tem de:

- Reveja se o seu cenário de acesso condicional é suportado
- Impedir que as aplicações cliente de ignorar a imposição das políticas de acesso condicional.   

Este artigo explica como pode resolver ambos os casos.


## <a name="what-you-need-to-know"></a>O que precisa de saber

Pode utilizar o acesso condicional do Azure AD para proteger as aplicações na nuvem quando uma tentativa de autenticação é proveniente de:

- Um browser

- Uma aplicação de cliente que utiliza [autenticação moderna](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Algumas aplicações na nuvem também suportam os protocolos de autenticação existente. Isto aplica-se, por exemplo, ao SharePoint Online e Exchange Online. Quando uma aplicação de cliente pode utilizar um protocolo de autenticação existente para aceder uma aplicação de nuvem, o Azure AD não é possível impor uma política de acesso condicional esta tentativa de acesso. Para impedir que uma aplicação de cliente de ignorar a imposição de políticas, deve verificar se é possível ativar apenas a autenticação moderna nas aplicações cloud afetados. 

Exemplos de cliente de acesso condicional de aplicações não é aplicável a são:

- Office 2010 e anterior

- Office 2013 quando a autenticação moderna não está ativada



 
## <a name="control-access-to-sharepoint-online"></a>Controlar o acesso ao SharePoint Online

Para além de autenticação moderna, SharePoint Online também suporta os protocolos de autenticação existente. Se os protocolos de autenticação legadas estiverem ativados, as políticas de acesso condicional para o SharePoint não são impostas para clientes que não utilizam autenticação moderna.

Pode desativar os protocolos de autenticação existente para o acesso do SharePoint utilizando o  **[conjunto SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)**  cmdlet: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Controlar o acesso ao Exchange Online

Quando configurar políticas de acesso condicional para o Exchange Online, terá de rever o seguinte:

- Exchange ActiveSync

- Protocolos de autenticação existente



### <a name="exchange-activesync"></a>Exchange ActiveSync

Enquanto o Exchange Active Sync suporta a autenticação moderna, existem algumas limitações sobre o suporte para cenários de acesso condicional:

- Só pode configurar a condição de plataformas de dispositivos  

    ![Plataformas de dispositivos](./media/active-directory-conditional-access-no-modern-authentication/05.png)

- O requisito de autenticação multifator não é suportada  

    ![Acesso condicional](./media/active-directory-conditional-access-no-modern-authentication/01.png)

Para proteger de forma eficaz acesso ao Exchange Online do Exchange ActiveSync, pode:

- Configure uma política de acesso condicional suportados seguindo estes passos:

    a. Selecione apenas **Exchange Online do Office 365** como aplicação na nuvem.  

    ![Acesso condicional](./media/active-directory-conditional-access-no-modern-authentication/04.png)

    b. Selecione **Exchange Active Sync** como **aplicação cliente**e, em seguida, selecione **aplicar a política apenas para as plataformas suportadas**.  

    ![Plataformas de dispositivos](./media/active-directory-conditional-access-no-modern-authentication/03.png)

- Bloco do Exchange ActiveSync através de regras de serviços de Federação do Active Directory (AD FS).

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Protocolos de autenticação existente

Para além de autenticação moderna, Exchange Online também suporta os protocolos de autenticação existente. Se os protocolos de autenticação legadas estiverem ativados, as políticas de acesso condicional para o Exchange Online não são impostas para clientes que não utilizam autenticação moderna.

Pode desativar os protocolos de autenticação existente para o Exchange Online através da definição de regras do AD FS. Esta bloqueia o acesso de:

- Clientes mais antigos do Office, como o Office 2013, que não tiver ativada a autenticação moderna 

- Versões anteriores do Office


## <a name="set-up-ad-fs-rules"></a>Configurar regras de AD FS

Pode utilizar as seguintes regras de autorização de emissão para permitir ou bloquear o tráfego ao nível do AD FS. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Bloqueia o tráfego legado da extranet

Ao aplicar as regras de três seguintes: 

- Ativar o acesso:
    - Tráfego do Exchange ActiveSync
    - Tráfego de browser
    - Tráfego de autenticação moderna
- Bloquear acesso ao: 
    - Aplicações de cliente legacy da extranet

**Regra 1:**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regra 2:**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regra 3:**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>Bloqueia o tráfego de legado em qualquer lugar

Ao aplicar as regras de três seguintes:

- Ativar o acesso: 
    - Tráfego do Exchange ActiveSync
    - Tráfego de browser
    - Tráfego de autenticação moderna
- Bloquear acesso ao: 
    - Legado aplicações a partir de qualquer localização

##### <a name="rule-1"></a>Regra 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regra 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regra 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [de acesso condicional no Azure Active Directory](active-directory-conditional-access.md)




