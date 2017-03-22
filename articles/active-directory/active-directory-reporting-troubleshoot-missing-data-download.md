---
title: "Resolução de problemas: dados em falta nos registos de atividades transferidos do Azure Active Directory - pré-visualização | Microsoft Docs"
description: "Fornece uma resolução para os dados em falta na pré-visualização de registos de atividades transferidos do Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: e0d65edcb7c14114565402038b0958c3a2ffb477
ms.lasthandoff: 03/09/2017


---

# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Não consigo encontrar dados nos registos de atividades do Azure Active Directory que transferi


## <a name="symptoms"></a>Sintomas

Transferi os registos de atividades (auditorias ou inícios de sessão) e não vejo todos os registos para o período de tempo que escolhi. Porquê? 

 ![Relatórios](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Causa

Quando transfere registos de atividades no portal do Azure, limitamos o tamanho a 120K registos, ordenados pelos mais recentes. 

## <a name="resolution"></a>Resolução

Pode tirar partido das [APIs de Relatórios do Azure AD](active-directory-reporting-api-getting-started.md) para obter até um milhão de registos num determinado período. A nossa abordagem recomendada é executar um script agendado que liga às APIs de relatórios para obter registos de uma forma incremental durante um período de tempo (por exemplo, diária ou semanalmente).

## <a name="next-steps"></a>Passos seguintes
Veja as [FAQ de relatórios do Azure Active Directory](active-directory-reporting-faq.md).


