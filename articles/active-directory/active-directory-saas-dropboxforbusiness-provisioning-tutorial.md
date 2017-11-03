---
title: "Tutorial: Integração do Azure Active Directory com o Dropbox para empresas | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Dropbox para empresas."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 6f7616e47322242f01a13d763f71c93d4ac06a92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Configurar o Dropbox para empresas para aprovisionamento de utilizadores automática

O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar a Dropbox para empresas e o Azure AD aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD ao Dropbox para empresas automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um Dropbox para empresas início de sessão único subscrição ativado.
*   Uma conta de utilizador no Dropbox para empresas com permissões de administrador de equipa.

## <a name="assigning-users-to-dropbox-for-business"></a>Atribuir utilizadores a Dropbox para empresas

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder ao seu Dropbox para a aplicação de negócio. Depois de decidir, pode atribuir estes utilizadores no seu Dropbox para a aplicação de negócio, seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Sugestões importantes para atribuir utilizadores a Dropbox para empresas

*   Recomenda-se que um único utilizador do Azure AD é atribuído a Dropbox para empresas testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador a Dropbox para empresas, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinida" não funciona para o aprovisionamento...

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o de ligar o seu Azure AD ao Dropbox para API de aprovisionamento da conta de utilizador do negócio e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no Dropbox para empresas com base no utilizador e grupo atribuição no Azure AD.

>[!Tip]
>Também pode optar por ativar baseados em SAML Single Sign-On para Dropbox para empresas, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o aprovisionamento da conta de utilizador automáticas:

1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado o Dropbox para empresas para o início de sessão único, procure a instância da Dropbox para empresas com o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **Dropbox para empresas** na Galeria de aplicações. Selecione o Dropbox para empresas dos resultados de pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a instância da Dropbox para empresas, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**. 

    ![Aprovisionamento](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, clique em **autorizar**. É aberta uma Dropbox para a caixa de diálogo de início de sessão de negócio numa nova janela do browser.

6. No **início de sessão ao Dropbox para ligar com o Azure AD** caixa de diálogo, iniciar sessão no seu Dropbox para o inquilino de negócio.

     ![Aprovisionamento de utilizadores](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769518.png "aprovisionamento de utilizadores")

7. Certifique-se de que pretende dar permissão do Azure Active Directory para efetuar alterações ao seu Dropbox para o inquilino de negócio. Clique em **permitir**.
    
      ![Aprovisionamento de utilizadores](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769519.png "aprovisionamento de utilizadores")

8. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar ao seu Dropbox para a aplicação de negócio. Se a ligação falhar, certifique-se a Dropbox para a conta tem permissões de administração de equipa e tente a **"Autorizar"** passo novamente.

9. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação.

10. Clique em **guardar.**

11. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory ao Dropbox para empresas.**

12. No **mapeamentos de atributos** secção, reveja os atributos de utilizador são sincronizados a partir do Azure AD para a Dropbox para empresas. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no Dropbox para empresas para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

13. Para ativar o Azure AD aprovisionamento do serviço para Dropbox para empresas, altere o **estado de aprovisionamento** para **no** na secção de definições

14. Clique em **guardar.**

Inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos ao Dropbox para empresas na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento no seu Dropbox para a aplicação de negócio de aprovisionamento.

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar que a conta foi sincronizada Dropbox para empresas.

Um ciclo de aprovisionamento de utilizadores concluída com êxito é indicada pelo Estado relacionado.

![Atribuir utilizadores](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/IC769523.png "atribuir utilizadores")


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](active-directory-saas-dropboxforbusiness-tutorial.md)