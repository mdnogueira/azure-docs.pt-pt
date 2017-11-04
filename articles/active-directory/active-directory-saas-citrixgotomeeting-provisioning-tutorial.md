---
title: "Tutorial: Integração do Azure Active Directory com GoToMeeting | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e GoToMeeting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 72f887b18daef27ff7e6ccea5bb30b55c86c7b98
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="tutorial-configuring-gotomeeting-for-automatic-user-provisioning"></a>Tutorial: Configurar GoToMeeting para aprovisionamento de utilizadores automática

O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar GoToMeeting e o Azure AD para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para GoToMeeting automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um GoToMeeting-início de sessão único ativar a subscrição.
*   Uma conta de utilizador no GoToMeeting com permissões de administrador de equipa.

## <a name="assigning-users-to-gotomeeting"></a>Atribuir utilizadores a GoToMeeting

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação GoToMeeting. Depois de decidir, pode atribuir estes utilizadores à sua aplicação GoToMeeting ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Sugestões importantes para atribuir utilizadores a GoToMeeting

*   Recomenda-se que um único utilizador do Azure AD é atribuído a GoToMeeting para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador a GoToMeeting, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinida" não funciona para o aprovisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o de ligar o seu Azure AD a conta de utilizador do GoToMeeting aprovisionamento API e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuída GoToMeeting com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
> Também pode optar por ativar baseados em SAML Single Sign-On para GoToMeeting, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o aprovisionamento da conta de utilizador automáticas:

1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado GoToMeeting para o início de sessão único, procure a instância do GoToMeeting utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **GoToMeeting** na Galeria de aplicações. Selecione GoToMeeting os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do GoToMeeting, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **aprovisionamento** modo para **automática**. 

    ![Aprovisionamento](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. Na secção credenciais de administrador, execute os seguintes passos:
   
    a. No **nome de utilizador de Admin GoToMeeting** caixa de texto, escreva o nome de utilizador do administrador.

    b. No **palavra-passe de administrador de GoToMeeting** caixa de texto, palavra-passe do administrador.

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação GoToMeeting. Se a ligação falhar, certifique-se de que a conta de GoToMeeting possui permissões de administrador de equipa e tente a **"Credenciais de administrador"** passo novamente.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação.

8. Clique em **guardar.**

9. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para GoToMeeting.**

10. No **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD e GoToMeeting. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no GoToMeeting para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD aprovisionamento do serviço para GoToMeeting, altere o **estado de aprovisionamento** para **no** na secção de definições

12. Clique em **guardar.**

Inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a GoToMeeting na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação GoToMeeting de aprovisionamento.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


