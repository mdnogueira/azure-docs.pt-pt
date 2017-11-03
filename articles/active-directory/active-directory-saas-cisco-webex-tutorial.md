---
title: "Tutorial: Integração do Azure Active Directory com o Cisco Webex | Microsoft Docs"
description: "Saiba como utilizar o Cisco Webex com o Azure Active Directory para ativar o início de sessão único, aprovisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: b44b1a5b3e988a51db3325ec8a181651fa84e768
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: Integração do Azure Active Directory com Cisco Webex
O objetivo deste tutorial é mostrar a integração do Azure e Cisco Webex.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

* Uma subscrição do Azure válida
* Um inquilino Cisco Webex

Depois de concluir este tutorial, os utilizadores do Azure AD que tem atribuídos ao Cisco Webex será capazes de início de sessão único para a aplicação no site da sua empresa Cisco Webex (serviço fornecedor iniciada pelo início de sessão) ou utilizando o [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos blocos modulares seguintes:

* Ativar a integração de aplicações para Cisco Webex
* Configurar início de sessão único (SSO)
* Configurar o aprovisionamento de utilizadores
* Atribuir utilizadores

![Cenário](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "cenário")

## <a name="enable-the-application-integration-for-cisco-webex"></a>Ativar a integração de aplicações para Cisco Webex
O objetivo desta secção consiste em descrevem como ativar a integração de aplicações para Cisco Webex.

**Para ativar a integração de aplicações para Cisco Webex, execute os seguintes passos:**

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **do Active Directory**.
   
   ![Do Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "do Active Directory")
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
   ![Aplicações](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "aplicações")
4. Clique em **adicionar** na parte inferior da página.
   
   ![Adicionar aplicação](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Adicionar aplicação")
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
   ![Adicionar uma aplicação de gallerry](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "adicionar uma aplicação de gallerry")
6. No **caixa pesquisa**, tipo **Cisco Webex**.
   
   ![Galeria de aplicações](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Galeria de aplicações")
7. No painel de resultados, selecione **Cisco Webex**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
   ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
   
## <a name="configure-single-sign-on"></a>Configurar o início de sessão único

O objetivo desta secção consiste em descrevem como permitir que os utilizadores para se autenticar Cisco Webex à respetiva conta no Azure AD com Federação com base no protocolo SAML.  

Como parte deste procedimento, é necessário para criar um certificado codificado base-64. Se não estiver familiarizado com este procedimento, consulte [como converter um certificado de binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

**Para configurar o início de sessão único, execute os seguintes passos:**

1. No portal clássico do Azure, no **Cisco Webex** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar início de sessão único** caixa de diálogo.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "configurar o início de sessão único")
2. No **como gostaria aos utilizadores iniciar sessão Cisco Webex** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "configurar o início de sessão único")
3. No **URL da aplicação configurar** página, execute os passos seguintes e, em seguida, clique em **seguinte**.
   
   ![Configurar o URL da aplicação](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "configurar o URL da aplicação")   
   1. No **iniciar sessão no URL** caixa de texto, escreva o URL de inquilino Cisco Webex (por ex.: *http://contoso.webex.com*).
   2. No **URL de resposta de Webex Cisco** caixa de texto, tipo sua **Cisco Webex AssertionConsumerService URL** (por ex.: *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).
4. No **configurar o início de sessão único em Cisco Webex** página, para transferir o certificado, clique em **Transferir certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "configurar o início de sessão único")
5. Numa janela do browser web diferente, inicie sessão no site da sua empresa Cisco Webex como administrador.
6. No menu na parte superior, clique em **administração de sites**.
   
   ![Administração de sites](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "administração de sites")
7. No **Gerir Site** secção, clique em **SSO configuração**.
   
   ![Configuração de SSO](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO configuração")
8. Na secção de configuração de SSO Web Federado, execute os seguintes passos:
   
   ![Federado SSO configuração](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "federado SSO configuração")  
   1. Do **protocolo Federation** lista, selecione **SAML 2.0**.
   2. Criar um **com codificação Base 64** ficheiro a partir do seu certificado transferido.  
    >[!TIP]
    >Para obter mais detalhes, consulte [como converter um certificado de binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)
    >  
   3. Abra o certificado codificado base-64 no bloco de notas e, em seguida, copie o conteúdo do mesmo.
   4. Clique em **importar metadados de SAML**e, em seguida, cole o certificado codificado base-64.
   5. No portal clássico do Azure, no **configurar o início de sessão único em Cisco Webex** página da caixa de diálogo, copie o **URL do emissor** valor e, em seguida, cole-o para o **emissor para SAML (ID de IdP)** caixa de texto.
   6. No portal clássico do Azure, no **configurar o início de sessão único em Cisco Webex** página da caixa de diálogo, copie o **URL de início de sessão remoto** valor e, em seguida, cole-o para o **URL de início de sessão do serviço do cliente SSO** caixa de texto.
   7. Do **NameID formato** lista, selecione **endereço de correio eletrónico**.
   8. No **AuthnContextClassRef** caixa de texto, tipo **urn: oasis: os nomes: tc: SAML:2.0:ac:classes:Password**.
   9. No portal clássico do Azure, no **configurar o início de sessão único em Cisco Webex** página da caixa de diálogo, copie o **URL de fim de sessão remoto** valor e, em seguida, cole-o para o **URL de fim de sessão do serviço do cliente SSO** caixa de texto.
   10. Clique em **atualização**.
9. No portal clássico do Azure, no **configurar o início de sessão único em Cisco Webex** página da caixa de diálogo, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída**.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "configurar o início de sessão único")
   
## <a name="configure-user-provisioning"></a>Configurar o aprovisionamento de utilizadores

Para permitir que os utilizadores do Azure AD sessão Cisco Webex, têm de ser aprovisionados para Cisco Webex.  

* No caso de Cisco Webex, o aprovisionamento é uma tarefa manual.

**Aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Cisco Webex** inquilino.
2. Aceda a **gerir utilizadores \> adicionar utilizador**.
   
   ![Adicionar utilizadores](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "adicionar utilizadores")
3. A secção de adicionar utilizadores, execute os seguintes passos:
   
   ![Adicionar utilizador](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "adicionar utilizador")   
   1. Como **tipo de conta**, selecione **anfitrião**.
   2. Escreva as informações de um utilizador do Azure AD existente no seguintes caixas de texto: **nome próprio, apelido**, **nome de utilizador**, **E-Mail**, **palavra-passe**, **Confirmar palavra-passe**.
   3. Clique em **Adicionar**.

>[!NOTE]
>Pode utilizar quaisquer outras Cisco Webex utilizador conta criação ferramentas ou APIs fornecidas pelo Cisco Webex para aprovisionar contas de utilizador do AAD. 
> 

## <a name="assign-users"></a>Atribuir utilizadores
Para testar a configuração, tem de conceder os utilizadores do Azure AD que pretende permitir a utilizar o acesso de aplicação para o mesmo atribuindo-los.

**Para atribuir utilizadores a Cisco Webex, execute os seguintes passos:**

1. No portal clássico do Azure, crie uma conta de teste.
2. No **Cisco Webex** página de integração de aplicações, clique em **atribuir utilizadores**.
   
   ![Atribuir utilizadores](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "atribuir utilizadores")
3. Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a sua atribuição.
   
   ![Sim](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Sim")

Se pretender testar as definições de início de sessão único, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

