---
title: "Tutorial: Integração do Azure Active Directory com Promapp | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Promapp."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jeedes
ms.openlocfilehash: a9cd70b048d454009d8741f394fed0b6b93fcab7
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Tutorial: Integração do Azure Active Directory com Promapp

Neste tutorial, irá aprender a integrar Promapp com o Azure Active Directory (Azure AD).

Integrar Promapp com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Promapp
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Promapp (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Promapp, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Promapp-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Promapp a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-promapp-from-the-gallery"></a>Adicionar Promapp a partir da Galeria
Para configurar a integração de Promapp com o Azure AD, terá de adicionar Promapp a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Promapp a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Promapp**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_search.png)

5. No painel de resultados, selecione **Promapp**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Promapp com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Promapp é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Promapp tem de ser estabelecida.

No Promapp, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Promapp, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Promapp](#creating-a-promapp-test-user)**  - para ter um homólogo de Britta Simon Promapp que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Promapp.

**Para configurar o Azure AD-início de sessão único com Promapp, execute os seguintes passos:**

1. No portal do Azure, no **Promapp** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_samlbase.png)

3. No **Promapp domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:
    | |
    |--|
    | `https://demo.promapp.com/TENANTNAME`|
    | `https://go.promapp.com/TENANTNAME`|
    | `https://demoau.promapp.com/TENANTNAME`|
    | `https://au.promapp.com/TENANTNAME`|
    | `https://demous.promapp.com/TENANTNAME`|
    | `https://us.promapp.com/TENANTNAME`|
    | `https://dev.promapp.com/TENANTNAME`|
    | `https://test.promapp.com/TENANTNAME`|
    | `https://staging.promapp.com/TENANTNAME`|
    
    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://DOMAINNAME.promapp.com/azuread/saml/authenticate.aspx`

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_url1.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://DOMAINNAME.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente Promapp](https://www.promapp.com/about-us/contact-us/) para obter estes valores.

5. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-promapp-tutorial/tutorial_general_400.png)

7. No **Promapp configuração** secção, clique em **configurar Promapp** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_configure.png) 

8. Início de sessão no site da sua empresa Promapp como administrador. 

9. No menu na parte superior, clique em **Admin**. 
   
    ![Azure AD início de sessão único][12]

10. Clique em **Configurar**. 
   
    ![Azure AD início de sessão único][13]

11. No **segurança** caixa de diálogo, execute os seguintes passos:
   
    ![Azure AD início de sessão único][14]
    
    a. Colar **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure para o **URL de início de sessão SSO** caixa de texto.
    
    b. Como **SSO - o início de sessão único modo**, selecione **opcional**e, em seguida, clique em **guardar**.

    > [!NOTE]
    > **Opcional** modo destina-se apenas a testar. Quando estiver satisfeito com a configuração, selecione **necessário** modo para impor que todos os utilizadores se autentiquem com o Azure AD.

    c. Abra o certificado transferido no bloco de notas, copie o conteúdo de certificado sem a primeira linha (---**começar certificado**---) e a última linha (---**certificado final**---), cole-o a  **Certificado SSO-x. 509** caixa de texto e, em seguida, clique em **guardar**.
        
> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-promapp-test-user"></a>Criar um utilizador de teste Promapp

A aplicação de Promapp suporta Just-in-Time o aprovisionamento. Isto significa uma conta de utilizador é criada automaticamente se necessário durante uma tentativa de aceder à aplicação através do painel de acesso.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Promapp.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Promapp, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Promapp**.

    ![Configurar o início de sessão único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Para testar a aplicação no **IDP** modo iniciado, quando clica no mosaico Promapp no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Promapp.

Para testar a aplicação no **SP** iniciada modo, terá de iniciar a autenticação do seu site Promapp. Isto pode ser feito por deixar o campo de palavra-passe em branco quando iniciar sessão enfrenta **opcional** modo está ativado.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[100]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png

