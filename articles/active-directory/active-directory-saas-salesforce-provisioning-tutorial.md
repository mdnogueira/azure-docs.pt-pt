---
title: "Tutorial: Integração do Azure Active Directory com o Salesforce | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e da Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: a573a7ef79e28c50ae0923849a88f88af40f21be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-salesforce-for-automatic-user-provisioning"></a>Tutorial: Configurar o Salesforce para aprovisionamento de utilizadores automática

O objetivo deste tutorial é mostrar os passos necessários para efetuar no Salesforce e o Azure AD para aprovisionar automaticamente e contas de utilizador de aprovisionar desativação do Azure AD a Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Tem de ter um inquilino para Salesforce válido para o trabalho ou Salesforce para Education. É possível utilizar uma conta de avaliação gratuita para o serviço.
*   Uma conta de utilizador no Salesforce com permissões de administrador de equipa.

## <a name="assigning-users-to-salesforce"></a>Atribuir utilizadores a Salesforce

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação Salesforce. Depois de decidir, pode atribuir estes utilizadores à sua aplicação Salesforce ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Sugestões importantes para atribuir utilizadores a Salesforce

*   Recomenda-se que um único utilizador do Azure AD é atribuído a Salesforce para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*  Ao atribuir um utilizador a Salesforce, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinida" não funciona para aprovisionamento

    > [!NOTE]
    > Esta aplicação importa funções personalizadas do Salesforce como parte do processo de aprovisionamento, o cliente poderá ser útil selecionar ao atribuir utilizadores

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o de ligar o seu Azure AD a API de aprovisionamento da conta de utilizador do Salesforce e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no Salesforce com base na atribuição de utilizadores e grupos no Azure AD.

>[!Tip]
>Também pode optar por ativar baseados em SAML Single Sign-On para Salesforce, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o aprovisionamento da conta de utilizador automáticas:

O objetivo desta secção consiste em descrevem como ativar o aprovisionamento de utilizadores do Active Directory de contas de utilizador Salesforce.

1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado o Salesforce para o início de sessão único, procure a instância do Salesforce utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **Salesforce** na Galeria de aplicações. Selecione o Salesforce dos resultados de pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do Salesforce, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**. 
![Aprovisionamento](./media/active-directory-saas-salesforce-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, forneça as seguintes definições de configuração:
   
    a. No **nome de utilizador de Admin** caixa de texto, tipo de nome que tenha de conta de um Salesforce o **administrador de sistema** perfil no site Salesforce.com atribuído.
   
    b. No **palavra-passe de administrador** caixa de texto, escreva a palavra-passe para esta conta.

6. Para obter token de segurança do Salesforce, abra um novo separador e iniciar sessão na mesma conta de administrador do Salesforce. No canto superior direito da página, clique em seu nome e, em seguida, clique em **as minhas definições**.

     ![Ativar o aprovisionamento de utilizador automáticas](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-my-settings.png "ativar o aprovisionamento de utilizador automáticas")
7. No painel de navegação esquerdo, clique em **pessoais** para expandir a secção relacionada e, em seguida, clique em **repor o meu Token de segurança**.
  
    ![Ativar o aprovisionamento de utilizador automáticas](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-personal-reset.png "ativar o aprovisionamento de utilizador automáticas")
8. No **repor o meu Token de segurança** página, clique em **repor o Token de segurança** botão.

    ![Ativar o aprovisionamento de utilizador automáticas](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-reset-token.png "ativar o aprovisionamento de utilizador automáticas")
9. Verifique a pasta a receber de correio eletrónico associada esta conta de administrador. Procure um e-mail de em Salesforce.com que contém o novo token de segurança.
10. Copie o token, vá para a janela do Azure AD e cole-o para o **Socket Token** campo.

11. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação Salesforce.

12. No **correio eletrónico de notificação** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento e marque a caixa de verificação abaixo.

13. Clique em **guardar.**  
    
14.  Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory Salesforce.**

15. No **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD e Salesforce. Tenha em atenção que os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no Salesforce para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

16. Para ativar o Azure AD aprovisionamento de serviço do Salesforce, altere o **estado de aprovisionamento** para **no** na secção de definições

17. Clique em **guardar.**

Esta ação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a Salesforce na secção utilizadores e grupos. Tenha em atenção que a sincronização inicial leva superior para efetuar sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para aprovisionamento de relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação Salesforce.

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar que a conta do Salesforce foi sincronizada.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](active-directory-saas-salesforce-tutorial.md)