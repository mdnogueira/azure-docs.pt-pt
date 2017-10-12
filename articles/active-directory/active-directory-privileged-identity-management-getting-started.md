---
title: "Introdução ao Azure AD Privileged Identity Management | Microsoft Docs"
description: "Saiba como gerir identidades privilegiadas com a aplicação do Azure Active Directory Privileged Identity Management no Portal do Azure."
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 0f9f09ca8fb30d494433ed8d26b808d1b5d4d0b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Comece a utilizar o Azure AD Privileged Identity Management

Com o Azure Active Directory (AD) Privileged Identity Management, pode gerir, controlar e monitorizar o acesso dentro da sua organização. Este âmbito inclui o acesso a recursos do Azure, Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo mostra-lhe como adicionar a aplicação Azure AD PIM ao dashboard do Portal do Azure.

## <a name="add-the-privileged-identity-management-application"></a>Adicionar a aplicação Privileged Identity Management

Antes de utilizar o Azure AD Privileged Identity Management, terá de adicionar a aplicação ao dashboard do Portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/) como administrador global do diretório.
2. Se a organização tiver mais do que um diretório, selecione o nome de utilizador no canto superior direito do Portal do Azure. Selecione o diretório onde pretende utilizar o PIM.
3. Selecione **Mais serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação Privileged Identity Management abre.

Se for a primeira pessoa a utilizar o Azure AD Privileged Identity Management no diretório, são-lhe atribuídas automaticamente as funções **Administrador de segurança** e **Administrador de função com privilégios** no diretório. As atribuições de funções de diretório do Azure AD dos utilizadores só podem ser geridas por administradores de funções com privilégios. Além disso, pode optar por executar o [assistente de segurança](active-directory-privileged-identity-management-security-wizard.md) que o orienta ao longo da experiência inicial de deteção e atribuição.

## <a name="navigate-to-your-tasks"></a>Navegue para as suas tarefas

Assim que o Azure AD Privileged Identity Management estiver configurado, verá o painel de navegação sempre que abrir a aplicação. Utilize este painel para realizar as tarefas de gestão de identidade.

![Tarefas de nível superior para PIM - captura de ecrã](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

- A opção **As Minhas Funções** apresenta uma lista de funções elegíveis e ativas que lhe estão atribuídas. Este é o local onde pode ativar quaisquer funções elegíveis atribuídas.
- A opção **Aprovar Pedidos (Pré-visualização)** apresenta uma lista de pedidos de ativação de funções de diretório do Azure AD elegíveis pelos utilizadores do seu diretório, cuja aprovação está a seu cargo. [Saiba mais.](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
- A opção **Pedidos Pendentes (Pré-visualização)** apresenta qualquer um dos seus pedidos pendentes de ativação de atribuições de funções elegíveis.
- A opção **Rever Acesso** lista as revisões de acesso ativas que tem para concluir, quer esteja a rever o acesso para si próprio ou para outra pessoa.
- A opção **Funções de diretório do Azure AD**, localizada na secção Gerir do menu de navegação esquerdo, apresenta o dashboard para os administradores de funções com privilégios gerirem as atribuições de funções, alterarem as definições de ativação de funções, iniciarem revisões de acesso e muito mais. Este dashboard está desativado para todos aqueles que não são um administrador de funções com privilégios. Estes utilizadores têm acesso a um dashboard especial intitulado A minha vista. O dashboard A minha vista só apresenta informações sobre o utilizador que está a aceder ao dashboard, e não sobre o inquilino completo.
- A opção **Funções de recursos do Azure (Pré-visualização)**, localizada na secção Gerir do menu de navegação esquerdo, apresenta uma lista de recursos de subscrição para os quais tem atribuições de funções. 

## <a name="next-steps"></a>Passos seguintes
A [descrição geral do Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) inclui mais informações sobre como pode gerir o acesso administrativo na organização.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
