---
title: "Tutorial: Configurar Slack para aprovisionamento de utilizadores automática no Azure Active Directory | Microsoft Docs"
description: "Saiba como configurar o Azure Active Directory automaticamente aprovisionar e desativação de aprovisionar contas de utilizador ao Slack."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: 3cb49a4abb26c34a938c963c4cf326b5ccd490de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-slack-for-automatic-user-provisioning"></a>Tutorial: Configurar Slack para aprovisionamento de utilizadores automática


O objetivo deste tutorial é mostrar-lhe os passos necessários para efetuar Slack e o Azure AD para automaticamente as contas de utilizador aprovisionar e aprovisionar desativação do Azure AD para Slack. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active Directory
*   Um Slack inquilino com o [Plus plano](https://aadsyncfabric.slack.com/pricing) ou melhor ativado 
*   Uma conta de utilizador no Slack com permissões de administrador de equipa 

Nota: O Azure AD aprovisionamento integração depende o [Slack SCIM API](https://api.slack.com/scim) que está disponível para equipas Slack no sinal de adição planear ou uma melhor.

## <a name="assigning-users-to-slack"></a>Atribuir utilizadores a Slack

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, serão sincronizados apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD. 

Antes de configurar e ativar o serviço de aprovisionamento, terá de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação Slack. Depois de decidir, pode atribuir estes utilizadores à sua aplicação Slack ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Sugestões importantes para atribuir utilizadores a Slack

*   Recomenda-se que um único utilizador do Azure AD ser atribuídos a Slack para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao Slack, tem de selecionar o **utilizador** ou função de "Grupo de" na caixa de diálogo atribuição. A função de "Acesso predefinida" não funciona para o aprovisionamento.


## <a name="configuring-user-provisioning-to-slack"></a>Configurar o aprovisionamento de utilizadores ao Slack 

Esta secção orienta-o ao seu Azure AD a API de aprovisionamento da conta de utilizador do Slack e configurar o serviço de aprovisionamento para criar, atualizar e desativar atribuídos a contas de utilizador no Slack com base no utilizador e a atribuição de grupo no Azure AD.

**Sugestão:** também pode optar por ativar baseados em SAML Single Sign-On para Slack, seguindo as instruções fornecidas (portal do Azure) [https://portal.azure.com]. O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar o utilizador automáticas aprovisionamento da conta ao Slack no Azure AD:


1)  No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2) Se já tiver configurado Slack para o início de sessão único, procure a instância do Slack utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **Slack** na Galeria de aplicações. Selecione Slack os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3)  Selecione a sua instância do Slack, em seguida, selecione o **aprovisionamento** separador.

4)  Definir o **modo de aprovisionamento** para **automática**.

![Slack aprovisionamento](./media/active-directory-saas-slack-provisioning-tutorial/Slack1.PNG)

5)  Sob o **credenciais de administrador** secção, clique em **autorizar**. Esta ação abre uma caixa de diálogo de autorização Slack numa nova janela do browser. 

6) Na nova janela, inicie sessão no Slack utilizando a sua conta de administrador de equipa. na caixa de diálogo resultante do autorização, selecione o agrupamento Slack que pretende ativar o aprovisionamento de e, em seguida, selecione **autorizar**. Depois de concluída, volte ao portal do Azure para concluir a configuração de aprovisionamento.

![Caixa de diálogo de autorização](./media/active-directory-saas-slack-provisioning-tutorial/Slack3.PNG)

7) No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação Slack. Se a ligação falhar, certifique-se de que a conta Slack possui permissões de administrador de equipa e repita o passo de "opção autorizar".

8) Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação abaixo.

9) Clique em **Guardar**. 

10) Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory ao Slack**.

11) No **mapeamentos de atributos** secção, reveja os atributos de utilizador que serão sincronizados a partir do Azure AD para Slack. Tenha em atenção que os atributos selecionados como **correspondência** propriedades serão utilizadas para corresponder as contas de utilizador no Slack para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

12) Para ativar o Azure AD aprovisionamento do serviço para Slack, altere o **estado de aprovisionamento** para **no** no **definições** secção

13) Clique em **Guardar**. 

Isto iniciará a sincronização inicial de todos os utilizadores e/ou grupos atribuídos ao Slack na secção utilizadores e grupos. Tenha em atenção que a sincronização inicial irá demorar superior para efetuar sincronizações subsequentes, o que ocorrer aproximadamente a cada 10 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para aprovisionamento de relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcional] Configurar o aprovisionamento de objeto de grupo para Slack 

Opcionalmente, pode ativar o aprovisionamento dos objetos de grupo do Azure AD para Slack. Isto é diferente do "atribuir grupos de utilizadores", em que o grupo real de objeto para além dos seus membros serão replicadas do Azure AD para Slack. Por exemplo, se tiver um grupo denominado "Os meus grupo" no Azure AD, será criado um grupo identitical com o nome "Os meus grupo" dentro Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Para ativar o aprovisionamento dos objetos de grupo:

1) Na secção mapeamentos, selecione **sincronizar Azure grupos do Active Directory ao Slack**.

2) No painel do mapeamento de atributos, defina ativado como Sim.

3) No **mapeamentos de atributos** secção, reveja os atributos de grupo que irão ser sincronizados a partir do Azure AD para Slack. Tenha em atenção que os atributos selecionados como **correspondência** propriedades serão utilizadas para fazer corresponder os grupos no Slack para operações de atualização. 

4) Clique em **Guardar**.

Este resultado de quaisquer objetos do grupo atribuído ao Slack no **utilizadores e grupos** secção que está a ser totalmente sincronizados do Azure AD para Slack. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para aprovisionamento de relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação Slack.


## <a name="additional-resources"></a>Recursos Adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
