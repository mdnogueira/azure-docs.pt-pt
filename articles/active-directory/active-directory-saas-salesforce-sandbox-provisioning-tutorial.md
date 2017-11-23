---
title: "Tutorial: Integração do Azure Active Directory com o Salesforce Sandbox | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e a Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: c6c5d01a831a76c497f42fe4af3d90f8c5dc5903
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="tutorial-configuring-salesforce-sandbox-for-automatic-user-provisioning"></a>Tutorial: Configurar o Salesforce Sandbox para o aprovisionamento de utilizador automáticas

O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar no Salesforce Sandbox e o Azure AD para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para Salesforce Sandbox automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um inquilino válido para o Salesforce Sandbox para o trabalho ou Salesforce Sandbox para Education. É possível utilizar uma conta de avaliação gratuita para o serviço.
*   Uma conta de utilizador no Salesforce Sandbox com as permissões de administrador de equipa.

## <a name="assigning-users-to-salesforce-sandbox"></a>Atribuir utilizadores a Salesforce Sandbox

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores ou grupos no Azure AD que necessitam de aceder à sua aplicação Salesforce Sandbox. Depois de tiver tomado a decisão, pode atribuir estes utilizadores à sua aplicação Salesforce Sandbox ao seguir as instruções no [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Sugestões importantes para atribuir utilizadores a Salesforce Sandbox

* Recomenda-se que um único utilizador do Azure AD é atribuído a Salesforce Sandbox para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador a Salesforce Sandbox, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinida" não funciona para o aprovisionamento.

> [!NOTE]
> Esta aplicação importa funções personalizadas a partir de Salesforce Sandbox como parte do processo de aprovisionamento, o cliente poderá ser útil selecionar ao atribuir utilizadores.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o de ligar o seu Azure AD a API de aprovisionamento da conta de utilizador do Salesforce Sandbox e configurar o serviço de aprovisionamento para criar, atualizar e desativar o utilizador atribuído contas no Salesforce Sandbox com base na atribuição de utilizadores e grupos no Azure AD.

>[!Tip]
>Também pode optar por ativar baseados em SAML Single Sign-On para Sandbox do Salesforce, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o aprovisionamento da conta de utilizador automáticas

O objetivo desta secção consiste em descrevem como ativar o aprovisionamento de utilizadores do Active Directory de contas de utilizador para a Sandbox do Salesforce.

1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado o Salesforce Sandbox para o início de sessão único, procure a instância do Salesforce Sandbox utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **Salesforce Sandbox** na Galeria de aplicações. Selecione o Salesforce Sandbox de resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do Salesforce Sandbox, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**.

    ![a aprovisionar](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, forneça as seguintes definições de configuração:
   
    a. No **nome de utilizador de Admin** caixa de texto, tipo de uma Salesforce Sandbox conta nome que tenha o **administrador de sistema** perfil no site Salesforce.com atribuído.
   
    b. No **palavra-passe de administrador** caixa de texto, escreva a palavra-passe para esta conta.

6. Para obter token de segurança do Salesforce Sandbox, abra um novo separador e iniciar sessão na mesma conta de administrador do Salesforce Sandbox. No canto superior direito da página, clique em seu nome e, em seguida, clique em **definições**.

     ![Ativar o aprovisionamento de utilizador automáticas](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "ativar o aprovisionamento de utilizador automáticas")

7. No painel de navegação esquerdo, clique em **My informações pessoais** para expandir a secção relacionada e, em seguida, clique em **repor o meu Token de segurança**.
  
    ![Ativar o aprovisionamento de utilizador automáticas](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "ativar o aprovisionamento de utilizador automáticas")

8. No **repor o Token de segurança** página, clique em de **repor o Token de segurança** botão.

    ![Ativar o aprovisionamento de utilizador automáticas](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "ativar o aprovisionamento de utilizador automáticas")

9. Verifique a pasta a receber de correio eletrónico associada esta conta de administrador. Procure uma mensagem de e-mail do Salesforce Sandbox.com que contém o novo token de segurança.

10. Copie o token, vá para a janela do Azure AD e cole-o para o **segredo Token** campo.

11. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação Salesforce Sandbox.

12. No **correio eletrónico de notificação** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento e marque a caixa de verificação.

13. Clique em **guardar.**  
    
14.  Na secção mapeamentos, selecione **sincronizar do Azure Active Directory que os utilizadores Salesforce Sandbox.**

15. No **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD e Salesforce Sandbox. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no Salesforce Sandbox para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

16. Para ativar o Azure AD aprovisionamento do serviço do Salesforce Sandbox, altere o **estado de aprovisionamento** para **no** na secção de definições

17. Clique em **guardar.**

Inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a Salesforce Sandbox na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na aplicação Salesforce Sandbox de aprovisionamento.

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar que a conta do salesforce foi sincronizada.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
