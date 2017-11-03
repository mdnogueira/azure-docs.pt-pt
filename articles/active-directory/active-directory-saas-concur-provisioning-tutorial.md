---
title: "Tutorial: Integração do Azure Active Directory com Concur | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Concur."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: cd35b6e2dc3171e9cffdb820bbc5b0d45ff58e07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-concur-for-user-provisioning"></a>Tutorial: Configurar Concur para aprovisionamento de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar Concur e o Azure AD para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para Concur automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um Concur-início de sessão único ativar a subscrição.
*   Uma conta de utilizador no Concur com permissões de administrador de equipa.

## <a name="assigning-users-to-concur"></a>Atribuir utilizadores a Concur

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação Concur. Depois de decidir, pode atribuir estes utilizadores à sua aplicação Concur ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Sugestões importantes para atribuir utilizadores a Concur

*   Recomenda-se que um único utilizador do Azure AD ser atribuídos a Concur para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador a Concur, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinida" não funciona para o aprovisionamento.

## <a name="enable-user-provisioning"></a>Ativar o aprovisionamento de utilizador

Esta secção orienta-o de ligar o seu Azure AD a conta de utilizador do Concur aprovisionamento API e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuída Concur com base na atribuição de utilizadores e grupos no Azure AD.

> [!Tip] 
> Também pode optar por ativar baseados em SAML Single Sign-On para Concur, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o aprovisionamento da conta de utilizador:

O objetivo desta secção consiste em descrevem como ativar o aprovisionamento de contas de utilizador do Active Directory para Concur.

Para permitir que as aplicações no serviço de despesa, existe tem de ser a configuração adequada e a utilização de um perfil de administrador de serviço Web. Não adicione a função de administrador de WS para o seu perfil de administrador existente que utiliza para T & zar funções administrativas.

Concur consultores ou o administrador de cliente tem de criar um perfil de administrador do serviço Web distintos e o administrador de cliente tem de utilizar este perfil para as funções de administrador de serviços Web (por exemplo, ativar aplicações). Estes perfis têm de ser mantidos separados de diário T & zar admin perfil o administrador de cliente (o perfil de administrador T & zar não deve ter a função de WSAdmin atribuída).

Quando cria o perfil para ser utilizado para ativar a aplicação, introduza o nome do administrador de cliente para os campos de perfil de utilizador. Esta ação atribui propriedade para o perfil. Quando um ou mais perfis for criado, o cliente tem de iniciar sessão com este perfil, clique em de "*ativar*" botão para uma aplicação de parceiro no menu de serviços Web.

Pelos seguintes motivos, esta ação não deve ser feita com o perfil que utilizam para a administração de T & zar normal.

* O cliente tem de ser que clica "*Sim*" na janela de diálogo que é apresentada depois de uma aplicação está ativada. Isto, clique em visualizar que o cliente está disposto para a aplicação de parceiro para aceder aos respetivos dados, pelo que o utilizador ou o parceiro não é possível clique nesse botão de Sim.

* Se um administrador de cliente que ativou uma aplicação com o administrador T & Utilizar perfil sai da empresa (resultando num perfil que está a ser inactivated), todas as aplicações ativadas com que o perfil não funcionar até que a aplicação está ativada com a outro perfil de administrador WS Active Directory. É por isso é suposto criar distintos WS Admin perfis.

* Se um administrador sai da empresa, o nome associado ao perfil de administrador de WS pode ser alterado para o administrador de substituição, se assim o desejar sem afetar que a aplicação ativada porque não precisa desse perfil inactivated.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Concur** inquilino.

2. Do **administração** menu, selecione **serviços Web**.
   
    ![Inquilino Concur](./media/active-directory-saas-concur-provisioning-tutorial/IC721729.png "Concur inquilino")

3. No lado esquerdo, do **serviços Web** painel, selecione **ativar aplicações de parceiros**.
   
    ![Ativar a aplicação do parceiro](./media/active-directory-saas-concur-provisioning-tutorial/ic721730.png "ativar aplicações de parceiros")

4. Do **ativar aplicação** lista, selecione **do Azure Active Directory**e, em seguida, clique em **ativar**.
   
    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-provisioning-tutorial/ic721731.png "do Microsoft Azure Active Directory")

5. Clique em **Sim** para fechar o **confirmar a ação** caixa de diálogo.
   
    ![Confirme a ação](./media/active-directory-saas-concur-provisioning-tutorial/ic721732.png "confirmar a ação")

6. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

7. Se já tiver configurado Concur para o início de sessão único, procure a instância do Concur utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **Concur** na Galeria de aplicações. Selecione Concur os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

8. Selecione a sua instância do Concur, em seguida, selecione o **aprovisionamento** separador.

9. Definir o **modo de aprovisionamento** para **automática**. 
 
    ![Aprovisionamento](./media/active-directory-saas-concur-provisioning-tutorial/provisioning.png)

10. Sob o **credenciais de administrador** secção, introduza o **nome de utilizador** e **palavra-passe** do seu administrador de Concur.

11. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação Concur. Se a ligação falhar, certifique-se de que a conta de Concur possui permissões de administrador de equipa.

12. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação.

13. Clique em **guardar.**

14. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para Concur.**

15. No **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD e Concur. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no Concur para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

16. Para ativar o Azure AD aprovisionamento do serviço para Concur, altere o **estado de aprovisionamento** para **no** no **definições** secção

17. Clique em **guardar.**

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar que a conta foi sincronizada Concur.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](active-directory-saas-concur-tutorial.md)

