---
title: "Tutorial: Integração do Azure Active Directory com o Software de produtividade Wizergos | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Software de produtividade Wizergos."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
ms.openlocfilehash: 73b3bc05aeb337c12acb7e47c0dbebe6d0196530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Tutorial: Integração do Azure Active Directory com o Software de produtividade Wizergos
O objetivo deste tutorial é para lhe mostrar como integrar o Software de produtividade Wizergos com o Azure Active Directory (Azure AD).

Integrar o Software de produtividade Wizergos com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Software de produtividade Wizergos
* Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o Software de produtividade Wizergos-início de sessão único (SSO) com as respetivas contas do Azure AD
* Pode gerir as contas numa localização central - portal clássico do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Software de produtividade Wizergos, terá dos seguintes itens:

* Uma subscrição do Azure AD
* Uma subscrição de Wizergos produtividade Software SSO ativado

>[!NOTE]
>Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção. 
> 

Para testar os passos neste tutorial, deve seguir estas recomendações:

* Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
* Se não tiver um ambiente de avaliação do Azure AD, pode obter um [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir-lhe testar o SSO do Azure AD num ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Wizergos produtividade Software a partir da galeria do
2. Configurar e testar o SSO do Azure AD

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Adicionar Wizergos produtividade Software a partir da galeria do
Para configurar a integração do Software de produtividade Wizergos com o Azure AD, terá de adicionar Wizergos produtividade Software a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Wizergos produtividade Software a partir da galeria, execute os seguintes passos:**

1. No **Portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**. 
   
    ![Active Directory][1]
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Aplicações][2]
4. Clique em **adicionar** na parte inferior da página.
   
    ![Aplicações][3]
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
    ![Aplicações][4]
6. Na caixa de pesquisa, escreva **Wizergos produtividade Software**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_01.png)
7. No painel de resultados, selecione **Wizergos produtividade Software**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
    ![Selecionar a aplicação na Galeria](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD
O objetivo desta secção consiste em Mostrar-lhe como configurar e testar o SSO do Azure AD com o Software de produtividade Wizergos com base num utilizador de teste chamado "Britta Simon".

Para SSO funcionar, do Azure AD tem de saber o que é o utilizador homólogo Wizergos produtividade Software para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Software de produtividade Wizergos tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor do **Username** Wizergos produtividade software.

Para configurar e testar o Azure AD-início de sessão único com BynWizergos produtividade Softwareder, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD-início de sessão único](#configuring-azure-ad-single-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Software de produtividade Wizergos](#creating-a-wizergos-productivity-software-test-user)**  - para ter um homólogo de Britta Simon Wizergos produtividade Software que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-sso"></a>Configurar o SSO do Azure AD
Nesta secção, pode ativar do Azure AD início de sessão no portal clássico e configurar o início de sessão único na sua aplicação Wizergos produtividade Software.

**Para configurar o Azure AD-início de sessão único com o Software de produtividade Wizergos, execute os seguintes passos:**

1. No portal clássico, no **Wizergos produtividade Software** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar Single Sign-On** caixa de diálogo.
   
    ![Configurar o início de sessão único][6] 
2. No **como gostaria aos utilizadores iniciar sessão Software de produtividade Wizergos** página, selecione **do Azure AD Single Sign-On**e, em seguida, clique em **seguinte**:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_03.png)
3. No **configurar definições de aplicação** página da caixa de diálogo, clique em **seguinte**:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_04.png)
4. No **configurar o início de sessão único em Wizergos produtividade Software** página, clique em **Transferir certificado**e, em seguida, guarde o ficheiro no seu computador:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_05.png)
5. Numa janela do browser web diferente, início de sessão no seu inquilino Wizergos produtividade Software como um administrador.
6. No hamburger menu, selecione **Admin**.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)
7. Na página de administrador no menu esquerdo selecione **autenticação** e clique em **do Azure AD**.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)
8. Execute os seguintes passos no **autenticação** secção.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
  1. Clique em **carregar** botão para carregar o certificado transferido do Azure AD. 
  2. No **URL do emissor** textbox colocar o valor de **URL do emissor** do Assistente de configuração de aplicações do Azure AD.
  3. No **URL de início de sessão único** textbox colocar o valor de **URL Single Sign-on serviço** do Assistente de configuração de aplicações do Azure AD.
  4. No **único URL de Sign-Out** textbox colocar o valor de **único URL de serviço Sign-out** do Assistente de configuração de aplicações do Azure AD.
  5. Clique em **guardar** botão.
9. No portal clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
   
    ![Azure AD início de sessão único][10]
10. No **único início de sessão confirmação** página, clique em **concluída**.  
    
    ![Azure AD início de sessão único][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal clássico do chamado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_09.png)
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para apresentar a lista de utilizadores, no menu na parte superior, clique em **utilizadores**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)
4. Para abrir o **adicionar utilizador** caixa de diálogo, na barra de ferramentas na parte inferior, clique em **adicionar utilizador**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)
5. No **diga-nos informações sobre este utilizador** diálogo página, execute os seguintes passos:
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_05.png) 
  1. Como tipo de utilizador, selecione o novo utilizador na sua organização.
  2. O nome de utilizador **textbox**, tipo **BrittaSimon**.
  3. Clique em **Seguinte**.
6. No **perfil de utilizador** diálogo página, execute os seguintes passos:
   
   ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_06.png)
  1. No **nome próprio** caixa de texto, tipo **Britta**.  
  2. No **Apelido** caixa de texto, tipo, **Simon**.
  3. No **nome a apresentar** caixa de texto, tipo **Britta Simon**.
  4. No **função** lista, selecione **utilizador**.
  5. Clique em **Seguinte**.
7. No **Get palavra-passe temporária** página da caixa de diálogo, clique em **criar**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_07.png)
8. No **Get palavra-passe temporária** diálogo página, execute os seguintes passos:
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_08.png)
  1. Anote o valor da **nova palavra-passe**.
  2. Clique em **Concluído**.   

### <a name="create-a-wizergos-productivity-software-test-user"></a>Criar um utilizador de teste Wizergos produtividade Software
Nesta secção, vai criar um utilizador chamado Britta Simon Wizergos produtividade software. . Trabalhar com a equipa de suporte através do Software de produtividade Wizergos [ support@wizergos.com ](emailTo:support@wizergos.com) para adicionar os utilizadores na plataforma Wizergos produtividade Software.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD
O objetivo desta secção consiste em Ativar Britta Simon utilizar o SSO do Azure ao conceder o acesso ao Software de produtividade Wizergos.

  ![Atribua o utilizador][200]

**Para atribuir Britta Simon Wizergos produtividade software, execute os seguintes passos:**

1. No portal clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Atribua o utilizador][201]
2. Na lista de aplicações, selecione **Wizergos produtividade Software**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_50.png)
3. No menu na parte superior, clique em **utilizadores**.
   
    ![Atribua o utilizador][203]
4. Na lista de utilizadores, selecione **Britta Simon**.
5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
   
    ![Atribua o utilizador][205]

### <a name="test-single-sign-on"></a>Teste o início de sessão único
O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico do Software de produtividade Wizergos no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Software de produtividade Wizergos.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_205.png
