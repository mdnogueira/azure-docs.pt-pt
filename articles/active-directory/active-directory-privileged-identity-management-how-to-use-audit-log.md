---
title: Como utilizar o registo de auditoria no Azure AD Privileged Identity Management | Microsoft Docs
description: "Saiba como utilizar o registo de auditoria na extensão do Azure Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 5d13a6dd-1fcb-4e76-82fb-cb2f4f0e4357
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 7d9a5255a64d46c1388d328a606b3f297d61262b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-audit-log-in-pim"></a>Utilizar o registo de auditoria no PIM
Pode utilizar o registo de auditoria de Privileged Identity Management (PIM) para ver todas as atribuições de utilizador e ativações dentro de um determinado período de tempo. Se pretender ver o histórico de auditoria completo de atividade no seu inquilino, incluindo o administrador, o utilizador final e atividade de sincronização, pode utilizar o [relatórios de acesso e utilização do Azure Active Directory.](active-directory-view-access-usage-reports.md)

## <a name="navigate-to-the-audit-log"></a>Navegue para o registo de auditoria
Do [portal do Azure](https://portal.azure.com) dashboard, selecione o **do Azure AD Privileged Identity Management** aplicação. A partir daí, aceder ao registo de auditoria clicando **gerir funções privilegiadas** > **histórico de auditorias** no dashboard do PIM.

## <a name="the-audit-log-graph"></a>O gráfico de registo de auditoria
Pode utilizar o registo de auditoria para ver o total de ativações, ativações máx. por dia e ativações médias diário num gráfico de linha.  Também pode filtrar os dados por função, se existir mais de uma função no histórico de auditoria.

Utilize o **tempo**, **ação**, e **função** botões para ordenar o registo.

## <a name="the-audit-log-list"></a>A lista de registo de auditoria
As colunas na lista de registo de auditoria são:

* **Requerente** -o utilizador que pediu a ativação da função ou a alteração.  Se o valor for "Azure sistema", verifique o registo de auditoria do Azure para obter mais informações.
* **Utilizador** -o utilizador que é atribuído a uma função ou a ativação.
* **Função** -a função atribuído ou ativados pelo utilizador.
* **Ação** - as ações executadas pelo requerente. Isto pode incluir a atribuição, anulação, ativação ou desativação.
* **Tempo** - quando ocorreu a ação.
* **Reasoning** -se a qualquer texto foi introduzido no campo motivo durante a ativação, irá aparecer aqui.
* **Expiração** - apenas relevantes para a ativação das funções.

## <a name="filter-the-audit-log"></a>Filtre o registo de auditoria
Pode filtrar as informações que aparecem no registo de auditoria, clicando a **filtro** botão.  O **painel de parâmetros de gráfico de atualização** será apresentada.

Depois de definir os filtros, clique em **atualização** para filtrar os dados no registo.  Se os dados não aparecem imediatamente, atualize a página.

### <a name="change-the-date-range"></a>Alterar o intervalo de datas
Utilize o **hoje**, **na última semana**, **último mês**, ou **personalizada** botões para alterar o intervalo de tempo do registo de auditoria.

Quando escolhe o **personalizada** botão, terá um **de** campo de data e uma **para** campo de data para especificar um intervalo de datas para o registo.  Pode introduzir as datas no formato DD/MM/AAAA ou clique em de **calendário** ícone e escolha a data a partir de um calendário.

### <a name="change-the-roles-included-in-the-log"></a>Alterar as funções incluídas no registo
Verifique ou desmarque o **função** caixa de verificação junto a cada função para incluir ou excluir o registo.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

