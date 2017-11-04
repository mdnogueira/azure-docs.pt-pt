---
title: Authentication do Azure Active Directory baseada em certificado em dispositivos iOS | Microsoft Docs
description: "Saiba mais sobre os cenários suportados e os requisitos para configurar a autenticação baseada em certificado em soluções com dispositivos iOS"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 8e9610fee635ec24f8f7eaeba0c6a19a421c0456
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Authentication do Azure Active Directory baseada em certificado em dispositivos iOS

Autenticação baseada em certificado (CBA) permite-lhe ser autenticado pelo Azure Active Directory com um certificado de cliente num dispositivo Windows, Android ou iOS ao ligar a sua conta do Exchange online para:

* Aplicações móveis do Office, tais como o Microsoft Outlook e o Microsoft Word   
* Clientes do Exchange ActiveSync (EAS)

Configurar esta funcionalidade elimina a necessidade de introduzir uma combinação de nome de utilizador e palavra-passe na determinadas correio e aplicações do Microsoft Office no seu dispositivo móvel.

Este tópico fornece os requisitos e os cenários suportados para a configuração CBA num dispositivo iOS(Android) dos utilizadores de inquilinos no Office 365 Enterprise, empresas, educação, US Government, China e planos de Datacenters.

Esta funcionalidade está disponível na pré-visualização no Office 365 US Government defesa e Federal planos.




## <a name="microsoft-mobile-applications-support"></a>Suporte de aplicações móveis da Microsoft

| Aplicações | Suporte |
| --- | --- |
| Azure Information Protection aplicação |![Marcar][1] |
| Portal da empresa do Intune |![Marcar][1] |
| Microsoft Teams |![Marcar][1] |
| OneNote |![Marcar][1] |
| OneDrive |![Marcar][1] |
| Outlook |![Marcar][1] |
| Power BI |![Marcar][1] |
| Skype para empresas |![Marcar][1] |
| Word / Excel / PowerPoint |![Marcar][1] |
| Yammer |![Marcar][1] |


## <a name="requirements"></a>Requisitos

A versão de SO do dispositivo tem de ser iOS 9 e acima

Tem de ser configurado um servidor de Federação.  

Microsoft Authenticator é necessário para as aplicações do Office no iOS.  

Para o Azure Active Directory revogar um certificado de cliente, o token do AD FS tem de ter as seguintes afirmações:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (O número de série do certificado de cliente)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (A cadeia para o emissor do certificado de cliente)

Azure Active Directory adiciona estas afirmações para o token de atualização, se estiverem disponíveis, o token do AD FS (ou qualquer outro token SAML). Quando o token de atualização tem de ser validada, estas informações são utilizadas para verificar a revogação.

Como melhor prática, deve atualizar as páginas de erro do AD FS com o seguinte:

* O requisito para instalar o Microsoft Authenticator em dispositivos iOS
* Instruções sobre como obter um certificado de utilizador.

Para obter mais detalhes, consulte [personalizar as páginas de AD FS início de sessão](https://technet.microsoft.com/library/dn280950.aspx).

Algumas aplicações do Office (com a autenticação moderna ativado) enviar '*perguntar = o início de sessão*' para o Azure AD no seu pedido. Por predefinição, os do Azure AD isto traduz no pedido para AD FS para '*wauth = usernamepassworduri*' (pede-lhe ADFS para efetuar a autenticação de U/P) e '*wfresh = 0*' (pede-lhe ADFS ignorar o estado SSO e efetuar uma autenticação de raiz). Se pretender ativar a autenticação baseada em certificado para estas aplicações, terá de modificar o comportamento predefinido do AD do Azure. Basta definir o '*PromptLoginBehavior*'nas definições do domínio federado para'*desativado*'.
Pode utilizar o [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) cmdlet para efetuar esta tarefa:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`


## <a name="exchange-activesync-clients-support"></a>Suporte de clientes do Exchange ActiveSync
No iOS 9 ou posterior, o cliente de correio iOS nativo é suportado. Para todas as outras aplicações do Exchange ActiveSync, para determinar se esta funcionalidade é suportada, contacte o programador da aplicação.  


## <a name="next-steps"></a>Passos seguintes

Se pretender configurar a autenticação baseada em certificado no seu ambiente, consulte [introdução à autenticação baseada em certificado no Android](active-directory-certificate-based-authentication-get-started.md) para obter instruções.


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
