---
title: "Tutorial: Configurar Cerner Central para o aprovisionamento de utilizadores automática no Azure Active Directory | Microsoft Docs"
description: Saiba como configurar o Azure Active Directory para aprovisionar automaticamente os utilizadores para uma lista na Cerner Central.
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
ms.date: 05/26/2017
ms.author: asmalser-msft
ms.openlocfilehash: 84613b7f8d7bd031d492a62da0bc53be96ac45a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-cerner-central-for-automatic-user-provisioning"></a>Tutorial: Configurar Cerner Central para o aprovisionamento de utilizadores automática

O objetivo deste tutorial é mostrar-lhe os passos que necessários para executar no Centro de Cerner e o Azure AD para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para uma lista de utilizador no Centro de Cerner automaticamente. 


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes itens:

*   Um inquilino do Azure Active Directory
*   Um inquilino Cerner Central 

> [!NOTE]
> Azure Active Directory integra Cerner Central utilizando o [SCIM](http://www.simplecloud.info/) protocolo.

## <a name="assigning-users-to-cerner-central"></a>Atribuir utilizadores a Cerner Central

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD são sincronizados. 

Antes de configurar e ativar o serviço de aprovisionamento, deve decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder ao centro de Cerner. Depois de decidir, pode atribuir estes utilizadores a Cerner Central ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Sugestões importantes para atribuir utilizadores a Cerner Central

*   Recomenda-se que um único utilizador do Azure AD ser atribuídos a Cerner Central para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Depois de testar inicial estiver concluída para um único utilizador, Cerner Central recomenda a atribuir a lista completa de utilizadores que se destina a aceder a qualquer solução de Cerner (não apenas Cerner Central) ser aprovisionada para a lista de utilizador do Cerner.  Outras soluções de Cerner tirar partido desta lista de utilizadores na lista de utilizador.

*   Ao atribuir um utilizador a Cerner Central, tem de selecionar o **utilizador** função na caixa de diálogo atribuição. Os utilizadores com a função de "Acesso predefinida" foram excluídos de aprovisionamento.


## <a name="configuring-user-provisioning-to-cerner-central"></a>Configurar o aprovisionamento de utilizadores para Cerner Central

Esta secção orienta-o de ligar o seu Azure AD a lista de utilizadores do Centro de Cerner utilizando a conta de utilizador do Cerner SCIM aprovisionamento API e configurar o serviço de aprovisionamento para criar, atualizar e desativar o utilizador atribuído contas no Centro de Cerner com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
> Também pode optar por ativar baseados em SAML Single Sign-On para Cerner Central, seguindo as instruções fornecidas no [portal do Azure (https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementam entre si. Para obter mais informações, consulte o [Cerner Central único início de sessão tutorial](active-directory-saas-cernercentral-tutorial.md).


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Para configurar o utilizador automáticas aprovisionamento da conta para Cerner Central no Azure AD:


Para aprovisionar contas de utilizador para Cerner Central, terá de pedir uma conta de sistema Cerner Central de Cerner e gerar um token de portador do OAuth do Azure AD pode utilizar para ligar ao ponto final SCIM do Cerner. Recomenda-se também que a integração ser efetuada num ambiente sandbox Cerner antes de implementar para produção.

1.  O primeiro passo é assegurar que as pessoas gerir o Cerner e a integração do Azure AD tem uma conta de CernerCare, que é necessária para aceder à documentação necessária para concluir as instruções. Se necessário, utilize os URLs abaixo para criar contas de CernerCare em cada ambiente aplicável.

   * Sandbox: https://sandboxcernercare.com/accounts/create

   * Produção: https://cernercare.com/accounts/create  

2.  Em seguida, deve ser criada uma conta de sistema para o Azure AD. Utilize as instruções abaixo para pedir uma conta de sistema para os seus ambientes de sandbox e produção.

   * Instruções: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produção: https://cernercentral.com/system-accounts/

3.  Em seguida, gere um token de portador do OAuth para cada uma das suas contas de sistema. Para tal, siga as instruções abaixo.

   * Instruções: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produção: https://cernercentral.com/system-accounts/

4. Por fim, terá de adquirir IDs de Realm de lista de utilizador para ambientes de sandbox e de produção no Cerner para concluir a configuração. Para obter informações sobre como adquirir isto, consulte: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Agora pode configurar o Azure AD para aprovisionar contas de utilizador para Cerner. Iniciar sessão para o [portal do Azure](https://portal.azure.com)e navegue para o **do Azure Active Directory > aplicações da empresa > todas as aplicações** secção.

6. Se já tiver configurado Cerner Central para o início de sessão único, procure a instância do Centro de Cerner utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **Cerner Central** na Galeria de aplicações. Selecione Cerner Central os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

7.  Selecione a sua instância do Cerner Central, em seguida, selecione o **aprovisionamento** separador.

8.  Definir o **modo de aprovisionamento** para **automática**.

   ![Centro de Cerner aprovisionamento](./media/active-directory-saas-cernercentral-provisioning-tutorial/Cerner.PNG)

9.  Preencha os campos seguintes em **credenciais de administrador**:

   * No **URL de inquilino** campo, introduza um URL no formato abaixo, substituindo "-lista-Realm-ID de utilizador" com o ID de realm que obteve no passo #4.

> Sandbox: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

> Produção: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * No **segredo Token** campo, introduza o token de portador do OAuth gerados no passo 3 de # e clique em **Testar ligação**.

   * Deverá ver uma notificação de sucesso no lado upperright do seu portal.

10. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação abaixo.

11. Clique em **Guardar**. 

12. No **mapeamentos de atributos** secção, reveja os atributos de utilizador e de grupo para ser sincronizado do Azure AD Cerner Central. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador e grupos no Centro de Cerner para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

13. Para ativar o Azure AD aprovisionamento do serviço para Cerner Central, altere o **estado de aprovisionamento** para **no** no **definições** secção

14. Clique em **Guardar**. 

Esta ação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos ao centro de Cerner na secção utilizadores e grupos. A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o Azure AD aprovisionamento do serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para os relatórios de atividade, que descrevem a todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação Cerner Central de aprovisionamento.

Para obter mais informações sobre como ler o Azure AD, os registos de aprovisionamento, consulte [relatórios sobre o aprovisionamento da conta de utilizador automáticas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

## <a name="additional-resources"></a>Recursos adicionais

* [Centro de Cerner: Publicar dados de identidade com o Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Tutorial: Configurar Cerner Central para início de sessão no Azure Active Directory](active-directory-saas-cernercentral-tutorial.md)
* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-enterprise-apps-manage-provisioning.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passos seguintes
* [Saiba como rever os registos e obter relatórios sobre o aprovisionamento de atividade](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).
