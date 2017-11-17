---
title: 'Licenciamento: O Azure AD SSPR | Microsoft Docs'
description: Azure AD self-service palavra-passe repor os requisitos de licenciamento
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: b65a4e49097828e7cd54a29e814befd2d2ac5d88
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Repor o licenciamento requisitos de palavra-passe self-service do Azure AD

Ordem de palavra-passe do Azure Active Directory (Azure AD) Repor para a função, *tem de ter pelo menos uma licença atribuída na sua organização*. Iremos não impõem licenciamento a experiência de reposição de palavra-passe por utilizador. Para manter a conformidade com o contrato de licenciamento da Microsoft, terá de atribuir licenças para os utilizadores que utilizam as funcionalidades premium.

* **Os utilizadores apenas na nuvem**: do Office 365 qualquer paga SKU ou do Azure AD Basic
* **Nuvem** ou **os utilizadores no local**: Azure AD Premium P1 P2, Enterprise Mobility + segurança (EMS) ou o Secure produtivos Enterprise (es)

## <a name="licenses-required-for-password-writeback"></a>Licenças necessárias para a repetição de escrita de palavras-passe

Para utilizar a repetição de escrita de palavras-passe, tem de ter uma das seguintes licenças atribuídas no seu inquilino:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 (plano E3)
* Microsoft 365 (plano E5)

> [!WARNING]
> Autónomo Office 365 licenciamento planos *não suportam a repetição de escrita de palavras-passe* e requerem que tenha um dos planos anteriores para esta funcionalidade funcione.

Podem encontrar informações de licenciamento adicionais, incluindo os custos, nas seguintes páginas:

* [Azure Active Directory preços do site](https://azure.microsoft.com/pricing/details/active-directory/)
* [Capacidades e funcionalidades do Active Directory do Azure](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Ativar o grupo ou com base no utilizador licenciamento

Azure AD agora suporta o licenciamento baseadas em grupos. Os administradores podem atribuir licenças em massa a um grupo de utilizadores, em vez de atribuir um de cada vez. Para obter mais informações, consulte [atribuir, certifique-se e resolver problemas com licenças](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).

Alguns serviços da Microsoft não estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, o administrador tem de especificar o **localização de utilização** propriedade no utilizador. Atribuição de licenças pode ser efetuada sob a **utilizador** > **perfil** > **definições** secção no portal do Azure. *Quando utilizar a atribuição do grupo de licenças, os utilizadores sem uma localização de utilização especificada herdam a localização do diretório.*

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

