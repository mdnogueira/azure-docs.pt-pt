---
title: "Como executar uma revisão de acesso | Microsoft Docs"
description: "Saiba como executar uma revisão à aplicação Azure Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 49ee2feb-7d2e-4acf-82c1-40ff23062862
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a98ed60221eeba1d9c92df846aeae2deafb8ae60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>Como executar uma revisão de acesso no Azure AD Privileged Identity Management
O Azure Active Directory (AD) Privileged Identity Management simplifica como às empresas gerem o acesso privilegiado para recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.  

Se forem atribuídos a uma função administrativa, o administrador com função privilegiada da sua organização poderá pedir-lhe para confirmar regularmente se ainda precisar dessa função a tarefa. Poderá obter um e-mail que inclui uma ligação ou, pode ir diretamente para o [portal do Azure](https://portal.azure.com). Siga os passos neste artigo para efetuar um rever automática das suas funções atribuídas.

Se a um administrador com função privilegiada interessado em revisões de acesso, obtenha mais detalhes em [como iniciar uma revisão do acesso](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Adicionar a aplicação Privileged Identity Management
Pode utilizar a aplicação do Azure AD Privileged Identity Management (PIM) a [portal do Azure](https://portal.azure.com/) para efetuar a revisão.  Se não tiver a aplicação Azure AD Privileged Identity Management no seu portal, siga estes passos para começar a utilizar.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Selecione o seu nome de utilizador no canto superior direito do portal do Azure e selecione o diretório onde irá a estar a funcionar.
3. Selecione **Mais serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação de Privileged Identity Management será aberta.

## <a name="approve-or-deny-access"></a>Aprovar ou negar o acesso
Quando aprovar ou negar o acesso, é estiver apenas a informar o revisor se, continuar a utilizar esta função ou não. Escolha **aprovar** se pretender permaneça na função, ou **negar** se não precisa de acesso já. O estado não mudará de imediato, até que o revisor aplica-se os resultados.
Siga estes passos para localizar e concluir a revisão do acesso:

1. Na aplicação do PIM, selecione **revisão acesso privilegiado**. Se tiver quaisquer revisões pendentes acesso, aparecem no painel de revisões de acesso do Azure AD.
2. Selecione a revisão que pretende concluir.
3. A menos que criou a revisão, apresentado como o único utilizador na revisão. Selecione a marca de verificação junto ao seu nome.
4. Escolha o **aprovar** ou **negar**. Poderá ter de incluir um motivo para a sua decisão no **de indicar um motivo** caixa de texto.  
5. Fechar o **funções de revisão do Azure AD** painel.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
