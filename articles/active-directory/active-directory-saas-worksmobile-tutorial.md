---
title: "Tutorial: Integração do Azure Active Directory com FUNCIONA MOBILE | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e FUNCIONA MOBILE."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 760fbb21c86fa14808f9a946c2c2fd0814bfb139
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-works-mobile"></a>Tutorial: Integração do Azure Active Directory com FUNCIONA MOBILE

Neste tutorial, irá aprender a integrar o MOBILE FUNCIONA com o Azure Active Directory (Azure AD).

Integrar o MOBILE FUNCIONA com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao MOBILE FUNCIONA
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para MOBILE FUNCIONA (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com FUNCIONA móveis, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um FUNCIONA MOBILE-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar MOBILE FUNCIONA na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-works-mobile-from-the-gallery"></a>Adicionar MOBILE FUNCIONA na galeria do
Para configurar a integração do MOBILE FUNCIONA com o Azure AD, tem de adicionar MOBILE FUNCIONA na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar MOBILE FUNCIONA na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **FUNCIONA MOBILE**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_search.png)

5. No painel de resultados, selecione **FUNCIONA MOBILE**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com MOBILE FUNCIONA com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no FUNCIONA MOBILE for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no FUNCIONA MOBILE tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor do **Username** no FUNCIONA MOBILE.

Para configurar e testar o Azure AD-início de sessão único com FUNCIONA móveis, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste FUNCIONA MOBILE](#creating-a-works-mobile-test-user)**  - para ter um homólogo de Britta Simon MOBILE FUNCIONA que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação móvel FUNCIONA.

**Para configurar o Azure AD-início de sessão único com MOBILE FUNCIONA, execute os seguintes passos:**

1. No portal do Azure, no **FUNCIONA MOBILE** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_samlbase.png)

3. No **FUNCIONA MOBILE domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://auth.worksmobile.com/d/login/{domain}/?userId={ID@domain}`

    b. No **identificador** caixa de texto, digite o valor como`worksmobile.com`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de MOBILE FUNCIONA](mailto:dl_ssoinfo@worksmobile.com) para obter este valor. 
 
4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Raw)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_400.png)

6. No **FUNCIONA MOBILE configuração** secção, clique em **configurar MOBILE de FUNCIONA** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_configure.png) 

7. Para obter SSO configurado para a sua aplicação, contacte [equipa de suporte de FUNCIONA MOBILE](mailto:dl_ssoinfo@worksmobile.com) e forneça-los com as seguintes informações: 

    • O transferido **ficheiro de certificado**

    • O **URL do serviço de início de sessão único SAML**

    • O **ID de entidade SAML**

    • O **URL de início de sessão**

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-works-mobile-test-user"></a>Criar um utilizador de teste FUNCIONA MOBILE

 Nesta secção, vai criar um utilizador chamado Britta Simon FUNCIONA MOBILE. Consulte [equipa de suporte de FUNCIONA MOBILE](mailto:dl_ssoinfo@worksmobile.com) para adicionar os utilizadores na plataforma móvel FUNCIONA.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder acesso ao MOBILE FUNCIONA.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a MOBILE FUNCIONA, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **FUNCIONA MOBILE**.

    ![Configurar o início de sessão único](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, pode testa a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico MOBILE FUNCIONA no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de MOBILE FUNCIONA.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_203.png

