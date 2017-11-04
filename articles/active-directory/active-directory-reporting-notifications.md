---
title: "Notificações de relatórios do Azure Active Directory"
description: "Como utilizar o Azure Active Directory reporting notificações para início de sessão suspeitos ins."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: e561061cadd88e2c5670e27f2a66ef21002e30b0
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-reporting-notifications"></a>Notificações de relatórios do Azure Active Directory
## <a name="what-reports-generate-email-notifications"></a>Os relatórios geram notificações por e-mail
Neste momento, apenas o início de sessão Irregular acionadores de relatório de atividade notificações por correio eletrónico.

## <a name="what-is-an-irregular-sign-in"></a>O que é um "início de sessão Irregular"?
Dados de inícios de sessão são aqueles que tenham sido identificadas pelo nosso algoritmos do machine learning, on the basis of uma condição de "Impossível" combinado com um dispositivo e localização de início de sessão anómala. Isto pode indicar que um hacker tem sido a tentar iniciar sessão com esta conta.

## <a name="who-receives-the-email-notifications"></a>Quem recebe as notificações de e-mail?
O e-mail é enviado para todos os administradores globais que tenham sido atribuídos uma licença do Active Directory Premium. Para garantir que é entregue, enviar-lhe-lo para os administradores endereço de correio eletrónico alternativo, bem como. Deve incluir Admins aad-alerts-noreply@mail.windowsazure.com na respetiva lista de remetentes seguros, pelo que não perder o e-mail.

## <a name="how-often-are-these-emails-sent"></a>Com que frequência são estes e-mails enviados?
O e-mail é enviado se ocorrem de 10 novas dados início de sessão atividades nos últimos 30 dias ou desde a última mensagem de e-mail foi enviada, o que for menor.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Como posso aceder ao relatório mencionado na mensagem de correio eletrónico?
Ao clicar na ligação, será redirecionado para a página do relatório no portal clássico do Azure. Para poder aceder ao relatório, tem de ser ambas:

* Um administrador ou coadministrador da sua subscrição do Azure
* Um administrador global no diretório e atribuída uma licença do Active Directory Premium. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md).

## <a name="can-i-turn-off-these-emails"></a>Posso desativar estes e-mails?
Sim, para desativar notificações relacionadas com inícios de sessão anómalos no portal clássico do Azure, clique em **configurar**e, em seguida, selecione **desativado** sob o **notificações** secção.

## <a name="whats-next"></a>Passos seguintes
* Curiosidade sobre os relatórios de segurança, auditoria e atividade estão disponíveis? Veja [do Azure AD de segurança, auditoria e de relatórios de atividade](active-directory-view-access-usage-reports.md)
* [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Adicionar a imagem corporativa às suas páginas de Início de Sessão e Painel de Acesso](active-directory-add-company-branding.md)

