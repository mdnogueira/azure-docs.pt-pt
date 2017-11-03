---
title: "Tutorial: Integração do Azure Active Directory com Qualtrics | Microsoft Docs"
description: "Saiba como utilizar Qualtrics com o Azure Active Directory para ativar o início de sessão único, aprovisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 2fcde595a40dafda7549f5bccb582b57585b314e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Tutorial: Integração do Azure Active Directory com Qualtrics
O objetivo deste tutorial é mostrar a integração do Azure e Qualtrics.  

O cenário descrito neste tutorial assume que já tem os seguintes itens:

* Uma subscrição do Azure válida
* Um Qualtrics-início de sessão único (SSO) ativada subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que tem atribuídos ao Qualtrics será capazes de início de sessão único para a aplicação no site da sua empresa Qualtrics (serviço fornecedor iniciada pelo início de sessão) ou utilizando o [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos modulares seguintes:

1. Ativar a integração de aplicações para Qualtrics
2. Configurar início de sessão único (SSO)
3. Configurar o aprovisionamento de utilizadores
4. Atribuir utilizadores

![Cenário](./media/active-directory-saas-qualtrics-tutorial/IC789542.png "cenário")

## <a name="enabling-the-application-integration-for-qualtrics"></a>Ativar a integração de aplicações para Qualtrics
O objetivo desta secção consiste em descrevem como ativar a integração de aplicações para Qualtrics.

**Para ativar a integração de aplicações para Qualtrics, execute os seguintes passos:**

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **do Active Directory**.
   
   ![Do Active Directory](./media/active-directory-saas-qualtrics-tutorial/IC700993.png "do Active Directory")
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
   ![Aplicações](./media/active-directory-saas-qualtrics-tutorial/IC700994.png "aplicações")
4. Clique em **adicionar** na parte inferior da página.
   
   ![Adicionar aplicação](./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Adicionar aplicação")
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
   ![Adicionar uma aplicação de gallerry](./media/active-directory-saas-qualtrics-tutorial/IC749322.png "adicionar uma aplicação de gallerry")
6. No **caixa pesquisa**, tipo **Qualtrics**.
   
   ![Galeria de aplicações](./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Galeria de aplicações")
7. No painel de resultados, selecione **Qualtrics**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
   ![Qualtrics](./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
   
## <a name="configure-single-sign-on"></a>Configurar o início de sessão único

O objetivo desta secção consiste em descrevem como permitir que os utilizadores para se autenticar Qualtrics à respetiva conta no Azure AD com Federação com base no protocolo SAML.

**Para configurar o início de sessão único, execute os seguintes passos:**

1. No portal clássico do Azure, no **Qualtrics** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar início de sessão único** caixa de diálogo.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-qualtrics-tutorial/IC789545.png "configurar o início de sessão único")
2. No **como gostaria aos utilizadores iniciar sessão Qualtrics** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-qualtrics-tutorial/IC789546.png "configurar o início de sessão único")
3. No **configurar o URL da aplicação** na página a **Qualtrics no URL de sessão** caixa de texto, escreva o URL (por ex.: "*https://ssotest2ut1.qualtrics.com*") e, em seguida, clique em **seguinte**.
   
   ![Configurar o URL da aplicação](./media/active-directory-saas-qualtrics-tutorial/IC789547.png "configurar o URL da aplicação")
4. No **configurar o início de sessão único em Qualtrics** página, clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados no seu computador.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-qualtrics-tutorial/IC789548.png "configurar o início de sessão único")
5. Envie o ficheiro de metadados para a equipa de suporte Qualtrics.
   
   >[!NOTE]
   >A configuração de SSO tem de ser efetuadas pela equipa de suporte Qualtrics. Irá receber uma notificação, assim que a configuração foi concluída.
   > 
   > 
6. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída** para fechar o **configurar início de sessão único** caixa de diálogo.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-qualtrics-tutorial/IC789549.png "configurar o início de sessão único")
   
## <a name="configure-user-provisioning"></a>Configurar o aprovisionamento de utilizadores

Não há nenhum item de ação para configurar o aprovisionamento para Qualtrics de utilizadores. Quando um utilizador atribuído tenta iniciar sessão Qualtrics através do painel de acesso, Qualtrics verifica se o utilizador existe.  

Se não existe nenhuma conta de utilizador ainda estão disponíveis, é criado automaticamente pelo Qualtrics.

## <a name="assign-users"></a>Atribuir utilizadores
Para testar a configuração, tem de conceder os utilizadores do Azure AD que pretende permitir a utilizar o acesso de aplicação para o mesmo atribuindo-los.

**Para atribuir utilizadores a Qualtrics, execute os seguintes passos:**

1. No portal clássico do Azure, crie uma conta de teste.
2. No **Qualtrics** página de integração de aplicações, clique em **atribuir utilizadores**.
   
   ![Atribuir utilizadores](./media/active-directory-saas-qualtrics-tutorial/IC789550.png "atribuir utilizadores")
3. Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a sua atribuição.
   
   ![Sim](./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Sim")

Se pretender testar as definições de SSO, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

