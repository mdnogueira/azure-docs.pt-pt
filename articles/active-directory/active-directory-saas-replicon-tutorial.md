---
title: "Tutorial: Integração do Azure Active Directory com Replicon | Microsoft Docs"
description: "Saiba como utilizar Replicon com o Azure Active Directory para ativar o início de sessão único, aprovisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 2aeeceb61191962b62892b8409218684f76c6fa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Tutorial: Integração do Azure Active Directory com Replicon
O objetivo deste tutorial é mostrar a integração do Azure e Replicon. O cenário descrito neste tutorial assume que já tem os seguintes itens:

* Uma subscrição do Azure válida
* Um inquilino Replicon

Depois de concluir este tutorial, os utilizadores do Azure AD que tem atribuídos ao Replicon será capazes de início de sessão único para a aplicação no site da sua empresa Replicon (serviço fornecedor iniciada pelo início de sessão) ou utilizando o [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos modulares seguintes:

1. Ativar a integração de aplicações para Replicon
2. Configurar início de sessão único (SSO)
3. Configurar o aprovisionamento de utilizadores
4. Atribuir utilizadores

![Cenário](./media/active-directory-saas-replicon-tutorial/IC777798.png "cenário")

## <a name="enable-the-application-integration-for-replicon"></a>Ativar a integração de aplicações para Replicon
O objetivo desta secção consiste em descrevem como ativar a integração de aplicações para Replicon.

**Para ativar a integração de aplicações para Replicon, execute os seguintes passos:**

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Do Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "do Active Directory")
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Aplicações](./media/active-directory-saas-replicon-tutorial/IC700994.png "aplicações")
4. Clique em **adicionar** na parte inferior da página.
   
    ![Adicionar aplicação](./media/active-directory-saas-replicon-tutorial/IC749321.png "Adicionar aplicação")
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
    ![Adicionar uma aplicação de gallerry](./media/active-directory-saas-replicon-tutorial/IC749322.png "adicionar uma aplicação de gallerry")
6. No **caixa pesquisa**, tipo **Replicon**.
   
    ![Galeria de aplicações](./media/active-directory-saas-replicon-tutorial/IC777799.png "Galeria de aplicações")
7. No painel de resultados, selecione **Replicon**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
   
## <a name="configure-single-sign-on"></a>Configurar o início de sessão único

O objetivo desta secção consiste em descrevem como permitir que os utilizadores para se autenticar Replicon à respetiva conta no Azure AD com Federação com base no protocolo SAML.

**Para configurar o início de sessão único, execute os seguintes passos:**

1. No portal clássico do Azure, no **Replicon** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar início de sessão único** caixa de diálogo.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-replicon-tutorial/IC777801.png "configurar o início de sessão único")
2. No **como gostaria aos utilizadores iniciar sessão Replicon** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-replicon-tutorial/IC777802.png "configurar o início de sessão único")
3. No **URL da aplicação configurar** página, execute os seguintes passos:
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-replicon-tutorial/IC777803.png "configurar o URL da aplicação")
  1. No **Replicon no URL sessão** caixa de texto, escreva o URL de inquilino Replicon (por ex.: *https://na2.replicon.com/company/saml2/sp-sso/post*).
  2. No **URL de resposta de Replicon** caixa de texto, escreva o seu Replicon **AssertionConsumerService** URL (por ex.: *https://global.replicon.com/! / saml2/empresa/sso/post*).  
      
     >[!NOTE]
     >Pode obter o URL dos metadados Replicon em: **https://global.replicon.com/! /saml2/\<YourCompanyKey\>**.
     > 
     > 
 
  3. Clique em **Seguinte**.

4. No **configurar o início de sessão único em Replicon** página, para transferir os seus metadados, clique em **transferir metadados**e, em seguida, guardar os metadados no seu computador.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-replicon-tutorial/IC777804.png "configurar o início de sessão único")
5. Numa janela do browser web diferente, inicie sessão no site da sua empresa Replicon como administrador.

6. Para configurar SAML 2.0, execute os seguintes passos:
   
    ![Ativar a autenticação SAML](./media/active-directory-saas-replicon-tutorial/IC777805.png "autenticação ativar SAML")
  
  1. Para apresentar o **EnableSAML Authentication2** caixa de diálogo, acrescentar o seguinte para o URL, após a sua chave de empresa: **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**  
    * O seguinte mostra o esquema do URL concluído:  
   **https://Na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
   2. Clique em de  **+**  para expandir o **v20Configuration** secção.
   3. Clique em de  **+**  para expandir o **metaDataConfiguration** secção.
   4. Clique em **Escolher ficheiro**, para selecionar o ficheiro XML de metadados de fornecedor de identidade e clique em **submeter**.

7. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída** para fechar o **configurar início de sessão único** caixa de diálogo.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-replicon-tutorial/IC778418.png "configurar o início de sessão único")
   
## <a name="configure-user-provisioning"></a>Configurar o aprovisionamento de utilizadores

Para permitir que os utilizadores do Azure AD sessão Replicon, têm de ser aprovisionados para Replicon.  

No caso de Replicon, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Numa janela do browser web, inicie sessão no site da sua empresa Replicon como administrador.
2. Aceda a **administração \> utilizadores**.
   
    ![Os utilizadores](./media/active-directory-saas-replicon-tutorial/IC777806.png "utilizadores")
3. Clique em **+ adicionar utilizador**.
   
    ![Adicionar utilizador](./media/active-directory-saas-replicon-tutorial/IC777807.png "adicionar utilizador")
4. No **perfil de utilizador** secção, execute os seguintes passos:
   
    ![Perfil de utilizador](./media/active-directory-saas-replicon-tutorial/IC777808.png "perfil de utilizador")
   
  1. No **nome de início de sessão** caixa de texto, o Azure AD de tipo de endereço de correio eletrónico do utilizador do Azure AD que pretende aprovisionar.
  2. Como **tipo de autenticação**, selecione **SSO**.
  3. No **departamento** caixa de texto, escreva o departamento do utilizador.
  4. Como **empregado tipo**, selecione **administrador**.
  5. Clique em **guardar o perfil de utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras Replicon utilizador conta criação ferramentas ou APIs fornecidas pelo Replicon para aprovisionar contas de utilizador do AAD.
> 
> 

## <a name="assign-users"></a>Atribuir utilizadores
Para testar a configuração, tem de conceder os utilizadores do Azure AD que pretende permitir a utilizar o acesso de aplicação para o mesmo atribuindo-los.

**Para atribuir utilizadores a Replicon, execute os seguintes passos:**

1. No portal clássico do Azure, crie uma conta de teste.

2. No **Replicon** página de integração de aplicações, clique em **atribuir utilizadores**.
   
    ![Atribuir utilizadores](./media/active-directory-saas-replicon-tutorial/IC777809.png "atribuir utilizadores")

3. Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a sua atribuição.
   
    ![Sim](./media/active-directory-saas-replicon-tutorial/IC767830.png "Sim")

Se pretender testar as definições de início de sessão único, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

