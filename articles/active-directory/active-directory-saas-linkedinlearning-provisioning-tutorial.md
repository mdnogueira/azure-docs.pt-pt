---
title: "Tutorial: Configurar LinkedIn Learning para aprovisionamento de utilizadores automática no Azure Active Directory | Microsoft Docs"
description: Saiba como configurar o Azure Active Directory para aprovisionar e anular o aprovisionamento contas de utilizador para LinkedIn Learning automaticamente.
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
ms.date: 04/15/2017
ms.author: asmalser-msft
ms.openlocfilehash: 5eb2b1594eedb2a135d7b8cd501a33d8264e136b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-linkedin-learning-for-automatic-user-provisioning"></a>Tutorial: Configurar LinkedIn Learning para o aprovisionamento de utilizador automáticas


O objetivo deste tutorial é mostrar-lhe os passos que necessários para efetuar no LinkedIn Learning e o Azure AD para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para LinkedIn Learning automaticamente. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active Directory
*   Um inquilino LinkedIn Learning 
*   Uma conta de administrador no LinkedIn Learning com acesso ao centro de contas do LinkedIn

> [!NOTE]
> Azure Active Directory integra LinkedIn Learning utilizando o [SCIM](http://www.simplecloud.info/) protocolo.

## <a name="assigning-users-to-linkedin-learning"></a>Atribuir utilizadores a aprendizagem LinkedIn

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, serão sincronizados apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD. 

Antes de configurar e ativar o serviço de aprovisionamento, terá de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder a aprendizagem LinkedIn. Depois de decidir, pode atribuir estes utilizadores a aprendizagem LinkedIn ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-learning"></a>Sugestões importantes para atribuir utilizadores a aprendizagem LinkedIn

*   Recomenda-se que um único utilizador do Azure AD ser atribuídos a aprendizagem LinkedIn para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador a LinkedIn Learning, tem de selecionar o **utilizador** função na caixa de diálogo atribuição. A função de "Acesso predefinida" não funciona para o aprovisionamento.


## <a name="configuring-user-provisioning-to-linkedin-learning"></a>Configurar o aprovisionamento de utilizadores para LinkedIn Learning

Esta secção orienta-o seu Azure AD a ligar à conta de utilizador do LinkedIn Learning SCIM aprovisionamento API e configurar o serviço de aprovisionamento para criar, atualizar e desativar atribuídos a contas de utilizador no Learning LinkedIn com base no utilizador e a atribuição de grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseados em SAML Single Sign-On para LinkedIn Learning, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementam entre si.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-learning-in-azure-ad"></a>Para configurar o utilizador automáticas aprovisionamento da conta para LinkedIn Learning no Azure AD:


O primeiro passo é obter o token de acesso LinkedIn. Se for um administrador de empresa, pode aprovisionar um token de acesso. No seu centro de contas, aceda a **definições &gt; definições globais** e abra o **SCIM configuração** painel.

> [!NOTE]
> Se está a aceder ao centro de contas do diretamente em vez de através de uma ligação, pode aceder aos-la utilizando os seguintes passos.

1)  Inicie sessão no Centro de contas.

2)  Selecione **Admin &gt; definições de administrador** .

3)  Clique em **avançadas integrações** na barra lateral esquerdo. O utilizador é direcionado para o Centro de contas.

4)  Clique em **+ Adicionar nova configuração de SCIM** e siga o procedimento preenchendo em cada campo.

> Quando autoassign licenças não estiver ativada, significa que apenas os dados de utilizador estão sincronizados.

![LinkedIn aprovisionamento de aprendizagem](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_1.PNG)

> Quando a atribuição de autolicense estiver ativada, tem de ter em atenção a instância da aplicação e o tipo de licença. São atribuídas as licenças num provenientes do primeiro, primeiro servir base até que todas as licenças são executadas.

![LinkedIn aprovisionamento de aprendizagem](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_2.PNG)

5)  Clique em **gerar token**. Deverá ver o seu ecrã de token de acesso sob o **token de acesso** campo.

6)  Guarde o seu token de acesso à sua área de transferência ou o computador antes de deixarem a página.

7) Em seguida, iniciar sessão para o [portal do Azure](https://portal.azure.com)e navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

8) Se já tiver configurado o LinkedIn Learning para o início de sessão único, procure a instância do Learning LinkedIn utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **LinkedIn Learning** na Galeria de aplicações. Selecione LinkedIn Learning os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

9)  Selecione a sua instância do LinkedIn aprendizagem, em seguida, selecione o **aprovisionamento** separador.

10) Definir o **modo de aprovisionamento** para **automática**.

![LinkedIn aprovisionamento de aprendizagem](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_3.PNG)

11)  Preencha os campos seguintes em **credenciais de administrador** :

* No **URL de inquilino** campo, introduza https://api.linkedin.com.

* No **segredo Token** campo, introduza o token de acesso gerada no passo 1 e clique em **Testar ligação** .

* Deverá ver uma notificação de sucesso no lado upperright do seu portal.

12) Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação abaixo.

13) Clique em **Guardar**. 

14) No **mapeamentos de atributos** secção, reveja os atributos de utilizador e grupo que irão ser sincronizados a partir do Azure AD para LinkedIn Learning. Tenha em atenção que os atributos selecionados como **correspondência** propriedades serão utilizadas para corresponder as contas de utilizador e grupos no LinkedIn Learning para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

![LinkedIn aprovisionamento de aprendizagem](./media/active-directory-saas-linkedinlearning-provisioning-tutorial/linkedin_4.PNG)

15) Para ativar o Azure AD aprovisionamento do serviço de aprendizagem do LinkedIn, altere o **estado de aprovisionamento** para **no** no **definições** secção

16) Clique em **Guardar**. 

Isto iniciará a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a aprendizagem LinkedIn na secção utilizadores e grupos. Tenha em atenção que a sincronização inicial irá demorar superior para efetuar sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação de aprendizagem do LinkedIn de aprovisionamento.


## <a name="additional-resources"></a>Recursos Adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
