---
title: "Tutorial: Integração do Azure Active Directory com Picturepark | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Picturepark."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: 1c009aa1fdd3140a4466cf762b6c9687e74ce4c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integração do Azure Active Directory com Picturepark

Neste tutorial, irá aprender a integrar Picturepark com o Azure Active Directory (Azure AD).

Integrar Picturepark com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Picturepark
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Picturepark (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Picturepark, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Picturepark-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Picturepark a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-picturepark-from-the-gallery"></a>Adicionar Picturepark a partir da Galeria
Para configurar a integração de Picturepark com o Azure AD, terá de adicionar Picturepark a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Picturepark a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Picturepark**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_search.png)

5. No painel de resultados, selecione **Picturepark**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Picturepark com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Picturepark é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Picturepark tem de ser estabelecida.

No Picturepark, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Picturepark, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Picturepark](#creating-a-picturepark-test-user)**  - para ter um homólogo de Britta Simon Picturepark que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Picturepark.

**Para configurar o Azure AD-início de sessão único com Picturepark, execute os seguintes passos:**

1. No portal do Azure, no **Picturepark** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_samlbase.png)

3. No **Picturepark domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.picturepark.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente Picturepark](https://picturepark.com/about/contact/) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, copie o **THUMBPRINT** valor do certificado.

    ![Configurar o início de sessão único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-picturepark-tutorial/tutorial_general_400.png)

6. No **Picturepark configuração** secção, clique em **configurar Picturepark** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Picturepark como administrador.

8. Na barra de ferramentas na parte superior, clique em **ferramentas administrativas**e, em seguida, clique em **consola de gestão**.
   
    ![Consola de gestão](./media/active-directory-saas-picturepark-tutorial/ic795062.png "consola de gestão")

9. Clique em **autenticação**e, em seguida, clique em **fornecedores de identidade**.
   
    ![Autenticação](./media/active-directory-saas-picturepark-tutorial/ic795063.png "autenticação")

10. No **configuração do fornecedor de identidade** secção, execute os seguintes passos:
   
    ![Configuração do fornecedor de identidade](./media/active-directory-saas-picturepark-tutorial/ic795064.png "configuração do fornecedor de identidade")
   
    a. Clique em **Adicionar**.
  
    b. Escreva um nome para a sua configuração.
   
    c. Selecione **configurado como predefinido**.
   
    d. No **URI de Issuer** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
   
    e. No **impressão de botão de emissor fidedigno** caixa de texto, cole o valor de **Thumbprint** que copiou do **certificado de assinatura de SAML** secção. 

11. Clique em **JoinDefaultUsersGroup**.

12. Para definir o **Emailaddress** atributo no **afirmação** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` e clique em **guardar**.

      ![Configuração](./media/active-directory-saas-picturepark-tutorial/ic795065.png "configuração")

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-picturepark-test-user"></a>Criar um utilizador de teste Picturepark

Para permitir que os utilizadores do Azure AD sessão Picturepark, têm de ser aprovisionados para Picturepark. No caso de Picturepark, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Picturepark** inquilino.

2. Na barra de ferramentas na parte superior, clique em **ferramentas administrativas**e, em seguida, clique em **utilizadores**.
   
    ![Os utilizadores](./media/active-directory-saas-picturepark-tutorial/ic795067.png "utilizadores")

3. No **descrição geral de utilizadores** separador, clique em **novo**.
   
    ![Gestão de utilizadores](./media/active-directory-saas-picturepark-tutorial/ic795068.png "gestão de utilizadores")

4. No **criar utilizador** caixa de diálogo, execute os seguintes passos de um utilizador válido do Azure Active Directory que pretende aprovisionar:
   
    ![Criar utilizador](./media/active-directory-saas-picturepark-tutorial/ic795069.png "criar utilizador")
   
    a. No **endereço de correio eletrónico** caixa de texto, tipo de **endereço de correio eletrónico** do utilizador  **BrittaSimon@contoso.com** .  
   
    b. No **palavra-passe** e **Confirmar palavra-passe** caixas de texto, tipo de **palavra-passe** de BrittaSimon. 
   
    c. No **nome próprio** caixa de texto, tipo de **nome próprio** do utilizador **Britta**. 
   
    d. No **Apelido** caixa de texto, tipo de **Apelido** do utilizador **Simon**.
   
    e. No **empresa** caixa de texto, tipo de **nome da empresa** do utilizador. 
   
    f. No **país** caixa de texto, selecione o **país** do utilizador.
  
    g. No **ZIP** caixa de texto, tipo de **código postal** da cidade.
   
    h. No **Cidade** caixa de texto, tipo de **nome Cidade** do utilizador.

    posso. Selecione um **idioma**.
   
    j. Clique em **Criar**.

>[!NOTE]
>Pode utilizar quaisquer outras Picturepark utilizador conta criação ferramentas ou APIs fornecidas pelo Picturepark aprovisionar contas de utilizador do Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Picturepark.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Picturepark, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Picturepark**.

    ![Configurar o início de sessão único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Picturepark no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Picturepark. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_203.png

