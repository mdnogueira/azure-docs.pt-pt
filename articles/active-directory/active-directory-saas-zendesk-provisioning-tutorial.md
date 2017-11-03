---
title: "Tutorial: Configurar ZenDesk para aprovisionamento de utilizadores automática no Azure Active Directory | Microsoft Docs"
description: Saiba como configurar o Azure Active Directory para aprovisionar e anular o aprovisionamento contas de utilizador para ZenDesk automaticamente.
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
ms.openlocfilehash: 1a1414eefd20e6d7c025da08cfd5ae7c45daad33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configurar ZenDesk para aprovisionamento de utilizadores automática


O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar do ZenDesk e AD do Azure para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para ZenDesk automaticamente. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory
*   Um inquilino do ZenDesk com o [plano empresarial](https://www.zendesk.com/product/pricing/) ou melhor ativado 
*   Uma conta de utilizador no ZenDesk com permissões de administrador 

> [!NOTE]
> O Azure AD aprovisionamento integração depende o [API de REST do ZenDesk](https://developer.zendesk.com/rest_api/docs/core/introduction#the-api), que está disponível para equipas do ZenDesk no plano de essencial ou melhor.

## <a name="assigning-users-to-zendesk"></a>Atribuir utilizadores a ZenDesk

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD são sincronizados. 

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação do ZenDesk. Depois de decidir, pode atribuir estes utilizadores à sua aplicação do ZenDesk ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Sugestões importantes para atribuir utilizadores a ZenDesk

*   Recomenda-se que um único utilizador do Azure AD é atribuído a ZenDesk para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador a ZenDesk, tem de selecionar um o **utilizador** função, ou outra válida específicas da aplicação função (se disponível) na caixa de diálogo atribuição. O **predefinido acesso** função não funciona para o aprovisionamento e estes utilizadores são ignorados.

> [!NOTE]
> Enquanto funcionalidade adicionada, o serviço de aprovisionamento lê quaisquer funções personalizadas definidas na Zendesk e importa-los com o Azure AD, onde podem ser seleccionados na caixa de diálogo Selecionar função. Estas funções de mensagens em fila vai estar visíveis no portal do Azure após a ativação do serviço de aprovisionamento e um ciclo de sincronização foi concluída.

## <a name="configuring-user-provisioning-to-zendesk"></a>Configurar o aprovisionamento de utilizadores para ZenDesk 

Esta secção orienta-o de ligar o seu Azure AD a API de aprovisionamento da conta de utilizador do ZenDesk e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído do ZenDesk com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP] 
> Também pode optar por ativar baseados em SAML Single Sign-On para ZenDesk, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.


### <a name="configure-automatic-user-account-provisioning-to-zendesk-in-azure-ad"></a>Configurar utilizador automáticas aprovisionamento da conta para ZenDesk no Azure AD


1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado ZenDesk para o início de sessão único, procure a instância do ZenDesk utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **ZenDesk** na Galeria de aplicações. Selecione ZenDesk os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do ZenDesk, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**.

    ![Aprovisionamento do ZenDesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk1.png)

5. Sob o **credenciais de administrador** secção, introduza o **nome de utilizador de Admin & tokenkey & domínio** gerado pela conta do ZenDesk (pode encontrar o token na sua conta: **Admin**  >  **API** > **definições**). 

    ![Aprovisionamento do ZenDesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk2.png)

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação do ZenDesk. Se a ligação falhar, certifique-se de que a conta do ZenDesk possui permissões de administrador e repita o passo 5.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação "Enviar uma notificação por e-mail quando ocorre uma falha."

8. Clique em **Guardar**. 

9. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para ZenDesk**.

10. No **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD e ZenDesk. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no ZenDesk para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD aprovisionamento de serviço do ZenDesk, altere o **estado de aprovisionamento** para **no** no **definições** secção

12. Clique em **Guardar**. 

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a ZenDesk na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o Azure AD, os registos de aprovisionamento, consulte [relatórios sobre o aprovisionamento da conta de utilizador automáticas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre o aprovisionamento de atividade](active-directory-saas-provisioning-reporting.md)
