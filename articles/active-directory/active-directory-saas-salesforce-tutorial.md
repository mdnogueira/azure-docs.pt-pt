---
title: "Tutorial: Integração do Azure Active Directory com o Salesforce | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e da Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 9aecd7310ad44eb585c1326a3c705ed822735b9a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Tutorial: Integração do Azure Active Directory com o Salesforce

Neste tutorial, irá aprender a integrar o Salesforce com o Azure Active Directory (Azure AD).

Integrar o Salesforce com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Salesforce.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada Salesforce (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Salesforce, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Salesforce-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Salesforce a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-salesforce-from-the-gallery"></a>Adicionar Salesforce a partir da Galeria
Para configurar a integração do Salesforce com o Azure AD, terá de adicionar Salesforce a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Salesforce na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Salesforce**, selecione **Salesforce** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Salesforce na lista de resultados](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com o Salesforce com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Salesforce é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Salesforce tem de ser estabelecida.

No Salesforce, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o Salesforce, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Salesforce](#create-a-salesforce-test-user)**  - para ter um homólogo de Britta Simon no Salesforce que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Salesforce.

**Para configurar o Azure AD-início de sessão único com o Salesforce, execute os seguintes passos:**

1. No portal do Azure, no **Salesforce** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. No **domínio Salesforce e URLs** secção, execute os seguintes passos:

    ![Domínio do Salesforce e URLs único informações de início de sessão](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. No **URL de início de sessão** caixa de texto, digite o valor utilizando o padrão do seguinte:
    
    Conta de empresa:`https://<subdomain>.my.salesforce.com`

    Conta de programador:`https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. No **identificador** caixa de texto, digite o valor utilizando o padrão do seguinte:
    
    Conta de empresa:`https://<subdomain>.my.salesforce.com`

    Conta de programador:`https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte [equipa de suporte de cliente do Salesforce](https://help.salesforce.com/support) para obter estes valores.

4. No **certificado de assinatura de SAML** secção, clique em **certificado** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. No **Salesforce configuração** secção, clique em **configurar Salesforce** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração do Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Abra um novo separador no seu browser e inicie sessão na sua conta de administrador do Salesforce.

8. Clique em de **configuração** em **ícone definições** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/configure1.png)

9. Desloque para baixo até o **definições** no painel de navegação, clique em **identidade** para expandir a secção relacionada. Em seguida, clique em **as definições de início de sessão único**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

10. No **as definições de início de sessão único** página, clique em de **editar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Se não for possível ativar o início de sessão único definições para a sua conta do Salesforce, poderá ter de contactar [equipa de suporte de cliente do Salesforce](https://help.salesforce.com/support). 

11. Selecione **SAML ativada**e, em seguida, clique em **guardar**.

      ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
12. Para configurar as SAML único início de sessão definições, clique em **novo**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

13. No **SAML único início de sessão no definição editar** página, efetuar as seguintes configurações:

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Para o **nome** campo, escreva um nome amigável para esta configuração. Fornecer um valor para **nome** preencher automaticamente o **nome API** caixa de texto.

    b. No **emissor** campo, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.

    c. No **caixa de texto do Id de entidade**, escreva o seu nome de domínio do Salesforce utilizando o padrão do seguinte:
      
      * Conta de empresa:`https://<subdomain>.my.salesforce.com`
      * Conta de programador:`https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Para carregar o **certificado do fornecedor de identidade**, clique em **Escolher ficheiro** para procurar e selecionar o ficheiro de certificado, o que transferiu a partir do portal do Azure.

    e. Como **tipo de identidade de SAML**, escolha uma das seguintes opções:
    
      * Selecione **asserção contém o nome de utilizador do utilizador Salesforce**, se está a ser transferida Salesforce Username do utilizador na asserção SAML

      * Selecione **asserção contém o ID de Federação do objeto User**, se o ID de Federação do objeto de utilizador está a ser transmitido asserção SAML

      * Selecione **asserção contém o ID de utilização do objeto User**, se o ID de utilizador do objeto de utilizador está a ser transmitido na asserção SAML

    f. Para **SAML identidade localização**, selecione **é de identidade no elemento NameIdentifier da declaração de assunto**.

    g. Para **fornecedor iniciada pedido vínculo de serviço**, selecione **redirecionamento de HTTP**.

    h. No **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL Single Sign-On serviço**, que copiou do portal do Azure
    
    posso. Por fim, clique em **guardar** para aplicar a suas SAML único início de sessão definições.

14. No painel de navegação esquerdo do Salesforce, clique em **definições da empresa** para expandir a secção relacionada e, em seguida, clique em **meu domínio**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

15. Desloque para baixo até o **configuração da autenticação** secção e clique em de **editar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

16. No **configuração da autenticação** secção, verifique o **página de início de sessão** como **autenticação Servie** da sua configuração de SAML SSO e, em seguida, clique em  **Guardar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se mais do que um serviço de autenticação está selecionado, os utilizadores recebem um pedido para selecionar o serviço de autenticação, como para iniciar sessão ao iniciar início de sessão único para o seu ambiente do Salesforce. Se não quiser que acontecer, em seguida, deve **deixe desmarcada a todos os outros serviços de autenticação**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-salesforce-test-user"></a>Criar um utilizador de teste do Salesforce

Nesta secção, é criado um utilizador chamado Britta Simon no Salesforce. Salesforce suporta o aprovisionamento de just-in-time, que está ativada por predefinição.
Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe no Salesforce, uma nova é criada quando tentar aceder à Salesforce.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso Salesforce.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon Salesforce, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Salesforce**.

    ![A ligação do Salesforce na lista de aplicações](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do Salesforce no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação Salesforce.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



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

