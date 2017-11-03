---
title: "Sincronização do Azure AD Connect: Ativar Reciclagem AD | Microsoft Docs"
description: "Este tópico recomenda a utilização da funcionalidade de reciclagem do AD com o Azure AD Connect."
services: active-directory
keywords: "Reciclagem do AD, a eliminação acidental, âncora de origem"
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: eb455477547f3db8245cf3601576eba9c6fdc56f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Sincronização do Azure AD Connect: Ativar Reciclagem do AD
Recomenda-se que ative a funcionalidade de reciclagem do AD para os diretórios de Active Directory no local, que são sincronizados com o Azure AD. 

Caso o tenha eliminado acidentalmente um local objeto de utilizador do AD e restaure-la utilizando a funcionalidade, do Azure AD restaura o objeto de utilizador do Azure AD correspondente.  Para obter informações sobre a funcionalidade de reciclagem do AD, consulte o artigo [descrição geral do cenário para restaurar eliminar objetos do Active Directory](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Benefícios da ativação da Reciclagem do AD
Esta funcionalidade ajuda-o com o restauro de objetos de utilizador do Azure AD da seguinte forma:

* Caso o tenha eliminado acidentalmente um local objeto de utilizador do AD, o objeto de utilizador do Azure AD correspondente será eliminado no próximo ciclo de sincronização. Por predefinição, o Azure AD mantém o eliminado objeto de utilizador do Azure AD no estado de eliminação de forma recuperável durante 30 dias.

* Se tiver no local ativada a funcionalidade reciclagem de reciclagem do AD, pode restaurar o eliminado no local o objeto de utilizador do AD sem alterar o valor de âncora de origem. Quando a recuperado no local o objeto de utilizador do AD está sincronizado com o Azure AD, o Azure AD será objeto de utilizador de restauro do correspondente eliminados de forma recuperável do Azure AD. Para informações sobre o atributo âncora de origem, consulte o artigo [do Azure AD Connect: conceitos de Design](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Se não tiver no local ativada a funcionalidade de reciclagem do AD, poderá ser necessária para criar um objeto de utilizador do AD para substituir o objeto foi eliminado. Se o serviço de sincronização ligar do Azure AD está configurado para utilizar o atributo de AD gerado pelo sistema (tais como ObjectGuid) para o atributo âncora de origem, o objeto de utilizador recentemente criado do AD não terão o mesmo valor de âncora de origem que o objeto de utilizador eliminado do AD. Quando o objeto de utilizador do AD recém-criado está sincronizado com o Azure AD, do Azure AD cria um novo objeto de utilizador do Azure AD em vez de restaurar a eliminação de forma recuperável objeto de utilizador do Azure AD.

> [!NOTE]
> Por predefinição, do Azure AD mantém eliminar objetos de utilizador do Azure AD no estado de eliminação de forma recuperável durante 30 dias antes de estes sejam eliminados permanentemente. No entanto, os administradores podem acelerar a eliminação desses objetos. Assim que os objetos são eliminados definitivamente, já não podem ser recuperados, mesmo quando no local é ativada a funcionalidade de reciclagem do AD.



## <a name="next-steps"></a>Passos seguintes
**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
