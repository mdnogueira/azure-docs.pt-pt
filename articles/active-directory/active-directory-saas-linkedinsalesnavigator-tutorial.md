---
title: "Tutorial: Integração do Azure Active Directory com LinkedInSalesNavigator | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e LinkedInSalesNavigator."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.openlocfilehash: ef26a16e79d9c9b0654634960b57dc59827b2c24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Tutorial: Integração do Azure Active Directory com LinkedIn navegador de vendas

Neste tutorial, irá aprender a integrar o navegador de vendas LinkedIn com o Azure Active Directory (Azure AD).

Integrar o navegador de vendas LinkedIn com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao navegador de vendas LinkedIn
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o navegador de vendas LinkedIn (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, procurar [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LinkedIn navegador de vendas, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um navegador de vendas LinkedIn início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Evite utilizar o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de LinkedIn vendas navegador de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>A adição de LinkedIn vendas navegador de galeria
Para configurar a integração do navegador de vendas LinkedIn com o Azure AD, tem de adicionar navegador de vendas LinkedIn da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o navegador de vendas LinkedIn da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **navegador de vendas LinkedIn**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

5. No painel de resultados, selecione **navegador de vendas LinkedIn**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com LinkedIn navegador de vendas baseia-se um utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no navegador de vendas LinkedIn é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no navegador de vendas LinkedIn tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no navegador de vendas LinkedIn.

Para configurar e testar o Azure AD-início de sessão único com LinkedIn navegador de vendas, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do navegador de vendas LinkedIn](#creating-a-linkedin-sales-navigator-test-user)**  - para ter um homólogo de Britta Simon no navegador de vendas LinkedIn que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação LinkedIn vendas navegador.

**Para configurar o Azure AD-início de sessão único com LinkedIn navegador de vendas, execute os seguintes passos:**

1. No portal do Azure, no **navegador de vendas LinkedIn** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, em **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

3. Numa janela do browser web diferente, início de sessão no seu **navegador de vendas LinkedIn** Web site como um administrador.

4. No **Centro de contas**, clique em **definições globais** em **definições**. Além disso, selecione **navegador de vendas** na lista pendente.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

5. Clique em **ou clique aqui para carregar e copiar campos individuais a partir de formulário** e copie **Id de entidade** e **Url de acesso de consumidor da asserção (ACS)**.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

6. No portal do Azure, em **LinkedIn vendas navegador de domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. No **identificador** caixa de texto, introduza o **ID de entidade** copiados a partir do LinkedIn Portal 

    b. No **URL de resposta** caixa de texto, introduza o **Url de acesso de consumidor da asserção (ACS)** copiados a partir do LinkedIn Portal

7. Verifique **Mostrar avançadas definições de URL**, se pretender configurar a aplicação no **SP** iniciada modo.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    No **URL de início de sessão** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

8. O **navegador de vendas LinkedIn** aplicação espera as asserções de SAML num formato específico, que necessita para adicionar mapeamentos de atributos personalizado à sua configuração de atributos token SAML. A seguinte captura de ecrã mostra um exemplo. O valor predefinido de **identificador de utilizador** é **user.userprincipalname** mas navegador de vendas LinkedIn espera que deve ser mapeado com o endereço de correio eletrónico do utilizador. Pode utilizar **user.mail** atributo da lista ou utilize o valor do atributo adequado com base na configuração da sua organização. 

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/updateusermail.png)
    
9. No **atributos de utilizador** secção, clique em **ver e editar todos os outros atributos de utilizador** e definir os atributos. O utilizador tem de adicionar quatro afirmações com o nome **e-mail**, **departamento**, **firstname**, e **lastname** e o valor deve ser mapeado com **user.mail**, **user.department**, **user.givenname**, e **user.surname** respetivamente

    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | Correio eletrónico| User.Mail |
    | Departamento| User.Department |
    | nome próprio| User.givenName |
    | Apelido| User.Surname |
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Clique em **adicionar atributo** para abrir a caixa de diálogo do atributo.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**

10. Execute os seguintes passos no **nome** atributo -

    a. Clique no atributo para abrir o **Editar atributo** janela.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/url_update.png)

    b. Eliminar o valor do URL do **espaço de nomes**.
    
    c. Clique em **Ok** para guardar a definição.

11. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

12. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_400.png)

13. Aceda a **definições de administrador do LinkedIn** secção. Clique em **ficheiro XML de carregar** carregar o ficheiro XML de metadados que transferiu do portal do Azure.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

14. Clique em **no** para permitir SSO. Estado SSO é alterado de **não ligado** para **ligado**

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>Criar um utilizador de teste do LinkedIn navegador de vendas

Aplicação de navegador de vendas ligado suporta apenas no tempo (JIT) de aprovisionamento de utilizador e de utilizadores de autenticação são criados automaticamente na aplicação. Ativar **automaticamente atribuir licenças** para atribuir uma licença ao utilizador.
   
   ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para o navegador de vendas LinkedIn.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a LinkedIn navegador de vendas, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **navegador de vendas LinkedIn**.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do navegador de vendas LinkedIn no painel de acesso, deve ser redirecionado para a página organizacional onde tem de fornecer detalhes da sua conta LinkedIn pessoais. -Liga a sua conta pessoal com a sua conta de negócio LinkedIn. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_203.png

