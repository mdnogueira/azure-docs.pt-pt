---
title: "Tutorial: Integração do Azure Active Directory com UserVoice | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e UserVoice."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: fcfda1c2ecb162fb93b70574a18bd745b72ee4db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integração do Azure Active Directory com UserVoice

Neste tutorial, irá aprender a integração do UserVoice no Azure Active Directory (Azure AD).

Integrar o UserVoice com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao UserVoice.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o UserVoice (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o UserVoice, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um UserVoice-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar UserVoice a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-uservoice-from-the-gallery"></a>Adicionar UserVoice a partir da Galeria
Para configurar a integração do UserVoice com o Azure AD, terá de adicionar UserVoice a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar UserVoice a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **UserVoice**, selecione **UserVoice** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![UserVoice na lista de resultados](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com UserVoice com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no UserVoice é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no UserVoice tem de ser estabelecida.

No UserVoice, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o UserVoice, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do UserVoice](#create-a-uservoice-test-user)**  - para ter um homólogo de Britta Simon no UserVoice que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação da UserVoice.

**Para configurar o Azure AD-início de sessão único com o UserVoice, execute os seguintes passos:**

1. No portal do Azure, no **UserVoice** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. No **UserVoice domínio e os URLs** secção, execute os seguintes passos:

    ![UserVoice domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenantname>.UserVoice.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente do UserVoice](https://www.uservoice.com/) para obter estes valores.

4. No **certificado de assinatura de SAML** secção, copie o **THUMBPRINT** valor do certificado.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-uservoice-tutorial/tutorial_general_400.png)

6. No **UserVoice configuração** secção, clique em **configurar UserVoice** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração do UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa UserVoice como administrador.

8. Na barra de ferramentas na parte superior, clique em **definições**e, em seguida, selecione **Web portal** no menu.
   
    ![Secção de definições no lado de aplicação](./media/active-directory-saas-uservoice-tutorial/ic777519.png "definições")

9. No **Web portal** separador o **autenticação de utilizador** secção, clique em **editar** para abrir o **editar autenticação de utilizador** página da caixa de diálogo.
   
    ![Portal Web do separador](./media/active-directory-saas-uservoice-tutorial/ic777520.png "Web portal")

10. No **editar autenticação de utilizador** diálogo página, execute os seguintes passos:
   
    ![Editar autenticação de utilizador](./media/active-directory-saas-uservoice-tutorial/ic777521.png "editar autenticação de utilizador")
   
    a. Clique em **Single Sign-On (SSO)**.
 
    b. Colar o **único início de sessão no URL do serviço SAML** valor que copiou do portal do Azure para o **SSO remoto início de sessão** caixa de texto.

    c. Colar o **Sign-Out URL** valor que copiou do portal do Azure para o **Sign-Out remoto SSO caixa de texto**.
 
    d. Colar o **Thumbprint** valor que copiou do portal do Azure para o **impressão digital de certificado SHA1 atual** caixa de texto.
    
    e. Clique em **guardar as definições de autenticação**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-uservoice-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-uservoice-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-uservoice-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-uservoice-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-uservoice-test-user"></a>Criar um utilizador de teste do UserVoice

Para ativar a utilizadores do Azure AD para início de sessão para o UserVoice, tem de ser aprovisionados no UserVoice. No caso do UserVoice, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para Aprovisionar uma conta de utilizador, execute os seguintes passos:
1. Inicie sessão no seu **UserVoice** inquilino.

2. Aceda a **definições**.
   
    ![Definições](./media/active-directory-saas-uservoice-tutorial/ic777811.png "definições")

3. Clique em **geral**.

4. Clique em **agentes e permissões**.
   
    ![Os agentes e permissões](./media/active-directory-saas-uservoice-tutorial/ic777812.png "agentes e permissões")

5. Clique em **adicione administradores**.
   
    ![Adicionar administradores](./media/active-directory-saas-uservoice-tutorial/ic777813.png "adicione administradores")

6. No **convidar admins** caixa de diálogo, execute os seguintes passos:
   
    ![Convidar admins](./media/active-directory-saas-uservoice-tutorial/ic777814.png "convidar admins")
   
    a. Na caixa de texto de mensagens de correio eletrónico, escreva o endereço de e-mail da conta que pretende aprovisionar e, em seguida, clique em **adicionar**.
   
    b. Clique em **convidar**.

> [!NOTE]
> Pode utilizar quaisquer outras UserVoice utilizador conta criação ferramentas ou APIs fornecidas pelo UserVoice para aprovisionar contas de utilizador do AAD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para o UserVoice.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon para o UserVoice, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **UserVoice**.

    ![A ligação do UserVoice na lista de aplicações](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do UserVoice no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação do UserVoice.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_203.png

