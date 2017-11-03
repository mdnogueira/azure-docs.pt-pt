---
title: "Tutorial: Integração do Azure Active Directory com LinkedIn elevar | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e efetuar a elevação LinkedIn."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 5336543e06d60be555722a615568b12048c2aa2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Tutorial: Integração do Azure Active Directory com LinkedIn elevar

Neste tutorial, irá aprender a integrar o LinkedIn elevar com o Azure Active Directory (Azure AD).

Integrar o LinkedIn elevar com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao efetuar a elevação LinkedIn
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao efetuar a elevação LinkedIn (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal de gestão do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LinkedIn elevar, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um LinkedIn elevar início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de elevar o LinkedIn da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-linkedin-elevate-from-the-gallery"></a>A adição de elevar o LinkedIn da galeria do
Para configurar a integração do LinkedIn elevar com o Azure AD, tem de adicionar elevar o LinkedIn da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar a efetuar a elevação LinkedIn da galeria do, execute os seguintes passos:**

1. No  **[Azure Management Portal](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **LinkedIn elevar**. No painel de resultados, clique em **LinkedIn elevar** para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com LinkedIn elevar com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo LinkedIn elevar é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LinkedIn elevar tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor do **Username** no elevar o LinkedIn.

Para configurar e testar o Azure AD-início de sessão único com LinkedIn elevar, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste LinkedIn elevar](#creating-a-linkedin-elevate-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal de gestão do Azure e configurar o início de sessão único na sua aplicação elevar o LinkedIn.

**Para configurar o Azure AD-início de sessão único com LinkedIn elevar, execute os seguintes passos:**

1. No portal de gestão do Azure, no **LinkedIn elevar** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, como **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedin_01.png)

3. Numa janela do browser web diferente, início de sessão no seu inquilino LinkedIn elevar como administrador.

4. No **Centro de contas**, clique em **definições globais** em **definições**. Além disso, selecione **Elevate - elevar o teste do AAD** na lista pendente.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_01.png)

5. Clique em **ou clique aqui para carregar e copiar campos individuais a partir de formulário** e copie **Id de entidade** e **Url de acesso de consumidor da asserção (ACS)**

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_03.png)

6. No Portal do Azure, em **LinkedIn elevar o domínio e os URLs**, execute os passos seguintes se quiser configurar o SSO no **IdP iniciada** modo

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_01.png)

    a. No **identificador** caixa de texto, introduza o **ID de entidade** copiados a partir do LinkedIn Portal 

    b. No **URL de resposta** caixa de texto, introduza o **Url de acesso de consumidor da asserção (ACS)** copiados a partir do LinkedIn Portal

7. Se quiser configurar o SSO no **SP iniciada**, em seguida, clique em opção de definição de mostrar URL avançado na secção de configuração e configurar o início de sessão no URL com o padrão do seguinte:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 
    
    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_02.png) 
    
8. A aplicação efetue a elevação LinkedIn espera as asserções de SAML num formato específico, que necessita para adicionar mapeamentos de atributos personalizado à sua configuração de atributos token SAML. A seguinte captura de ecrã mostra um exemplo para este. O valor predefinido de **identificador de utilizador** é **user.userprincipalname** mas LinkedIn elevar espera este deve ser mapeado com o endereço de correio eletrónico do utilizador. Para que pode utilizar **user.mail** atributo da lista ou utilize o valor do atributo adequado com base na configuração da sua organização. 

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/updateusermail.png)

9. No **atributos de utilizador** secção, clique em **ver e editar todos os outros atributos de utilizador** e definir os atributos. Tem de adicionar outra afirmação com o nome **departamento** e o valor tem de ser mapeados para **user.department**.

    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | Departamento| User.Department |

      ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/userattribute.png)

      a. Clique em adicionar atributo para abrir a página de detalhes de atributo adicionar o atributo de departamento, conforme mostrado abaixo-

      ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/adduserattribute.png)

      b. Clique em **Ok** para guardar o atributo.

      c. Alterar o nome do atributo **emailaddress** para **e-mail**.


10. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_certificate.png) 

11. Clique em **Guardar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_400.png)

12. Aceda a **definições de administrador do LinkedIn** secção. Carregue o ficheiro XML que apenas transferiu a partir do portal do Azure, clicando na opção de ficheiro XML de carregar.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_metadata_03.png)

13. Clique em **no** para permitir SSO. Estado SSO deixará de **não ligado** para **ligado**

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal de gestão do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**. 

### <a name="creating-a-linkedin-elevate-test-user"></a>Criar um utilizador de teste LinkedIn elevar

Aplicação elevar ligado suporta apenas no tempo de aprovisionamento de utilizador e de utilizadores de autenticação serão criados na aplicação automaticamente. No administrador de página de definições no LinkedIn elevar portal Inverte o comutador **automaticamente atribuir licenças** como ativa para permitir apenas no tempo de aprovisionamento e esta serão também atribuir uma licença ao utilizador.

   ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo o respetivo acesso para efetuar a elevação LinkedIn.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon LinkedIn elevação, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **LinkedIn elevar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_0001.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico LinkedIn elevar no painel de acesso, deve obter a página de início de sessão do Azure e no após o início de sessão com êxito, pode ser obtido na sua aplicação elevar o LinkedIn.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutorial: Configurar LinkedIn elevar para o utilizador automáticas de aprovisionamento com o Azure Active Directory](active-directory-saas-linkedinelevate-provisioning-tutorial.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_203.png
