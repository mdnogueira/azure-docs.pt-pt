---
title: "Tutorial: Integração do Azure Active Directory com Pingboard | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Pingboard."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: 67534da42ac27989a8b08cec4d6f9f9c31774264
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Tutorial: Integração do Azure Active Directory com Pingboard

Neste tutorial, irá aprender a integrar Pingboard com o Azure Active Directory (Azure AD).

Integrar Pingboard com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Pingboard
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Pingboard (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Pingboard, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Pingboard-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Pingboard a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-pingboard-from-the-gallery"></a>Adicionar Pingboard a partir da Galeria
Para configurar a integração de Pingboard com o Azure AD, terá de adicionar Pingboard a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Pingboard a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![As aplicações da empresa][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Pingboard**, selecione **Pingboard** do painel de resultados e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Pingboard na lista de resultados](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Pingboard com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Pingboard é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Pingboard tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Pingboard.

Para configurar e testar o Azure AD-início de sessão único com Pingboard, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Pingboard](#create-a-pingboard-test-user)**  - para ter um homólogo de Britta Simon Pingboard que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Pingboard.

**Para configurar o Azure AD-início de sessão único com Pingboard, execute os seguintes passos:**

1. No portal do Azure, no **Pingboard** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2.  No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. No **Pingboard domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Domínio Pingboard e URLs único início de sessão informações IDP](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_url.png)

    a. No **identificador** caixa de texto, digite o valor como:`http://app.pingboard.com/sp`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<entity-id>.pingboard.com/auth/saml/consume`

4. Verifique **Mostrar avançadas definições de URL**, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Domínio Pingboard e URLs único início de sessão informações SP](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     No **URL de início de sessão** caixa de texto, escreva o URL a utilizar o padrão do seguinte:`https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE] 
    > Tenha em atenção que estes valores não estiverem reais. Atualize estes valores com o URL de resposta real e o URL de início de sessão. Contacte [equipa de suporte de cliente Pingboard](https://support.pingboard.com/) para obter estes valores.

5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Xml de metadados de Pingboard](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-pingboard-tutorial/tutorial_general_400.png)

7. Para configurar o SSO no lado de Pingboard, abra uma nova janela do browser e inicie sessão na sua conta Pingboard. Tem de ser um administrador de Pingboard para configurar o início de sessão único.

8. No menu superior, selecione **aplicações > integrações**

    ![Configurar o início de sessão único](./media/active-directory-saas-pingboard-tutorial/Pingboard_integration.png)

9.  No **integrações** página, localizar o **"Do Azure Active Directory"** mosaico e clique no mesmo.

    ![Integração de início de sessão único Pingboard](./media/active-directory-saas-pingboard-tutorial/Pingboard_aad.png)

10. No modal que se segue clique **"Configurar"**

    ![Botão de configuração Pingboard](./media/active-directory-saas-pingboard-tutorial/Pingboard_configure.png)

11. Na página seguinte, tenha em atenção que "integração de SSO do Azure está ativada". Abra o ficheiro XML de metadados transferido de um bloco de notas e cole o conteúdo **IDP metadados**.

    ![Ecrã de configuração Pingboard SSO](./media/active-directory-saas-pingboard-tutorial/Pingboard_sso_configure.png)

12. O ficheiro é validado, e se tudo está correto, o início de sessão único irá agora ser ativado.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/active-directory-saas-pingboard-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-pingboard-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Botão Adicionar](./media/active-directory-saas-pingboard-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-pingboard-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-pingboard-test-user"></a>Criar um utilizador de teste Pingboard

Para permitir que os utilizadores do Azure AD sessão Pingboard, têm de ser aprovisionados para Pingboard. Pingboard, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Pingboard como administrador.

2. Clique em **"Adicionar empregado"** botão no **diretório** página.

    ![Adicionar empregado](./media/active-directory-saas-pingboard-tutorial/create_testuser_add.png)

3. No **"Adicionar empregado"** diálogo página, execute os seguintes passos:

    ![Convidar pessoas](./media/active-directory-saas-pingboard-tutorial/create_testuser_name.png)

    a. No **nome completo** caixa de texto, o nome completo de tipo de utilizador como **Britta Simon**.

    b. No **E-Mail** caixa de texto, como o tipo de endereço de correio eletrónico do utilizador  **brittasimon@contoso.com** .

    c. No **cargo** caixa de texto, escreva o título de trabalho de Britta Simon.

    d. No **localização** lista pendente, selecionar a localização do Britta Simon.
    
    e. Clique em **Adicionar**.   

4. Um ecrã de confirmação é fornecido para confirmar a adição de utilizador.
    
    ![Confirmar](./media/active-directory-saas-pingboard-tutorial/create_testuser_confirm.png)
        
    > [!NOTE]
    > Titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue-se de uma ligação para confirmar a respetiva conta para ficar ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Pingboard.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Pingboard, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Pingboard**.

    ![A ligação de Pingboard na lista de aplicações](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

Quando clica no mosaico Pingboard no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Pingboard.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_203.png
