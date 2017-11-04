---
title: "Notificações de proteção de identidade do Active Directory do Azure | Microsoft Docs"
description: "Saiba como notificações suportam as atividades de investigação."
services: active-directory
keywords: "proteção de identidade do Azure Active Directory, o cloud app discovery, gestão de aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: abc0f3926905295a9cf239146cce7fc57da7eb29
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações de proteção de identidade do Active Directory do Azure
Azure AD Identity Protection envia dois tipos de e-mails de notificação automatizada para o ajudar a gerir o risco de utilizador e os eventos de risco:

* Utilizador comprometido e-mail de alerta
* E-mail de texto implícita semanal

## <a name="user-compromised-alert-email"></a>Utilizador comprometido e-mail de alerta
É gerado um alerta de e-mail comprometido de utilizador quando o Azure AD Identity Protection identifica uma conta como comprometido. Mensagem de correio eletrónico inclui uma ligação para os utilizadores sinalizados para o relatório de risco no dashboard do Identity Protection. Recomendamos que investigue imediatamente a notificações de contas comprometidas.

## <a name="weekly-digest-email"></a>E-mail de texto implícita semanal
O e-mail de texto implícita semanal contém um resumo dos novos eventos de risco.<br>
Inclui:

* Utilizadores em risco
* Atividades suspeitas
* Vulnerabilidades detetadas
* Ligações para os relatórios relacionados no Identity Protection

<br>
![Remediação](./media/active-directory-identityprotection-notifications/400.png "remediação")
<br>

Pode mudar a enviar um e-mail de texto implícita semanal.
<br><br>
![Riscos de utilizador](./media/active-directory-identityprotection-notifications/62.png "riscos de utilizador")
<br>

**Para abrir a caixa de diálogo de configuração relacionados**:

1. No **do Azure AD Identity Protection** painel, clique em **definições**.
   <br><br>
   ![Política de risco utilizador](./media/active-directory-identityprotection-notifications/401.png "política de risco do utilizador")
   <br>
2. No **geral** secção, clique em **notificações**.
   <br><br>
   ![Política de risco utilizador](./media/active-directory-identityprotection-notifications/405.png "política de risco do utilizador")
   <br>

## <a name="see-also"></a>Consultar também
* [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md)
