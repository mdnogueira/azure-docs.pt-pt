---
title: "Tutorial: Integração do Azure Active Directory com o Salesforce | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e da Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 7471a6999ac4dcd15f5162e69cb0100cc1e3b14f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Tutorial: Integração do Azure Active Directory com o Salesforce

Neste tutorial, irá aprender a integrar o Salesforce com o Azure Active Directory (Azure AD).

Integrar o Salesforce com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Salesforce
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada Salesforce (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Salesforce, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Salesforce início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Salesforce a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-salesforce-from-the-gallery"></a>Adicionar Salesforce a partir da Galeria
Para configurar a integração do Salesforce com o Azure AD, terá de adicionar Salesforce a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Salesforce na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Salesforce**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. No painel de resultados, selecione **Salesforce**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o Salesforce com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Salesforce é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Salesforce tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Salesforce.

Para configurar e testar o Azure AD-início de sessão único com o Salesforce, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Salesforce](#creating-a-salesforce-test-user)**  - para ter um homólogo de Britta Simon no Salesforce que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Salesforce.

**Para configurar o Azure AD-início de sessão único com o Salesforce, execute os seguintes passos:**

1. No portal do Azure, no **Salesforce** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. No **domínio Salesforce e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    No **URL de início de sessão** caixa de texto, digite o valor utilizando o padrão do seguinte: 
   * Conta de empresa:`https://<subdomain>.my.salesforce.com`
   * Conta de programador:`https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > Estes valores não estiverem a real. Atualize estes valores com o URL de início de sessão real. Contacte [equipa de suporte de cliente do Salesforce](https://help.salesforce.com/support) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Clique em **guardar** botão ![configurar Single Sign-On](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. No **Salesforce configuração** secção, clique em **configurar Salesforce** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.** 

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  Abra um novo separador no seu browser e inicie sessão na sua conta de administrador do Salesforce.

8.  Sob o **administrador** painel de navegação, clique em **controlos de segurança** para expandir a secção relacionada. Em seguida, clique em **as definições de início de sessão único**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  No **as definições de início de sessão único** página, clique em de **editar** botão.
    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > Se não for possível ativar o início de sessão único definições para a sua conta do Salesforce, poderá ter de contactar [equipa de suporte de cliente do Salesforce](https://help.salesforce.com/support). 

10. Selecione **SAML ativada**e, em seguida, clique em **guardar**.

      ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. Para configurar as SAML único início de sessão definições, clique em **novo**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. No **SAML único início de sessão no definição editar** página, efetuar as seguintes configurações:

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Para o **nome** campo, escreva um nome amigável para esta configuração. Fornecer um valor para **nome** preencher automaticamente o **nome API** caixa de texto.

    b. Colar **ID de entidade de SAML** valor para o **emissor** campo no Salesforce.

    c. No **caixa de texto do Id de entidade**, escreva o seu nome de domínio do Salesforce utilizando o padrão do seguinte:
      
      * Conta de empresa:`https://<subdomain>.my.salesforce.com`
      * Conta de programador:`https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Clique em **procurar** ou **Escolher ficheiro** para abrir o **Escolher ficheiro a carregar** caixa de diálogo, selecione o certificado do Salesforce e, em seguida, clique em **abrir**para carregar o certificado.

    e. Para **tipo de identidade de SAML**, selecione **asserção contém o nome de utilizador do utilizador em salesforce.com**.

    f. Para **SAML identidade localização**, selecione **é de identidade no elemento NameIdentifier da instrução do requerente**

    g. Colar **URL Single Sign-On serviço** para o **URL de início de sessão do fornecedor de identidade** campo no Salesforce.
    
    h. Para **fornecedor iniciada pedido vínculo de serviço**, selecione **redirecionamento de HTTP**.
    
    posso. Por fim, clique em **guardar** para aplicar a suas SAML único início de sessão definições.

13. No painel de navegação esquerdo do Salesforce, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. Desloque para baixo até o **configuração da autenticação** secção e clique em de **editar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. No **serviço de autenticação** secção, selecione o nome amigável da sua configuração de SAML SSO e, em seguida, clique em **guardar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se mais do que um serviço de autenticação está selecionado, os utilizadores recebem um pedido para selecionar o serviço de autenticação, como para iniciar sessão ao iniciar início de sessão único para o seu ambiente do Salesforce. Se não quiser que acontecer, em seguida, deve **deixe desmarcada a todos os outros serviços de autenticação**.
<CE>    
> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar em **Single Sign-On** separador e aceder à documentação do embedded através de  **Configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No painel de navegação esquerdo do **portal do Azure**, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-salesforce-test-user"></a>Criar um utilizador de teste do Salesforce

Nesta secção, é criado um utilizador chamado Britta Simon no Salesforce. Salesforce suporta o aprovisionamento de just-in-time, que está ativada por predefinição.
Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe no Salesforce, uma nova é criada quando tentar aceder à Salesforce.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso Salesforce.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon Salesforce, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Salesforce**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Para testar as definições de início de sessão único, abra o painel de acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), em seguida, iniciar sessão na conta de teste e, em **Salesforce**.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o aprovisionamento de utilizadores](active-directory-saas-salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png

