---
title: "Tutorial: Integração do Azure Active Directory com o Tableau Online | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Tableau Online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: 443fab1198a91a4d5749e6421f7b8603fc75a81e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Tutorial: Integração do Azure Active Directory com o Tableau Online

Neste tutorial, irá aprender a integrar o Tableau Online com o Azure Active Directory (Azure AD).

Integrar o Tableau Online com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Tableau Online
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o Tableau Online (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Tableau Online, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Tableau Online-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Tableau Online a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-tableau-online-from-the-gallery"></a>Adicionar Tableau Online a partir da Galeria
Para configurar a integração do Tableau Online com o Azure AD, terá de adicionar Tableau Online a Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Tableau Online a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Tableau Online**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_search.png)

5. No painel de resultados, selecione **Tableau Online**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o Tableau Online de acordo com um utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo Tableau online é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Tableau Online tem de ser estabelecida.

Online Tableau, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o Tableau Online, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Tableau Online](#creating-a-tableau-online-test-user)**  - para ter um homólogo de Britta Simon Tableau Online que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Tableau Online.

**Para configurar o Azure AD-início de sessão único com o Tableau Online, execute os seguintes passos:**

1. No portal do Azure, no **Tableau Online** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

3. No **domínio Online Tableau e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. No **URL de início de sessão** caixa de texto, escreva o URL:`https://sso.online.tableau.com`

    b. No **identificador** caixa de texto, escreva o URL:`https://sso.online.tableau.com/public/sp/<instancename>`

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_general_400.png)

6. Numa janela do browser diferente, início de sessão para a aplicação Tableau Online. Aceda a **definições** e, em seguida, **autenticação**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
    
7. Para ativar SAML, em **tipos de autenticação** secção. Verifique o **-início de sessão único com o SAML** caixa de verificação.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

8. Desloque para baixo até **ficheiro de metadados de importação para o Tableau Online** secção.  Clique em Procurar e importar o ficheiro de metadados que transferiu a partir do Azure AD. Em seguida, clique em **aplicar**.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

9. No **corresponder asserções** secção, insira o nome de asserção correspondente do fornecedor de identidade para **endereço de correio eletrónico**, **nome próprio**, e **Apelido**. Para obter estas informações do Azure AD: 
  
    a. No portal do Azure, siga o **Tableau Online** página de integração de aplicações.
    
    b. Na secção de atributos, selecione o **"ver e editar todos os outros atributos de utilizador"** caixa de verificação. 
    
   ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/attributesection.png)
      
    c. Copie o valor de espaço de nomes para estes atributos: givenname, e-mail e o apelido, utilizando os seguintes passos:

   ![Azure AD início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Clique em **user.givenname** valor 
    
    e. Copie o valor da **espaço de nomes** caixa de texto.

   ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/attributesection2.png)

    f. Para copiar o namesapce valores para o e-mail e o apelido, siga os passos anteriores.

    g. Mudar para a aplicação Tableau Online, em seguida, defina o **atributos Online Tableau** secção da seguinte forma:
     * E-mail: **correio** ou **userprincipalname**
     * Nome próprio: **givenname**
     * Apelido: **Apelido**
   
   ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-tableau-online-test-user"></a>Criar um utilizador de teste Tableau Online

Nesta secção, vai criar um utilizador chamado Britta Simon Tableau online.

1. No **Tableau Online**, clique em **definições** e, em seguida, **autenticação** secção. Desloque para baixo até **selecionar utilizadores** secção. Clique em **adicionar utilizadores** e, em seguida, **introduza os endereços de correio eletrónico**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Selecione **adicionar utilizadores para a autenticação de início de sessão único (SSO)**. No **Introduza endereços de correio eletrónico** adicionar caixa de textobritta.simon@contoso.com
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Clique em **Criar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para o Tableau Online.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Tableau Online, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Tableau Online**.

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico Tableau Online no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação Tableau Online.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png

