---
title: "Proteção do Azure Active Directory identidade - como desbloquear utilizadores | Microsoft Docs"
description: "Saiba como desbloquear utilizadores que foram bloqueados por uma política do Azure Active Directory Identity Protection."
services: active-directory
keywords: "proteção de identidade do Azure Active Directory, desbloquear utilizador"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 0bef2fa17474298f36f360efa49922f5f85b21a1
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Proteção do Azure Active Directory identidade - como desbloquear utilizadores
Com o Azure Active Directory Identity Protection, pode configurar políticas para bloquear os utilizadores se forem satisfeitas as condições configuradas. Normalmente, um utilizador bloqueado contactos técnico fiquem desbloqueado. Este tópicos explica os passos que pode efetuar para desbloquear um utilizador bloqueado.

## <a name="determine-the-reason-for-blocking"></a>Determinar a razão para bloquear
Como primeiro passo para desbloquear um utilizador, terá de determinar o tipo de política que bloqueou o utilizador porque os passos seguintes são consoante no mesmo.
Com o Azure Active Directory Identity Protection, um utilizador pode ser está bloqueado por uma política de início de sessão risco ou uma política de risco do utilizador.

Pode obter o tipo de política que bloqueou um utilizador de cabeçalho na caixa de diálogo que foi apresentada ao utilizador durante a tentativa de início de sessão:

| Política | Caixa de diálogo utilizador |
| --- | --- |
| Risco de início de sessão |![O início de sessão bloqueado](./media/active-directory-identityprotection-unblock-howto/02.png) |
| Risco de utilizador |![Conta bloqueada](./media/active-directory-identityprotection-unblock-howto/104.png) |

Um utilizador que está bloqueado por:

* Uma política de início de sessão risco também é conhecido como o início de sessão suspeito
* Uma política de risco de utilizador também é conhecido como uma conta em risco

## <a name="unblocking-suspicious-sign-ins"></a>Desbloqueio suspeitos inícios de sessão
Para desbloquear um suspeito início de sessão, tem as seguintes opções:

1. **Início de sessão de um dispositivo ou localização familiar** -um motivo comum bloqueados suspeitos inícios de sessão são tentativas de início de sessão de localizações desconhecidas ou dispositivos. Os utilizadores rapidamente podem determinar se é este o motivo do bloqueio por tentativa de início de sessão de um dispositivo ou localização familiar.
2. **Excluir da política** - se considera que a configuração atual da sua política de início de sessão está a causar problemas para utilizadores específicos, pode excluir os utilizadores do mesmo. Consulte [do Azure Active Directory Identity Protection](active-directory-identityprotection.md) para obter mais detalhes.
3. **Desativar política** - se considera que a configuração de política está a causar problemas de todos os seus utilizadores, pode desativar a política. Consulte [do Azure Active Directory Identity Protection](active-directory-identityprotection.md) para obter mais detalhes.

## <a name="unblocking-accounts-at-risk"></a>Desbloqueio contas em risco
Para desbloquear uma conta em risco, tem as seguintes opções:

1. **Repor palavra-passe** -pode repor a palavra-passe do utilizador. Consulte [reposição de palavra-passe segura manual](active-directory-identityprotection.md#manual-secure-password-reset) para obter mais detalhes.
2. **Ignore todos os eventos de risco** - os blocos de política do utilizador risco um utilizador se o utilizador configurado o risco de nível para bloquear o acesso foi atingido. Pode reduzir a um utilizador do nível de risco fechando manualmente comunicadas eventos de risco. Para obter mais detalhes, consulte [fechar os eventos de risco manualmente](active-directory-identityprotection.md#closing-risk-events-manually).
3. **Excluir da política** - se considera que a configuração atual da sua política de início de sessão está a causar problemas para utilizadores específicos, pode excluir os utilizadores do mesmo. Consulte [do Azure Active Directory Identity Protection](active-directory-identityprotection.md) para obter mais detalhes.
4. **Desativar política** - se considera que a configuração de política está a causar problemas de todos os seus utilizadores, pode desativar a política. Consulte [do Azure Active Directory Identity Protection](active-directory-identityprotection.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
 Pretende saber mais sobre o Azure AD Identity Protection? Veja [do Azure Active Directory Identity Protection](active-directory-identityprotection.md).
