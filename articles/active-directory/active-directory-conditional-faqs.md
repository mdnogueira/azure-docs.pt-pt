---
title: Acesso condicional do Azure Active Directory perguntas mais frequentes | Microsoft Docs
description: "Obtenha respostas às perguntas mais frequentes sobre o acesso condicional no Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5eb16a92feee0937edeff1c00e0babe85176a79f
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Acesso condicional do Azure Active Directory perguntas mais frequentes

## <a name="which-applications-work-with-conditional-access-policies"></a>As aplicações que funcionam com as políticas de acesso condicional?

Para obter informações sobre as aplicações que funcionam com as políticas de acesso condicional, consulte [aplicações e os browsers que utilizar regras de acesso condicional no Azure Active Directory](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Políticas de acesso condicional são aplicadas para colaboração B2B e os utilizadores convidados?

As políticas são impostas para utilizadores de colaboração empresa-empresa (B2B). No entanto, em alguns casos, um utilizador não poderá satisfazer os requisitos de política. Por exemplo, a organização de um utilizador convidado não suportem autenticação multifator. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Política do SharePoint Online também aplicar no OneDrive para empresas?

Sim. Política do SharePoint Online também se aplica para o OneDrive para empresas.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Por que motivo não é possível definir uma política em aplicações de cliente, como o Word ou o Outlook?

Uma política de acesso condicional define os requisitos para aceder a um serviço. É imposto quando ocorre a autenticação para esse serviço. A política não está definida diretamente numa aplicação de cliente. Em vez disso, é aplicada quando um cliente chama um serviço. Por exemplo, uma política definida no SharePoint aplica-se aos clientes chamar SharePoint. Uma política definida no Exchange aplica-se ao Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de acesso condicional é aplicável a contas de serviço?

Aplicam políticas de acesso condicional a todas as contas de utilizador. Isto inclui a contas de utilizador que são utilizadas como contas de serviço. Muitas vezes, uma conta de serviço que executa automática não é possível satisfazer os requisitos de uma política de acesso condicional. Por exemplo, autenticação multifator pode ser necessária. As contas de serviço podem ser excluídas da política de uma utilizando as definições de gestão de política de acesso condicional. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>APIs de gráfico está disponível para configurar políticas de acesso condicional?

Atualmente, não. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>O que é a política de exclusão predefinida para plataformas de dispositivos não suportados?

Atualmente, as políticas de acesso condicional seletivamente são impostas nos utilizadores de dispositivos iOS e Android. Aplicações em outras plataformas de dispositivos, por predefinição, não são afetadas pela política de acesso condicional para dispositivos iOS e Android. Um administrador de inquilino pode optar por substituir a política global para não permitir acesso aos utilizadores nas plataformas que não são suportadas.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Como funcionam as políticas de acesso condicional para o Microsoft Teams?

Microsoft Teams baseia-se descontos elevados no Exchange Online e SharePoint Online para cenários de produtividade núcleos, como reuniões, calendários e a partilha de ficheiros. Políticas de acesso condicional que estão definidas para estas aplicações em nuvem se aplicam ao Microsoft Teams quando um utilizador iniciar sessão diretamente no Teams da Microsoft.

Microsoft Teams também é suportado em separado como uma aplicação na nuvem em políticas de acesso condicional do Azure Active Directory. Políticas de acesso condicional que estão definidas para uma aplicação de nuvem aplicam-se ao Microsoft Teams quando um utilizador inicia sessão. No entanto, sem as políticas corretas nas outras aplicações, como o Exchange Online e SharePoint Online utilizadores ainda poderá aceder diretamente a esses recursos.

Os clientes de ambiente de trabalho Teams da Microsoft para o Windows e Mac suportam autenticação moderna. Autenticação moderna inclui o início de sessão com base no Azure Active Directory Authentication Library (ADAL) para aplicações de cliente do Microsoft Office entre plataformas.