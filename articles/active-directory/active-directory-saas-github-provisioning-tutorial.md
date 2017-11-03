---
title: "Tutorial: Configuração de GitHub para aprovisionamento de utilizadores automática no Azure Active Directory | Microsoft Docs"
description: Saiba como configurar o Azure Active Directory para aprovisionar e anular o aprovisionamento contas de utilizador ao GitHub automaticamente.
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.openlocfilehash: 3cc70273e95dbf4913e7bbcd8a37bd9a52987b60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-github-for-automatic-user-provisioning"></a>Tutorial: Configuração de GitHub para o aprovisionamento de utilizador automáticas


O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar no GitHub e o Azure AD para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD ao GitHub automaticamente. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory
*   Um inquilino do Github com o [plano negócio](https://help.github.com/articles/organization-billing-plans/#business-plan) ou melhor ativado 
*   Uma conta de utilizador no GitHub com permissões de administrador 

> [!NOTE]
> O Azure AD aprovisionamento integração depende o [GitHub SCIM API](https://developer.github.com/v3/scim/), que está disponível para as equipas de Github no plano de negócios ou melhor.

## <a name="assigning-users-to-github"></a>Atribuir utilizadores a GitHub

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD é sincronizado. 

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação do GitHub. Depois de decidir, pode atribuir estes utilizadores à sua aplicação do GitHub, seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Sugestões importantes para atribuir utilizadores a GitHub

*   Recomenda-se que um único utilizador do Azure AD é atribuído ao GitHub para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao GitHub, tem de selecionar um o **utilizador** função, ou outra válida específicas da aplicação função (se disponível) na caixa de diálogo atribuição. O **predefinido acesso** função não funciona para o aprovisionamento e estes utilizadores são ignorados.


## <a name="configuring-user-provisioning-to-github"></a>Configurar o aprovisionamento de utilizadores ao GitHub 

Esta secção orienta-o de ligar o seu Azure AD a API de aprovisionamento da conta de utilizador do GitHub e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no GitHub com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
> Também pode optar por ativar baseados em SAML Single Sign-On para o GitHub, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.


### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configurar utilizador automáticas aprovisionamento da conta ao GitHub para no Azure AD


1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado o GitHub para o início de sessão único, procure a instância do GitHub com o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **GitHub** na Galeria de aplicações. Selecione o GitHub dos resultados de pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do GitHub, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**.

    ![Aprovisionamento do GitHub](./media/active-directory-saas-github-provisioning-tutorial/GitHub1.png)

5. Sob o **credenciais de administrador** secção, clique em **autorizar**. Esta operação abre uma caixa de diálogo de autorização do GitHub numa nova janela do browser. 

6. Na nova janela, inicie sessão no GitHub com a conta de administrador. Na caixa de diálogo resultante do autorização, selecione o agrupamento do GitHub que pretende ativar o aprovisionamento de e, em seguida, selecione **autorizar**. Depois de concluída, volte ao portal do Azure para concluir a configuração de aprovisionamento.

    ![Caixa de diálogo de autorização](./media/active-directory-saas-github-provisioning-tutorial/GitHub2.png)

7. No portal do Azure, de entrada **URL de inquilino** e clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação do GitHub. Se a ligação falhar, certifique-se a conta do GitHub possui permissões de administrador e **URl de inquilino** é inputted corretamente, em seguida, repita o passo de "opção autorizar" (pode constituem **URL de inquilino** pela regra: "https://api.github.com/scim/v2/organizations/ + < Organizations_name >", pode encontrar as organizações na sua conta do GitHub: **definições** > **organizações**).

    ![Caixa de diálogo de autorização](./media/active-directory-saas-github-provisioning-tutorial/GitHub3.png)

8. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação "Enviar uma notificação por e-mail quando ocorre uma falha."

9. Clique em **Guardar**. 

10. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory ao GitHub**.

11. No **mapeamentos de atributos** secção, reveja os atributos de utilizador são sincronizados a partir do Azure AD no GitHub. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no GitHub para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

12. Para ativar o Azure AD aprovisionamento do serviço para o GitHub, altere o **estado de aprovisionamento** para **no** no **definições** secção

13. Clique em **Guardar**. 

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos ao GitHub na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o Azure AD, os registos de aprovisionamento, consulte [relatórios sobre o aprovisionamento da conta de utilizador automáticas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre o aprovisionamento de atividade](active-directory-saas-provisioning-reporting.md)
