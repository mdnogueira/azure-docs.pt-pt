---
title: Introdução ao Azure AD Privileged Identity Management | Microsoft Docs
description: Saiba como gerir identidades privilegiadas com a aplicação do Azure Active Directory Privileged Identity Management no Portal do Azure.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2016
ms.author: kgremban

---
# Introdução ao Azure AD Privileged Identity Management
Com o Azure Active Directory (AD) Privileged Identity Management, pode gerir, controlar e monitorizar o acesso dentro da sua organização. Isto inclui o acesso a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo mostra-lhe como adicionar a aplicação Azure AD PIM ao dashboard do Portal do Azure.

## Adicionar a aplicação Privileged Identity Management
Antes de utilizar o Azure AD Privileged Identity Management, terá de adicionar a aplicação ao dashboard do Portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/) como administrador global do diretório.
2. Se a organização tiver mais do que um diretório, selecione o nome de utilizador no canto superior direito do Portal do Azure. Selecione o diretório onde irá utilizar o PIM.
3. Selecione **Mais serviços** e utilize a caixa de texto Filtro para procurar **Azure AD Privileged Identity Management**.
4. Marque **Afixar ao dashboard** e, em seguida, clique em **Criar**. A aplicação Privileged Identity Management abre.

Se for a primeira pessoa a utilizar o Azure AD Privileged Identity Management no diretório, o [assistente de segurança](active-directory-privileged-identity-management-security-wizard.md) irá guiá-lo na experiência de atribuição inicial. Depois disso, tornar-se-à automaticamente o primeiro **Administrador de segurança** e **Administrador com função privilegiada** no diretório. Apenas um administrador com função privilegiada pode aceder a esta aplicação para gerir o acesso de outros administradores.  

## Navegue para as suas tarefas
Assim que o Azure AD Privileged Identity Management é configurado, verá o painel de navegação sempre que abrir a aplicação. Utilize este painel para realizar as tarefas de gestão de identidade.

![Tarefas de nível superior para PIM - captura de ecrã](./media/active-directory-privileged-identity-management-getting-started/pim_tasks.png)

* **Ativar as minhas funções** leva-o à lista de funções que são atribuídas ao utilizador. É onde irá ativar as funções que lhe são elegíveis.
* **Gerir funções com privilégios** é o dashboard para os administradores de função com privilégios gerirem atribuições de função, alterarem definições de ativação de função, iniciarem revisões de acesso e muito mais. As opções neste dashboard estão desativadas para todas as pessoas que não são um administrador de função com privilégios.
* **Rever acesso privilegiado** leva-o a quaisquer revisões de acesso pendente que necessite de concluir, se estiver a rever o acesso para si ou para outra pessoa. 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passos seguintes
A [descrição geral do Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) inclui mais informações sobre como pode gerir o acesso administrativo na organização.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png



<!--HONumber=Sep16_HO3-->


