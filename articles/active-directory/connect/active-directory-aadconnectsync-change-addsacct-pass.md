---
title: "Sincronização do Azure AD Connect: alterar a palavra-passe de conta do AD DS | Microsoft Docs"
description: "Este documento de tópico descreve como atualizar do Azure AD Connect após a palavra-passe da conta do AD DS é alterada."
services: active-directory
keywords: Conta do AD DS, a conta do Active Directory, a palavra-passe
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 14e16a238e60ecfeeb3cbf88c3922a79349dcc75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="changing-the-ad-ds-account-password"></a>Alterar a palavra-passe de conta do AD DS
A conta do AD DS refere-se à conta de utilizador utilizada pelo Azure AD Connect para comunicar com o Active Directory no local. Se alterar a palavra-passe da conta do AD DS, tem de atualizar o serviço de sincronização ligar do Azure AD com a nova palavra-passe. Caso contrário, a sincronização já não pode sincronizar corretamente com o Active Directory no local e irá encontrar os seguintes erros:

* Na operação Synchronization Service Manager, qualquer importação ou exportação no local AD falha com **credenciais de início não** erro.

* No Visualizador de eventos do Windows, o registo de eventos da aplicação contém um erro com **6000 de ID de evento** e mensagem **'o agente de gestão "contoso.com" não conseguiu executar porque as credenciais foram inválidas '**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Como atualizar o serviço de sincronização com a nova palavra-passe para a conta do AD DS
Para atualizar o serviço de sincronização com a nova palavra-passe:

1. Inicie o Synchronization Service Manager (serviço de sincronização do início →).
</br>![Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  

2. Vá para o **conectores** separador.

3. Selecione o **conector AD** que corresponde à conta do AD DS para o qual a respetiva palavra-passe foi alterada.

4. Em **ações**, selecione **propriedades**.

5. Na caixa de diálogo de pop-up, selecione **ligar à floresta do Active Directory**:

6. Introduza a nova palavra-passe da conta do AD DS no **palavra-passe** caixa de texto.

7. Clique em **OK** para guardar a nova palavra-passe e fechar a caixa de diálogo de pop-up.

8. Reinicie o Azure AD Connect serviço de sincronização em Gestor de controlo de serviço do Windows. Isto é para se certificar de que qualquer referência a palavra-passe antiga é removida da cache de memória.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Noções e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
