---
title: "Tutorial: Integração do Azure Active Directory com Jive | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Jive."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 957b152fdd40d08a867e788b0cb9f7d57ed481e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-jive-for-user-provisioning"></a>Tutorial: Configurar Jive para aprovisionamento de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar no Jive e o Azure AD para automaticamente as contas de utilizador aprovisionar e aprovisionar desativação do Azure AD para Jive.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um Jive início de sessão único subscrição ativado.
*   Uma conta de utilizador no Jive com permissões de administrador de equipa.

## <a name="assigning-users-to-jive"></a>Atribuir utilizadores a Jive

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação Jive. Depois de decidir, pode atribuir estes utilizadores à sua aplicação Jive ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Sugestões importantes para atribuir utilizadores a Jive

*   Recomenda-se que um único utilizador do Azure AD ser atribuídos a Jive para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador a Jive, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinida" não funciona para o aprovisionamento.

## <a name="enable-user-provisioning"></a>Ativar o aprovisionamento de utilizador

Esta secção orienta-o de ligar o seu Azure AD a conta de utilizador do Jive aprovisionamento API e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuída Jive com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
> Também pode optar por ativar baseados em SAML Single Sign-On para Jive, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o aprovisionamento da conta de utilizador:

O objetivo desta secção consiste em descrevem como ativar o aprovisionamento de utilizadores do Active Directory de contas de utilizador para Jive.
Como parte deste procedimento, é necessário fornecer um token de segurança do utilizador que tem de pedir a Jive.com.

1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado Jive para o início de sessão único, procure a instância do Jive utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **Jive** na Galeria de aplicações. Selecione Jive os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do Jive, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**. 

    ![Aprovisionamento](./media/active-directory-saas-jive-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, forneça as seguintes definições de configuração:
   
    a. No **nome de utilizador de Admin Jive** caixa de texto, tipo de nome que tenha de conta de um Jive o **administrador de sistema** perfil no Jive.com atribuído.
   
    b. No **palavra-passe de administrador de Jive** caixa de texto, escreva a palavra-passe para esta conta.
   
    c. No **URL de inquilino Jive** caixa de texto, escreva o URL de inquilino Jive.
      
      > [!NOTE]
      > O URL de inquilino Jive é o URL que é utilizado pela sua organização para iniciar sessão no Jive.  
      > Normalmente, o URL tem o seguinte formato: **www.\< organização\>. jive.com**.          

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação Jive.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação abaixo.

8. Clique em **guardar.**

9. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para Jive.**

10. No **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD e Jive. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no Jive para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD aprovisionamento do serviço para Jive, altere o **estado de aprovisionamento** para **no** na secção de definições

12. Clique em **guardar.**

Inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a Jive na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação Jive de aprovisionamento.

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar que a conta foi sincronizada Jive.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](active-directory-saas-jive-tutorial.md)