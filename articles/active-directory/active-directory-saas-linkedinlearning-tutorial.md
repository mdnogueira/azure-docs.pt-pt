---
title: "Tutorial: Integração do Azure Active Directory com LinkedIn Learning | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e LinkedIn Learning."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: jeedes
ms.openlocfilehash: 6ad28cb3adaa63ddc3d3769a650d26ca6a7e2695
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Tutorial: Integração do Azure Active Directory com LinkedIn Learning

Neste tutorial, irá aprender a integrar LinkedIn Learning no Azure Active Directory (Azure AD).

Integrar o LinkedIn Learning com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao LinkedIn Learning
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para LinkedIn Learning (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LinkedIn Learning, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um LinkedIn Learning início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de aprendizagem do LinkedIn da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-linkedin-learning-from-the-gallery"></a>A adição de aprendizagem do LinkedIn da galeria do
Para configurar a integração do LinkedIn Learning com o Azure AD, tem de adicionar LinkedIn Learning da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar LinkedIn Learning a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **LinkedIn Learning**. No painel de resultados, clique em **LinkedIn Learning** para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Learning LinkedIn com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no LinkedIn Learning é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LinkedIn Learning tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no LinkedIn Learning.

Para configurar e testar o Azure AD-início de sessão único com o LinkedIn Learning, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de aprendizagem do LinkedIn](#creating-a-linkedin-learning-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de aprendizagem do LinkedIn.

**Para configurar o Azure AD-início de sessão único com LinkedIn Learning, execute os seguintes passos:**

1. No portal do Azure, no **LinkedIn Learning** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedin_01.png)

3. Numa janela do browser web diferente, início de sessão no seu inquilino de aprendizagem do LinkedIn como administrador.

4. No **Centro de contas**, clique em **definições globais** em **definições**. Além disso, selecione **Learning - predefinição** na lista pendente.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

5. Clique em **ou clique aqui para carregar e copiar campos individuais a partir de formulário** e copie **Id de entidade** e **Url de acesso de consumidor da asserção (ACS)**

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

6. No portal do Azure, em **LinkedIn Learning domínio e os URLs**, execute os passos seguintes se quiser configurar o SSO no **IdP iniciada** modo

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. No **identificador** caixa de texto, introduza o **ID de entidade** copiados a partir do LinkedIn Portal 

    b. No **URL de resposta** caixa de texto, introduza o **Url de acesso de consumidor da asserção (ACS)** copiados a partir do LinkedIn Portal

7. Se quiser configurar o SSO no **SP iniciada**, em seguida, clique em opção de definição de mostrar URL avançado na secção de configuração e configurar o URL de início de sessão com o padrão do seguinte:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
8. A aplicação de aprendizagem do LinkedIn espera as asserções de SAML num formato específico, que necessita para adicionar mapeamentos de atributos personalizado à sua configuração de atributos token SAML. A seguinte captura de ecrã mostra um exemplo para este. O valor predefinido de **identificador de utilizador** é **user.userprincipalname** mas LinkedIn Learning espera este deve ser mapeado com o endereço de correio eletrónico do utilizador. Para que pode utilizar **user.mail** atributo da lista ou utilize o valor do atributo adequado com base na configuração da sua organização. 

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/updateusermail.png)
    
9. No **atributos de utilizador** secção, clique em **ver e editar todos os outros atributos de utilizador** e definir os atributos. O utilizador tem de adicionar quatro afirmações com o nome **e-mail**, **departamento**, **firstname**, e **lastname** e o valor deve ser mapeado com **user.mail**, **user.department**, **user.givenname**, e **user.surname** respetivamente

    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | Correio eletrónico| User.Mail |    
    | Departamento| User.Department |
    | nome próprio| User.givenName |
    | Apelido| User.Surname |
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/userattribute.png)
    
    a. Clique em **adicionar atributo** para abrir a caixa de diálogo do atributo.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_04.png)

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_05.png)
    
    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**

10. Execute os seguintes passos no **nome** atributo -

    a. Clique no atributo para abrir o **Editar atributo** janela.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinLearning-tutorial/url_update.png)

    b. Eliminar o valor do URL do **espaço de nomes**.
    
    c. Clique em **Ok** para guardar a definição.

11. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png) 

12. Clique em **Guardar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_400.png)

13. Aceda a **definições de administrador do LinkedIn** secção. Carregue o ficheiro XML que transferiu a partir do portal do Azure, clicando a opção de ficheiro XML de carregar.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

14. Clique em **no** para permitir SSO. Estado SSO é alterado de **não ligado** para **ligado**

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**. 

### <a name="creating-a-linkedin-learning-test-user"></a>Criar um utilizador de teste de aprendizagem do LinkedIn

Suporta a aplicação de aprendizagem ligado. Apenas no aprovisionamento de utilizadores de tempo e após a autenticação os utilizadores são criados automaticamente na aplicação. No administrador de página de definições no LinkedIn Learning portal Inverte o comutador **automaticamente atribuir licenças** como ativa para permitir apenas no tempo de aprovisionamento e esta serão também atribuir uma licença ao utilizador.
   
   ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para LinkedIn Learning.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a aprendizagem LinkedIn, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **LinkedIn Learning**.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de aprendizagem do LinkedIn no painel de acesso, deve obter a página de início de sessão do Azure e no após o início de sessão com êxito, pode ser obtido na sua aplicação de aprendizagem do LinkedIn.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_203.png