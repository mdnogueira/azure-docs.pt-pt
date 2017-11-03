---
title: "Tutorial: Integração do Azure Active Directory com o ciclo de vida SCC | Microsoft Docs"
description: "Saiba como utilizar o ciclo de vida SCC com o Azure Active Directory para ativar o início de sessão único, aprovisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 9a30bcca720ff135d0180d73f46e78403e9bca43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Tutorial: Integração do Azure Active Directory com SCC ciclo de vida
O objetivo deste tutorial é mostrar a integração do Azure e SCC ciclo de vida.  

O cenário descrito neste tutorial assume que já tem os seguintes itens:

* Uma subscrição do Azure válida
* Um ciclo de vida SCC-início de sessão único (SSO) ativada subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que tem atribuídos ao ciclo de vida SCC será capazes de início de sessão único para a aplicação no site da sua empresa SCC ciclo de vida (serviço fornecedor iniciada pelo início de sessão) ou utilizando o [introdução para o acesso Painel](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos modulares seguintes:

1. Ativar a integração de aplicações para ciclo de vida SCC
2. Configurar início de sessão único (SSO)
3. Configurar o aprovisionamento de utilizadores
4. Atribuir utilizadores

![Cenário](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "cenário")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>Ativar a integração de aplicações para ciclo de vida SCC
O objetivo desta secção consiste em descrevem como ativar a integração de aplicações para ciclo de vida SCC.

**Para ativar a integração de aplicações para ciclo de vida SCC, execute os seguintes passos:**

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Do Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "do Active Directory")
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Aplicações](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "aplicações")
4. Clique em **adicionar** na parte inferior da página.
   
    ![Adicionar aplicação](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Adicionar aplicação")
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
    ![Adicionar uma aplicação de gallerry](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "adicionar uma aplicação de gallerry")
6. No **caixa pesquisa**, tipo **SCC ciclo de vida**.
   
    ![Galeria de aplicações](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galeria de aplicações")
7. No painel de resultados, selecione **SCC ciclo de vida**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
    ![Ciclo de vida SCC](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC ciclo de vida")
   
## <a name="configure-single-sign-on"></a>Configurar o início de sessão único

O objetivo desta secção consiste em descrevem como permitir que os utilizadores para se autenticar SCC ciclo de vida à respetiva conta no Azure AD com Federação com base no protocolo SAML.

**Para configurar o início de sessão único, execute os seguintes passos:**

1. No portal clássico do Azure, no **SCC ciclo de vida** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o * * configurar início de sessão único * * caixa de diálogo.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "configurar o início de sessão único")
2. No **como gostaria aos utilizadores iniciar sessão ciclo de vida SCC** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "configurar o início de sessão único")
3. No **URL da aplicação configurar** na página de **URL de início de sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para iniciar sessão sua aplicação de ciclo de vida SCC através do padrão de seguinte "*https://bs1.scc.com/ lc7/Welcome/Customer/PICTtest.aspx*"e, em seguida, clique em **seguinte**.
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "configurar o URL da aplicação")
4. No **configurar o início de sessão único no ciclo de vida SCC** página, clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "configurar o início de sessão único")
5. Reencaminhe nesse ficheiro de metadados para a equipa de suporte do ciclo de vida de SCC.
   
   >[!NOTE]
   >O início de sessão único tem de ser ativado pela equipa de suporte do ciclo de vida SCC.
   > 
   > 

6. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída** para fechar o **configurar início de sessão único** caixa de diálogo.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "configurar o início de sessão único")
   
## <a name="configure-user-provisioning"></a>Configurar o aprovisionamento de utilizadores

Para permitir que os utilizadores do Azure AD inicie sessão no ciclo de vida SCC, têm de ser aprovisionados para SCC ciclo de vida. Não há nenhum item de ação para configurar o aprovisionamento de utilizadores para SCC ciclo de vida.

Quando um utilizador atribuído tenta iniciar sessão no ciclo de vida SCC, uma conta de ciclo de vida SCC é criada automaticamente se necessário.

>[!NOTE]
>Pode utilizar quaisquer outras SCC ciclo de vida utilizador conta criação ferramentas ou APIs fornecidas pelo SCC ciclo de vida para aprovisionar contas de utilizador do AAD.
> 
> 

## <a name="assign-users"></a>Atribuir utilizadores
Para testar a configuração, tem de conceder os utilizadores do Azure AD que pretende permitir a utilizar o acesso de aplicação para o mesmo atribuindo-los.

**Para atribuir utilizadores a SCC ciclo de vida, execute os seguintes passos:**

1. No portal clássico do Azure, crie uma conta de teste.
2. No * * SCC ciclo de vida * * página de integração de aplicações, clique em **atribuir utilizadores**.
   
    ![Atribuir utilizadores](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "atribuir utilizadores")
3. Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a sua atribuição.
   
    ![Sim](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Sim")

Se pretender testar as definições de SSO, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

