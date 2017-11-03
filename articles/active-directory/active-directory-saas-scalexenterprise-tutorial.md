---
title: "Tutorial: Integração do Azure Active Directory com ScaleX empresarial | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e ScaleX Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: 0ebed0c2605862426384c0e219e52c9d626b6246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Tutorial: Integração do Azure Active Directory com ScaleX empresarial

Neste tutorial, irá aprender a integração empresarial ScaleX com o Azure Active Directory (Azure AD).

Integrar ScaleX Enterprise com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ScaleX Enterprise
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a empresa ScaleX (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo. O que é o acesso a aplicações e início de sessão único com [do Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ScaleX empresarial, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um empresa ScaleX início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar ScaleX Enterprise da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Adicionar ScaleX Enterprise da galeria do
Para configurar a integração do ScaleX Enterprise no Azure AD, tem de adicionar ScaleX Enterprise da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar ScaleX Enterprise da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **ScaleX Enterprise**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. No painel de resultados, selecione **ScaleX Enterprise**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com ScaleX empresarial com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo ScaleX empresa for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado ScaleX empresa tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** ScaleX empresa.

Para configurar e testar o Azure AD-início de sessão único com ScaleX empresarial, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)**  - para ter um homólogo de Britta Simon ScaleX empresa que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação ScaleX Enterprise.

**Para configurar o Azure AD-início de sessão único com ScaleX Enterprise, execute os seguintes passos:**

1. No portal do Azure, no **ScaleX Enterprise** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, como **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. No **ScaleX Enterprise domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. No **identificador** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://platform.rescale.com/saml2/<company id>/`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://platform.rescale.com/saml2/<company id>/acs/`

4. Verifique **Mostrar avançadas definições de URL**, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    No **URL de início de sessão** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Estes não são os valores reais. Atualize estes valores com o identificador de real, URL de resposta ou URL de início de sessão. Contacte [equipa de suporte de cliente do Enterprise ScaleX](http://info.rescale.com/contact_sales) para obter estes valores. 

5. A aplicação de ScaleX espera as asserções de SAML num formato específico, que requer a modificar os mapeamentos de atributos personalizados para a configuração de atributos token SAML. Clique em **ver e editar todos os outros atributos de utilizador** as definições de atributos de caixa de verificação para abrir o personalizado.

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Clique com o botão direito do rato no atributo **nome** e clique em eliminar.

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/delete_attribute_name.png)

    b. Clique em **emailaddress** atributo para abrir a janela Editar atributo. Altere o valor de **user.mail** para **user.userprincipalname** e clique em Ok.

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/edit_email_attribute.png)   
    
5. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_400.png)
    
7. No **ScaleX Enterprise configuração** secção, clique em **configurar Enterprise de ScaleX** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML** e **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. Para configurar o início de sessão único em **ScaleX Enterprise** lado, o início de sessão para o Web site da empresa ScaleX Enterprise como administrador.

9. Clique no menu no canto superior direito e selecione **Contoso administração**.

    > [!NOTE] 
    > Contoso é apenas um exemplo. Isto deve ser o nome da sua empresa real. 

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/Test_Admin.png) 

10. Selecione **integrações** no menu superior e selecionar **Single Sign-On**.

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/admin_sso.png) 

11. Preencha o formulário da seguinte forma:

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Selecione **"Criar qualquer utilizador que pode autenticar com o SSO".**

    b. **Fornecedor do serviço saml**: colar o valor ***urn: oasis: os nomes: tc: SAML:2.0:nameid-formato: persistente***

    c. **Nome do campo de e-mail fornecedor de identidade na resposta de ACS**: colar o valor`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **ID de entidade de EntityDescriptor do fornecedor de identidade:** colar o **ID de entidade de SAML** valor copiada a partir do portal do Azure.

    e. **URL de SingleSignOnService do fornecedor de identidade:** colar o **único início de sessão no URL do serviço SAML** do portal do Azure.

    f. **Certificado de identidade do fornecedor público X509:** abra o X509 certificado transferido a partir do Azure no bloco de notas e cole o conteúdo nesta caixa. Certifique-se de que existem que não interrompe a nenhuma linha no meio do conteúdo de certificado.
    
    g. Verifique as caixas de verificação seguintes: **ativado, encriptar NameID e AuthnRequests de início de sessão.**

    h. Clique em **definições de atualização de SSO** para guardar as definições.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Criar um utilizador de teste ScaleX Enterprise

Para permitir que os utilizadores do Azure AD iniciem sessão nos ScaleX Enterprise, estes têm de ser aprovisionados para ScaleX Enterprise. No caso de ScaleX Enterprise, o aprovisionamento é uma tarefa automática e não existem passos manuais são necessários. Qualquer utilizador que pode autenticar com êxito com credenciais SSO será aprovisionado automaticamente no lado ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso de utilizador para a empresa ScaleX.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a ScaleX Enterprise, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **ScaleX Enterprise**.

    ![Configurar o início de sessão único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Clique no mosaico ScaleX empresarial no painel de acesso, irá obter automaticamente com sessão iniciada para a aplicação de ScaleX Enterprise. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_203.png

