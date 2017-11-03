---
title: "Sincronização do Azure AD Connect: como gerir a conta de serviço do Azure AD | Microsoft Docs"
description: "Este tópico documenta como restaurar a conta de serviço do Azure AD."
services: active-directory
keywords: "AADSTS70002, AADSTS50054, como repor a palavra-passe para a conta de serviço do conector de sincronização do Azure AD Connect"
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 8e9e8192ee4fcb636b5be91d2616acbc9120c8c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Sincronização do Azure AD Connect: como gerir a conta de serviço do Azure AD
A conta de serviço utilizada pelo conector do Azure AD deveria para ser serviço gratuito. Se precisar de repor as respetivas credenciais, em seguida, este tópico é que o utilizador. Por exemplo, se um Administrador Global tiver por erro repor a palavra-passe da conta de serviço com o PowerShell.

## <a name="reset-the-credentials"></a>Repor as credenciais
Se a conta de serviço definida no conector Azure AD não consegue contactar o Azure AD devido a problemas de autenticação, é possível repor a palavra-passe.

1. Inicie sessão no servidor de sincronização do Azure AD Connect e inicie o PowerShell.
2. Execute `Add-ADSyncAADServiceAccount`.  
   ![Addadsyncaadserviceaccount de cmdlet do PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Forneça as credenciais de Administrador Global do Azure AD.

Este cmdlet repõe a palavra-passe da conta de serviço e atualizá-lo no Azure AD e no motor de sincronização.

## <a name="known-issues-these-steps-can-solve"></a>Estes passos podem resolver os problemas conhecidos
Esta secção se uma lista de erros comunicados pelos clientes que foram corrigidos por um credenciais repor a conta de serviço do Azure AD.

- - -
Evento 6900  
O servidor encontrou um erro inesperado ao processar uma notificação de alteração de palavra-passe:  
AADSTS70002: Erro a validar as credenciais. AADSTS50054: Palavra-passe antiga é utilizado para autenticação.

- - -
Evento 659  
Erro ao obter a configuração de sincronização da política de palavra-passe. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Erro a validar as credenciais. AADSTS50054: Palavra-passe antiga é utilizado para autenticação.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)

