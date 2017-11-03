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
ms.openlocfilehash: db849cd1e9da634064f79fbc041098542580ad02
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Repor o licenciamento requisitos de palavra-passe self-service do Azure AD

Por ordem para a reposição de palavra-passe do Azure AD para a função, **tem de ter pelo menos uma licença atribuída na sua organização**. Não impormos licenciamento a experiência de reposição de palavra-passe por utilizador. Para manter a conformidade com o contrato de licenciamento da Microsoft, terá de atribuir licenças para os utilizadores que utilizam as funcionalidades premium.

* **Os utilizadores apenas na nuvem** -Office 365 (O365) qualquer paga SKU ou do Azure AD Basic
* **Nuvem** e/ou **os utilizadores no local** -Azure AD Premium P1 P2, Enterprise Mobility + segurança (EMS) ou o Secure produtivos Enterprise (es)

## <a name="licenses-required-for-password-writeback"></a>Licenças necessárias para a repetição de escrita de palavras-passe

Para utilizar a repetição de escrita de palavras-passe, tem de ter uma das seguintes licenças atribuídas no seu inquilino.

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!WARNING]
> Autónomo Office 365 licenciamento planos **não suportam a repetição de escrita de palavras-passe** e necessitam de um dos planos anteriores para esta funcionalidade funcione.

Podem encontrar informações de licenciamento adicionais, incluindo os custos das páginas seguintes

* [Site de preços do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/)
* [Capacidades e funcionalidades do Active Directory do Azure](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Ativar o grupo ou com base no utilizador licenciamento

Azure AD agora suporta baseado no grupo administradores que permite atribuir licenças em massa a um grupo de utilizadores de licenciamento, em vez de atribuir um de cada vez. [Atribuir, certifique-se e resolver problemas com licenças](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

Alguns serviços da Microsoft não estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, o administrador tem de especificar a propriedade de "Localização de utilização" no utilizador. Atribuição de licenças pode ser efetuada em utilizador > perfil > da secção definições no portal do Azure. **Quando utilizar a atribuição do grupo de licenças, os utilizadores sem uma localização de utilização especificada herdam a localização do diretório.**

## <a name="next-steps"></a>Passos seguintes

* [Como concluir a uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [O registo para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md).
* [Os dados que são utilizados pelo SSPR e os dados que deve preencher para os seus utilizadores?](active-directory-passwords-data.md)
* [Os métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que motivo importantes acerca do mesmo?](active-directory-passwords-writeback.md)
* [Como comunicar na atividade na SSPR](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que fazer significa?](active-directory-passwords-how-it-works.md)
* [Julgo que algo está danificado. Como posso resolver problemas SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi abrangida algures senão](active-directory-passwords-faq.md)
