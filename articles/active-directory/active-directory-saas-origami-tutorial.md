---
title: "Tutorial: Integração do Azure Active Directory com Origami | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Origami."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 3420409b72ff032e64ac59365083dd141dfc3c1b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Tutorial: Integração do Azure Active Directory com Origami

Neste tutorial, irá aprender a integrar Origami com o Azure Active Directory (Azure AD).

Integrar Origami com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Origami
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Origami (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Origami, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Origami-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Origami a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-origami-from-the-gallery"></a>Adicionar Origami a partir da Galeria
Para configurar a integração de Origami com o Azure AD, terá de adicionar Origami a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Origami a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Origami**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_search.png)

5. No painel de resultados, selecione **Origami**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Origami com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Origami é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Origami tem de ser estabelecida.

No Origami, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Origami, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Origami](#creating-an-origami-test-user)**  - para ter um homólogo de Britta Simon Origami que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Origami.

**Para configurar o Azure AD-início de sessão único com Origami, execute os seguintes passos:**

1. No portal do Azure, no **Origami** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_samlbase.png)

3. No **Origami domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Origami](https://wordpress.org/support/theme/origami) para obter o valor. 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_general_400.png)

6. No **Origami configuração** secção, clique em **configurar Origami** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_configure.png) 

7. Inicie sessão conta Origami com direitos de administrador.

8. No menu na parte superior, clique em **Admin**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

9. Na página de caixa de diálogo de início de sessão único em configuração, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_531.png)

    a. Selecione **ativar o início de sessão único em**.

    b. No **início de sessão no URL do fornecedor de identidade da página** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.

    c. No **URL de página do fornecedor de identidade Sign-out** caixa de texto, cole o valor de **Sign-Out URL**, que copiou do portal do Azure.

    d. Clique em **procurar** para carregar o certificado que transferiu do portal do Azure.

    e. Clique em **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-origami-test-user"></a>Criar um utilizador de teste Origami

Nesta secção, vai criar um utilizador chamado Britta Simon Origami. 

1. Inicie sessão conta Origami com direitos de administrador.

2. No menu na parte superior, clique em **Admin**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

3. No **utilizadores e de segurança** caixa de diálogo, clique em **utilizadores**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)

4. Clique em **adicionar novo utilizador**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)

5. Na caixa de diálogo Adicionar novo utilizador, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)

    a. No **nome de utilizador** caixa de texto, introduza o e-mail do utilizador, como  **brittasimon@contoso.com** .

    b. No **palavra-passe** caixa de texto, escreva um palavra-passe.

    c. No **Confirmar palavra-passe** caixa de texto, escreva a palavra-passe novamente.

    d. No **nome próprio** caixa de texto, introduza o nome de utilizador como **Britta**.

    e. No **Apelido** caixa de texto, introduza o apelido do utilizador, como **Simon**.

    f. Clique em **Guardar**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)

6. Atribuir **funções de utilizador** e **acesso de cliente** ao utilizador. 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Origami.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Origami, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Origami**.

    ![Configurar o início de sessão único](./media/active-directory-saas-origami-tutorial/tutorial_origami_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Origami no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Origami.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-origami-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png

