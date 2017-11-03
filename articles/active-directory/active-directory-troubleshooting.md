---
title: "Resolução de problemas: O item de 'Do Active Directory' está em falta ou não está disponível | Microsoft Docs"
description: "O que fazer quando o item de menu do Active Directory não aparece no Portal de gestão do Azure."
services: active-directory
documentationcenter: na
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.openlocfilehash: be3a797c4a405fd2f6636e67f4c961dd6d143486
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Resolução de problemas: O item de 'Do Active Directory' está em falta ou não está disponível
Muitas das instruções para utilizar os serviços e funcionalidades do Azure Active Directory começam por "Ir para o Portal de gestão do Azure e clique em **do Active Directory**." Mas o que pode fazer se o item de menu ou a extensão do Active Directory não aparecer, ou se estiver marcado **não está disponível**? Este tópico foi concebido para ajudar. Descreve as condições sob as quais **do Active Directory** não aparecer ou não está disponível e explica como proceder.

## <a name="active-directory-is-missing"></a>O Active Directory está em falta
Normalmente, um **do Active Directory** item é apresentado no menu de navegação esquerdo. As instruções nos procedimentos de Azure Active Directory partem do princípio de que este item é na vista.

![Captura de ecrã: Active Directory no Azure](./media/active-directory-troubleshooting/typical-view.png)

O item do Active Directory é apresentada no menu de navegação esquerdo quando qualquer uma das seguintes condições é verdadeiro. Caso contrário, o item não aparecer.

* O utilizador atual sessão iniciada com uma conta Microsoft (anteriormente conhecida como um Windows Live ID).
  
    OU
* O inquilino do Azure tem um diretório e da conta atual é um administrador do diretório.
  
    OU
* O inquilino do Azure tem, pelo menos, um espaço de nomes do controlo de acesso do Azure AD (ACS). Para obter mais informações, consulte [espaço de nomes de controlo de acesso](https://msdn.microsoft.com/library/azure/gg185908.aspx).
  
    OU
* O inquilino do Azure tem pelo menos um fornecedor de multi-factor Authentication do Azure. Para obter mais informações, consulte [administrar o Azure multi-factor Authentication fornecedores](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Para criar um espaço de nomes do controlo de acesso ou um fornecedor de multi-factor Authentication, clique em **+ novo** > **serviços aplicacionais** > **do Active Directory**.

Para obter os direitos administrativos para um diretório, solicite a um administrador atribuir uma função de administrador à sua conta. Para obter mais informações, consulte [atribuir funções de administrador](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory não está disponível
Ao clicar em **+ novo** > **serviços aplicacionais**, um **do Active Directory** item é apresentado. Especificamente, o item do Active Directory é apresentado quando qualquer uma das funcionalidades do Active Directory, tais como o diretório, o controlo de acesso ou o fornecedor do multi-factor Auth, estão disponíveis ao utilizador atual.

No entanto, enquanto a página está a carregar, o item está desativado e está marcado como **não está disponível**. Este é um estado temporário. Se Aguarde alguns segundos, o item fica disponível. Se o atraso é prolongado, atualizar, muitas vezes, a página web resolve o problema.

![Captura de ecrã: Active Directory não está disponível](./media/active-directory-troubleshooting/not-available.png)

