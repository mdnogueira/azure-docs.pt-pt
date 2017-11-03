---
title: "Tutorial: Configurar LucidChart para aprovisionamento de utilizadores automática no Azure Active Directory | Microsoft Docs"
description: Saiba como configurar o Azure Active Directory para aprovisionar e anular o aprovisionamento contas de utilizador para LucidChart automaticamente.
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
ms.openlocfilehash: 1f9344a5e750360e21ed7dc8e3ed013c2c2e1a45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Configurar LucidChart para aprovisionamento de utilizadores automática


O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar LucidChart e o Azure AD para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para LucidChart automaticamente. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory
*   Um inquilino LucidChart com o [plano empresarial](https://www.lucidchart.com/user/117598685#/subscriptionLevel) ou melhor ativado 
*   Uma conta de utilizador no LucidChart com permissões de administrador 

## <a name="assigning-users-to-lucidchart"></a>Atribuir utilizadores a LucidChart

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD é sincronizado. 

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação LucidChart. Depois de decidir, pode atribuir estes utilizadores à sua aplicação LucidChart ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Sugestões importantes para atribuir utilizadores a LucidChart

*   Recomenda-se que um único utilizador do Azure AD é atribuído a LucidChart para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador a LucidChart, tem de selecionar um o **utilizador** função, ou outra válida específicas da aplicação função (se disponível) na caixa de diálogo atribuição. O **predefinido acesso** função não funciona para o aprovisionamento e estes utilizadores são ignorados.


## <a name="configuring-user-provisioning-to-lucidchart"></a>Configurar o aprovisionamento de utilizadores para LucidChart 

Esta secção orienta-o de ligar o seu Azure AD a conta de utilizador do LucidChart aprovisionamento API e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuída LucidChart com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
> Também pode optar por ativar baseados em SAML Single Sign-On para LucidChart, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.


### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Configurar utilizador automáticas aprovisionamento da conta para LucidChart no Azure AD


1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado LucidChart para o início de sessão único, procure a instância do LucidChart utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **LucidChart** na Galeria de aplicações. Selecione LucidChart os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do LucidChart, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**.

    ![Aprovisionamento de LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart1.png)

5. Sob o **credenciais de administrador** secção, introduza o **segredo Token** gerado pela conta do seu LucidChart (pode encontrar o token na sua conta: **equipa** > **integração de aplicações** > **SCIM**). 

    ![Aprovisionamento de LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart2.png)

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação LucidChart. Se a ligação falhar, certifique-se de que a conta de LucidChart possui permissões de administrador e repita o passo 5.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação "Enviar uma notificação por e-mail quando ocorre uma falha."

8. Clique em **Guardar**. 

9. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para LucidChart**.

10. No **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD e LucidChart. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no LucidChart para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD aprovisionamento do serviço para LucidChart, altere o **estado de aprovisionamento** para **no** no **definições** secção

12. Clique em **Guardar**. 

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a LucidChart na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o Azure AD, os registos de aprovisionamento, consulte [relatórios sobre o aprovisionamento da conta de utilizador automáticas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre o aprovisionamento de atividade](active-directory-saas-provisioning-reporting.md)
