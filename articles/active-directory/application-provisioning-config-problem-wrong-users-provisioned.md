---
title: "Errado conjunto de utilizadores estão a ser aprovisionado para uma aplicação de galeria do Azure AD | Microsoft Docs"
description: "Saiba como saber por que razão a ser aprovisionados um conjunto diferente de utilizadores a uma aplicação que as que o esperado"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 85b533584c8ec15a23be32e20db7de583fced6a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Errado conjunto de utilizadores estão a ser aprovisionado para uma aplicação de galeria do Azure AD

Os utilizadores que são aprovisionados para a aplicação é principalmente condicionados pelos quais os utilizadores e grupos foram **atribuído** à aplicação.

Utilize os recursos abaixo para saber como verificar quais os utilizadores e grupos tenham sido atribuídos a uma aplicação do Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Atribuir um utilizador diretamente como um administrador

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de correio eletrónico** do utilizador que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **utilizador** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do utilizador ou logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais do que um utilizador**, tipo noutra **nome completo** ou **endereço de correio eletrónico** para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação para os utilizadores selecionados.

Se o aprovisionamento é configurada e já em execução para uma aplicação, os novos utilizadores devem ser aprovisionados para uma aplicação dentro de, aproximadamente, 10 minutos. Verifique o **registos de auditoria** para obter mais detalhes.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Atribuir um grupo diretamente a uma aplicação como um administrador

Para atribuir um ou mais grupos diretamente a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome do grupo completa** do grupo que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **grupo** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais de um grupo**, tipo noutra **nome do grupo completa** para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este grupo para o **selecionados** lista.

13. Quando tiver terminado de selecionar grupos, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação em grupos selecionados.

Se o aprovisionamento é configurada e já em execução para uma aplicação, os novos utilizadores contidos no grupo de devem ser aprovisionados para uma aplicação dentro de, aproximadamente, 10 minutos. Verifique o **registos de auditoria** para obter mais detalhes.

>[!IMPORTANT]
>Aprovisionamento do nome do grupo e detalhes de grupo, para além de membros, caso seja suportado para algumas aplicações. Pode ativar ou desativar esta funcionalidade ativando ou desativando a **mapeamento** para objetos de grupo apresentados no **aprovisionamento** separador. 
>
>

Se estiver ativado a grupos de aprovisionamento, lembre-se de que reveja os mapeamentos de atributos para garantir que está a ser utilizado um campo adequado para o "ID correspondente". Isto pode ser o nome a apresentar ou e-mail alias, como o grupo e os seus membros não aprovisionadas se a propriedade correspondente está vazia ou não preenchido para um grupo no Azure AD.

## <a name="next-steps"></a>Passos seguintes
[Automatizar utilizador aprovisionamento e desaprovisionamento para aplicações SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)
