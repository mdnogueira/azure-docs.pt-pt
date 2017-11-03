---
title: "Tutorial: Integração do Azure Active Directory com LockPath Keylight | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e LockPath Keylight."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: e64a966f24411818abc4cc4ab29a428b5577d012
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Tutorial: Integração do Azure Active Directory com LockPath Keylight

Neste tutorial, irá aprender a integrar LockPath Keylight com o Azure Active Directory (Azure AD).

Integrar LockPath Keylight com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao LockPath Keylight
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para LockPath Keylight (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LockPath Keylight, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um LockPath Keylight início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar LockPath Keylight na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Adicionar LockPath Keylight na galeria do
Para configurar a integração de LockPath Keylight com o Azure AD, tem de adicionar LockPath Keylight na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar LockPath Keylight a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **LockPath Keylight**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_search.png)

5. No painel de resultados, selecione **LockPath Keylight**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com LockPath Keylight com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no LockPath Keylight é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LockPath Keylight tem de ser estabelecida.

No LockPath Keylight, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com LockPath Keylight, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste LockPath Keylight](#creating-a-lockpath-keylight-test-user)**  - para ter um homólogo de Britta Simon LockPath Keylight que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação LockPath Keylight.

**Para configurar o Azure AD-início de sessão único com LockPath Keylight, execute os seguintes passos:**

1. No portal do Azure, no **LockPath Keylight** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_samlbase.png)

3. No **LockPath Keylight domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<company name>.keylightgrc.com/`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<company name>.keylightgrc.com`

    c. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [equipa de suporte de cliente de Keylight LockPath](https://www.lockpath.com/contact/) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Raw)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/tutorial_general_400.png)
    
6. No **LockPath Keylight configuração** secção, clique em **configurar Keylight de LockPath** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_configure.png) 

7. Para ativar a SSO em LockPath Keylight, execute os seguintes passos:
   
    a. Início de sessão à sua conta LockPath Keylight como administrador.
    
    b. No menu na parte superior, clique em **pessoa**e selecione **Keylight configuração**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. Na vista de árvore à esquerda, clique em **SAML**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. No **SAML definições** caixa de diálogo, clique em **editar**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/404.png) 

8. No **editar definições de SAML** diálogo página, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    a. Definir **autenticação SAML** para **Active Directory**.

    b. Colar o **único início de sessão no URL do serviço SAML** valor que copiou do portal do Azure para o **URL de início de sessão do fornecedor de identidade** caixa de texto.

    c. Colar o **único URL de serviço Sign-Out** valor que copiou do portal do Azure para o **URL de fim de sessão do fornecedor de identidade** caixa de texto.

    d. Clique em **Escolher ficheiro** para selecionar o certificado de LockPath Keylight transferido e, em seguida, clique em **abra** para carregar o certificado.

    e. Definir **localização de Id de utilizador de SAML** para **NameIdentifier elemento da declaração de assunto**.
    
    f. Fornecer o **fornecedor de serviços de Keylight** através do padrão de seguinte: **https://&lt;CompanyName&gt;. keylightgrc.com**.
    
    g. Definir **utilizadores de aprovisionamento automático** para **Active Directory**.

    h. Definir **tipo de conta de aprovisionamento automático** para **utilizador completo**.

    posso. Definir **função de segurança de aprovisionamento automático**, selecione **utilizador padrão com SAML**.
    
    j. Definir **configuração de segurança de aprovisionamento automático**, selecione **configuração de utilizador padrão**.
     
    k. No **atributo de correio eletrónico** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. No **nome próprio atributo** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. No **atributo de nome do último** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Criar um utilizador de teste LockPath Keylight

Nesta secção, vai criar um utilizador chamado Britta Simon LockPath Keylight. LockPath Keylight suporta o aprovisionamento de just-in-time, que está ativada por predefinição.

Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado quando aceder LockPath Keylight se o utilizador ainda não existe. 

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar o [equipa de suporte de cliente de Keylight LockPath](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para LockPath Keylight.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a LockPath Keylight, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **LockPath Keylight**.

    ![Configurar o início de sessão único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico LockPath Keylight no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de LockPath Keylight. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png

