---
title: "Resolução de problemas: dados em falta nos registos de atividades transferidos do Azure Active Directory | Microsoft Docs"
description: "Fornece uma resolução para os dados em falta nos registos de atividades transferidos do Azure Active Directory."
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
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 28bf4eb46f18bf0a9b2cd8b1e7058eccfa12a88f
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
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

