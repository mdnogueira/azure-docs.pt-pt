---
title: "Tutorial: Integração do Azure Active Directory com início de sessão do Adobe | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Adobe início de sessão."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: b413772de1af1fbb128d29b81e5831cfd6a39ab4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Integração do Azure Active Directory com início de sessão do Adobe

Neste tutorial, irá aprender a integrar o Adobe início de sessão no Azure Active Directory (Azure AD).

Integrar o Adobe início de sessão com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao início de sessão do Adobe
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para início de sessão do Adobe (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Adobe início de sessão, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma sessão de Adobe-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar início de sessão do Adobe na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-adobe-sign-from-the-gallery"></a>Adicionar início de sessão do Adobe na galeria do
Para configurar a integração do Adobe início de sessão com o Azure AD, tem de adicionar início de sessão do Adobe na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Adobe início de sessão a partir da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Adobe sessão**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. No painel de resultados, selecione **Adobe sessão**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único, com início de sessão do Adobe com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Adobe início de sessão é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Adobe início de sessão tem de ser estabelecida.

No início de sessão do Adobe, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Adobe início de sessão, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de início de sessão do Adobe](#creating-an-adobe-sign-test-user)**  - para ter um homólogo de Britta Simon no Adobe início de sessão que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Adobe início de sessão.

**Para configurar o Azure AD-início de sessão único com Adobe início de sessão, execute os seguintes passos:**

1. No portal do Azure, no **Adobe sessão** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. No **URLs e de domínio de início de sessão do Adobe** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.echosign.com/`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.echosign.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de início de sessão do Adobe](https://helpx.adobe.com/in/contact/support.html) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_400.png)

6. No **configuração de sessão do Adobe** secção, clique em **configurar início de sessão do Adobe** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_configure.png) 


7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Adobe sessão como administrador.

8. No menu na parte superior, clique em **conta**e, em seguida, no painel de navegação no lado esquerdo, clique em **SAML definições** em **as definições da conta**.
   
   ![Conta](./media/active-directory-saas-adobe-echosign-tutorial/ic789520.png "conta")

9. Na secção de definições de SAML, execute os seguintes passos:
   
   ![Definições de SAML](./media/active-directory-saas-adobe-echosign-tutorial/ic789521.png "SAML definições")
   
   a. Como **SAML modo**, selecione **SAML obrigatório**.
   
   b. Selecione **permitir EchoSign conta aos administradores inicie sessão com as respetivas credenciais de EchoSign**.
   
   c. Como **criação de utilizador**, selecione **adicionar automaticamente utilizadores autenticados através de SAML**.

10. Passar, efetuando os seguintes passos:

       ![Definições de SAML](./media/active-directory-saas-adobe-echosign-tutorial/ic789522.png "SAML definições")

    a. Colar **ID de entidade de SAML**, que copiou do portal do Azure para o **ID da entidade IdP** caixa de texto.
    
    b. Colar **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure para o **URL de início de sessão do IdP** caixa de texto.
   
    c. Colar **Sign-Out URL**, que copiou do portal do Azure para o **URL de fim de sessão do IdP** caixa de texto.

    d. Abra o transferido **Certificate(Base64)** ficheiro no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **IdP certificado** caixa de texto

    e. Clique em **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-adobe-sign-test-user"></a>Criar um utilizador de teste de início de sessão do Adobe

Para permitir que os utilizadores do Azure AD iniciem sessão Adobe início de sessão, têm de ser aprovisionados no Adobe início de sessão. No caso de início de sessão do Adobe, o aprovisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar qualquer outro início de sessão do Adobe utilizador conta criação ferramentas ou APIs fornecidas pelo início de sessão do Adobe para aprovisionar contas de utilizador do AAD. 

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Adobe sessão** site da empresa como administrador.

2. No menu na parte superior, clique em **conta**e, em seguida, no painel de navegação no lado esquerdo, clique em **utilizadores e grupos**e, em seguida, clique em **criar um novo utilizador**.
   
   ![Conta](./media/active-directory-saas-adobe-echosign-tutorial/ic789524.png "conta")
   
3. No **criar novo utilizador** secção, execute os seguintes passos:
   
   ![Criar utilizador](./media/active-directory-saas-adobe-echosign-tutorial/ic789525.png "criar utilizador")
   
   a. Tipo de **endereço de correio eletrónico**, **nome próprio**, e **Apelido** de uma conta válida do AAD, pretende aprovisionar para caixas de texto relacionadas.
   
   b. Clique em **criar utilizador**.

>[!NOTE]
>O marcador de posição de conta do Azure Active Directory recebe uma mensagem de e-mail que inclui uma ligação para confirmar a conta para ficar ativa. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso Adobe início de sessão.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon Adobe início de sessão, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Adobe sessão**.

    ![Configurar o início de sessão único](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Quando clica no mosaico de início de sessão do Adobe no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação Adobe início de sessão.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_203.png

