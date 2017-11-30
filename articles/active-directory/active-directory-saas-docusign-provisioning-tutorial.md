---
title: "Tutorial: Integração do Azure Active Directory com DocuSign | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e DocuSign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: c6a618c9cb93fe5b82e97f84c346e3dc448737e4
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="tutorial-configuring-docusign-for-user-provisioning"></a>Tutorial: Configurar DocuSign para aprovisionamento de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar DocuSign e o Azure AD para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para DocuSign automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um DocuSign-início de sessão único ativar a subscrição.
*   Uma conta de utilizador no DocuSign com permissões de administrador de equipa.

## <a name="assigning-users-to-docusign"></a>Atribuir utilizadores a DocuSign

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação DocuSign. Depois de decidir, pode atribuir estes utilizadores à sua aplicação DocuSign ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Sugestões importantes para atribuir utilizadores a DocuSign

*   Recomenda-se que um único utilizador do Azure AD é atribuído a DocuSign para testar a configuração de aprovisionamento. Os utilizadores adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador a DocuSign, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinida" não funciona para o aprovisionamento.

> [!NOTE]
> Azure AD não suporta o grupo de aprovisionamento com a aplicação de Docusign, apenas os utilizadores podem ser aprovisionados.

## <a name="enable-user-provisioning"></a>Ativar o aprovisionamento de utilizador

Esta secção orienta-o de ligar o seu Azure AD a conta de utilizador do DocuSign aprovisionamento API e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuída DocuSign com base na atribuição de utilizadores e grupos no Azure AD.

> [!Tip]
> Também pode optar por ativar baseados em SAML Single Sign-On para DocuSign, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o aprovisionamento da conta de utilizador:

O objetivo desta secção consiste em descrevem como ativar o aprovisionamento de utilizadores do Active Directory de contas de utilizador para DocuSign.

1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

2. Se já tiver configurado DocuSign para o início de sessão único, procure a instância do DocuSign utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **DocuSign** na Galeria de aplicações. Selecione DocuSign os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do DocuSign, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**. 

    ![Aprovisionamento](./media/active-directory-saas-docusign-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, forneça as seguintes definições de configuração:
   
    a. No **nome de utilizador de Admin** caixa de texto, tipo de nome que tenha de conta de um DocuSign o **administrador de sistema** perfil no DocuSign.com atribuído.
   
    b. No **palavra-passe de administrador** caixa de texto, escreva a palavra-passe para esta conta.

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação DocuSign.

7. No **correio eletrónico de notificação** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento e marque a caixa de verificação.

8. Clique em **guardar.**

9. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para DocuSign.**

10. No **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD e DocuSign. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador no DocuSign para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD aprovisionamento do serviço para DocuSign, altere o **estado de aprovisionamento** para **no** na secção de definições

12. Clique em **guardar.**

Inicia a sincronização inicial de quaisquer utilizadores atribuídos a DocuSign na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação DocuSign de aprovisionamento.

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar que a conta foi sincronizada DocuSign.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](active-directory-saas-docusign-tutorial.md)