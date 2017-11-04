---
title: "Proteção de identidade do Azure Active Directory FAQ | Microsoft Docs"
description: Perguntas mais frequentes sobre o Azure AD Identity Protection
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
ms.date: 11/03/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 3d0505caf75a3e6433e2c9fd9795b3176d66d297
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="azure-active-directory-identity-protection-faq"></a>Proteção de identidade do Azure Active Directory FAQ

Este artigo inclui as respostas a perguntas mais frequentes sobre proteção de identidade do Azure Active Directory (Azure AD). Para obter mais informações, consulte [do Azure Active Directory Identity Protection](active-directory-identityprotection.md). 


## <a name="why-do-some-risk-events-have-closed-system-status"></a>Por que razão é que alguns eventos de risco tem o estado de "Fechado (sistema)"?

**R:** estes são os eventos que o Azure Active Directory Identity Protection detetado e mais tarde fechado porque os eventos foram já não considera duvidosos. Estes eventos não contam para o nível de risco do utilizador. 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>É necessário ser um administrador global para utilizar a proteção de identidade no portal do Azure?
**R:** Não. Pode ser um leitor de segurança, um administrador de segurança ou um Administrador Global para utilizar a proteção de identidade.

---

## <a name="how-do-i-get-identity-protection"></a>Como devo proceder para proteção de identidade?

**R:** consulte [introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md) para uma resposta a esta pergunta.

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>Como pode ordenar os utilizadores "Utilizadores sinalizados para risco"?

**R:** transferir os utilizadores sinalizados para o relatório de risco, clicando no **transferir** do **utilizadores sinalizados para risco** página. Em seguida, pode ordenar os dados transferidos com base nos campos disponíveis, incluindo atualizado último (UTC).

---
