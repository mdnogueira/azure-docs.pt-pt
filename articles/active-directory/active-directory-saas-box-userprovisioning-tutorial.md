---
title: "Tutorial: Integração do Azure Active Directory com caixa | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e caixa."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 9f061f3f5a0a4825854b893150ceccc8951487de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-box-for-automatic-user-provisioning"></a>Tutorial: Configurar a caixa para aprovisionamento de utilizadores automática

O objetivo deste tutorial é mostrar os passos, que é necessário executar na caixa e do Azure AD automaticamente as contas de utilizador aprovisionar e aprovisionar desativação do Azure AD à caixa.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um caixa início de sessão único subscrição ativado.
*   Uma conta de utilizador na caixa com permissões de administrador de equipa.

## <a name="assigning-users-to-box"></a>Atribuir utilizadores a caixa 

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação de caixa. Depois de decidir, pode atribuir estes utilizadores à sua aplicação caixa ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Atribuir utilizadores e grupos
O **caixa > utilizadores e grupos** separador no portal do Azure permite-lhe especificar quais os utilizadores e grupos devem receber acesso à caixa. Atribuição de um utilizador ou grupo faz com que os seguintes procedimentos ocorrer:

* Azure AD permite ao utilizador atribuído (a atribuição direta ou associação de grupo) para autenticar à caixa. Se um utilizador não está atribuído, do Azure AD não permite que-los para iniciar sessão caixa e devolve um erro na página de início de sessão do Azure AD.
* É adicionado um mosaico da aplicação para a caixa para o utilizador [iniciador da aplicação](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).
* Se o aprovisionamento automático está ativado, em seguida, atribuídos aos utilizadores e/ou grupos são adicionados à fila aprovisionamento aprovisionar automaticamente.
  
  * Se apenas os objetos de utilizador foram configurados para ser aprovisionados, em seguida, todos os utilizadores diretamente atribuídos são colocados na fila de aprovisionamento e todos os utilizadores que são membros de quaisquer grupos atribuídos são colocados na fila de aprovisionamento. 
  * Se os objetos de grupo foram configurados para ser aprovisionados, em seguida, todos os objetos do grupo atribuído são aprovisionados para a caixa e todos os utilizadores que são membros desses grupos. As associações de grupo e utilizador são mantidas após a ser escrito à caixa.

Pode utilizar o **atributos > Single Sign-On** separador para configurar os atributos de utilizador (ou afirmações) são apresentadas à caixa durante a autenticação baseada em SAML e o **atributos > aprovisionamento** separador para Configure como atributos de utilizador e grupo de fluxo do Azure AD à caixa durante o aprovisionamento de operações.

### <a name="important-tips-for-assigning-users-to-box"></a>Sugestões importantes para atribuir utilizadores a caixa 

*   Recomenda-se que um único utilizador do Azure AD atribuído à caixa para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador à caixa, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinida" não funciona para o aprovisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção fornece orientações para ligar o seu Azure AD a conta de utilizador da caixa aprovisionamento API e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído na caixa com base na atribuição de utilizadores e grupos no Azure AD.

Se o aprovisionamento automático está ativado, em seguida, atribuídos aos utilizadores e/ou grupos são adicionados à fila aprovisionamento aprovisionar automaticamente.
    
 * Se apenas os objetos de utilizador estiverem configurados para ser aprovisionados, em seguida, os utilizadores diretamente atribuídos são colocados na fila de aprovisionamento e todos os utilizadores que são membros de quaisquer grupos atribuídos são colocados na fila de aprovisionamento. 
    
 * Se os objetos de grupo foram configurados para ser aprovisionados, em seguida, todos os objetos do grupo atribuído são aprovisionados para a caixa e todos os utilizadores que são membros desses grupos. As associações de grupo e utilizador são mantidas após a ser escrito à caixa.

> [!TIP] 
> Também pode optar por ativar baseados em SAML Single Sign-On para a caixa, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o aprovisionamento da conta de utilizador automáticas:

O objetivo desta secção consiste em descrevem como ativar o aprovisionamento de contas de utilizador do Active Directory à caixa.

1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado a caixa para o início de sessão único, procure a instância da caixa utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **caixa** na Galeria de aplicações. Selecione a caixa de resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a instância da caixa de, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**. 

    ![Aprovisionamento](./media/active-directory-saas-box-userprovisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, clique em **autorizar** para abrir uma caixa de diálogo de início de sessão de caixa numa nova janela do browser.

6. No **início de sessão para conceder acesso à caixa** página, forneça as credenciais necessárias e, em seguida, clique em **autorizar**. 
   
    ![Ativar o aprovisionamento de utilizador automáticas](./media/active-directory-saas-box-userprovisioning-tutorial/IC769546.png "ativar o aprovisionamento de utilizador automáticas")

7. Clique em **conceder acesso à caixa** para autorizar esta operação e para regressar ao portal do Azure. 
   
    ![Ativar o aprovisionamento de utilizador automáticas](./media/active-directory-saas-box-userprovisioning-tutorial/IC769549.png "ativar o aprovisionamento de utilizador automáticas")

8. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação de caixa. Se a ligação falhar, certifique-se de que a conta de caixa possui permissões de administrador de equipa e tente a **"Autorizar"** passo novamente.

9. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação.

10. Clique em **guardar.**

11. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory à caixa.**

12. No **mapeamentos de atributos** secção, reveja os atributos de utilizador são sincronizados a partir do Azure AD à caixa. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador na caixa para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

13. Para ativar o Azure AD aprovisionamento do serviço de caixa, altere o **estado de aprovisionamento** para **no** na secção de definições

14. Clique em **guardar.**

Que inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos à caixa na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na aplicação da caixa de aprovisionamento.

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar que a conta foi sincronizada à caixa.

No seu inquilino de caixa, os utilizadores sincronizados estão listados na **utilizadores geridos** no **consola de administração**.

![Estado de integração](./media/active-directory-saas-box-userprovisioning-tutorial/IC769556.png "estado de integração")


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](active-directory-saas-box-tutorial.md)