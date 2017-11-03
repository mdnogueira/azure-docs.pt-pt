---
title: "Operações de Gestor do serviço de sincronização do Azure AD Connect | Microsoft Docs"
description: "Compreenda o separador de operações no Gestor de serviço de sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a1475e4fcd11eb008badba49665f4af6029a1697
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Utilizando o separador de operações de Gestor do serviço de sincronização

![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

No separador operações mostra os resultados das operações mais recentes. Este separador é importante para compreender e resolver problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Compreender as informações visíveis no separador de operações
A metade superior mostra todas as execuções por ordem cronológica. Por predefinição, as operações de registo mantém informações sobre os últimos sete dias, mas esta definição pode ser alterada com o [programador](active-directory-aadconnectsync-feature-scheduler.md). Pretende procurar qualquer execução que mostram um Estado de êxito. Pode alterar a ordenação clicando os cabeçalhos.

O **estado** coluna são as informações mais importantes e mostra o problema mais grave para execução. Eis um resumo rápido dos Estados mais comuns por ordem de prioridade para investigar (onde * indicar vários cadeias de erro possíveis).

| Estado | Comentário |
| --- | --- |
| parado-* |Não foi possível concluir a execução. Por exemplo, se o sistema remoto não está disponível e não pode ser contactado. |
| parado-erro-limite |Existem mais de 5.000 erros. A executar automaticamente foi parada devido ao elevado número de erros. |
| concluída -\*-erros |A execução foi concluída, mas existem erros (menos de 5000) que devem ser investigados. |
| concluída -\*-avisos |A execução foi concluída, mas alguns dados não está no estado esperado. Se tiver de erros, em seguida, esta mensagem é, normalmente, apenas um sintoma. Até ter resolvidas erros, não deve investigar avisos. |
| exito |Não existem problemas. |

Quando seleciona uma linha, na parte inferior atualizações para mostrar os detalhes do que são executados. A extremidade esquerda da parte inferior, poderá ter uma indicação de lista **passo n. º**. Esta lista aparece apenas se tiver vários domínios na floresta onde cada domínio é representado por um passo. O nome de domínio pode ser encontrado no cabeçalho **partição**. Em **estatísticas de sincronização**, pode encontrar mais informações sobre o número de alterações que foram processados. Pode clicar nas hiperligações para obter uma lista dos objetos alterados. Se tiver de objetos com erros, esses surgem erros em **erros de sincronização**.

Para obter mais informações, consulte [resolver problemas de um objeto que não está a sincronizar](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
