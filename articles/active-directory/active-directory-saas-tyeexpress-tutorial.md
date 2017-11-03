---
title: "Tutorial: Integração do Azure Active Directory com o T & Express I | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e T & I Express."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.openlocfilehash: 869e5284c71904fcc817ceee0f39d94fab1bc6f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Tutorial: Integração do Azure Active Directory com o T & I Express

Neste tutorial, irá aprender a integrar T & I Express com o Azure Active Directory (Azure AD).

Integrar o T & I Express com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao T & I Express
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para & I Express (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal de gestão do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o T & I Express, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Redireccionamen & I Express início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar e & I Express na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-te-express-from-the-gallery"></a>Adicionar e & I Express na galeria do
Para configurar a integração da T & I Express com o Azure AD, tem de adicionar e & I Express na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar e & I Express na galeria do, execute os seguintes passos:**

1. No  **[Azure Management Portal](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **T & I Express**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_search.png)

5. No painel de resultados, selecione **T & I Express**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o T & I Express com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo T & I Express for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no T & I Express tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** T & I Express.

Para configurar e testar o Azure AD-início de sessão único com o T & I Express, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste T & I Express](#creating-a-te-express-test-user)**  - para ter um homólogo de Britta Simon T & I Express que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal de gestão do Azure e configurar o início de sessão único na sua aplicação T & I Express.

**Para configurar o Azure AD-início de sessão único com o T & I Express, execute os seguintes passos:**

1. No portal de gestão do Azure, no **T & I Express** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, como **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

3. No **T & I Express domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. No **identificador** caixa de texto, digite o valor como:`https://<domain>.tyeexpress.com`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Tenha em atenção que estas não são os valores reais. Tem de atualizar estes valores com o identificador e o URL de resposta real. Aqui sugerimos que utilize o valor de cadeia exclusivo no identificador de. Contacte [equipa de suporte de T & I Express](http://www.tyeexpress.com/contacto.aspx) para obter estes valores.

5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_400.png)

8. Para configurar o início de sessão único em **T & i rápida** lado, o início de sessão para o T & zar express aplicação sem início de sessão único SAML utilizando credenciais de administrador.

9. Sob o **Admin** separador, clique em **domínio SAML** para abrir a página de definições de SAML.

    ![Configurar o início de sessão único](./media/active-directory-saas-tyeexpress-tutorial/tye-SAML.png)

10. Selecione o **Activar(Activate)** opção de **não** para **SI(Yes)**. No **metadados do fornecedor de identidade** caixa de texto, cole os metadados XML que tiver donwloaded a partir do portal do Azure.

    ![Configurar o início de sessão único](./media/active-directory-saas-tyeexpress-tutorial/tyeAdmin.png)

11. Clique em de **Guardar(Save)** botão para guardar as definições. 


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal de gestão do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tyeexpress-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-te-express-test-user"></a>Criar um utilizador de teste T & I Express

Para permitir que os utilizadores do Azure AD sessão T & I Express, têm de ser aprovisionados para T & I Express.  
Em caso de T & I Express, o aprovisionamento é uma tarefa manual.

**Aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa T & Express I como administrador.

2. Na etiqueta do administrador, clique em utilizadores para abrir a página principal de utilizadores.

    ![Adicionar empregado](./media/active-directory-saas-tyeexpress-tutorial/tye-adminusers.png)

3. Na página inicial, clique em  **+**  para adicionar os utilizadores.

    ![Adicionar empregado](./media/active-directory-saas-tyeexpress-tutorial/tye-usershome.png)

4. Introduza todos os detalhes obrigatórios, tal como pedido no formulário e clique no botão de guardar para guardar os detalhes.

    ![Adicionar empregado](./media/active-directory-saas-tyeexpress-tutorial/tye-usersadd.png)

    ![Adicionar empregado](./media/active-directory-saas-tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo o respetivo acesso para & I Express.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon para & I Express, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **T & I Express**.

    ![Configurar o início de sessão único](./media/active-directory-saas-tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico T & I Express no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação T & I Express.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tyeexpress-tutorial/tutorial_general_203.png

