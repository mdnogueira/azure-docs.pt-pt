---
title: "Tutorial: Integração do Azure Active Directory com Wdesk | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Wdesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 37660b80cfb01d6a3105aea5ce248f1e03c46695
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Tutorial: Integração do Azure Active Directory com Wdesk

Neste tutorial, irá aprender a integrar Wdesk com o Azure Active Directory (Azure AD).

Integrar Wdesk com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Wdesk
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Wdesk (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo. [O que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Wdesk, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Wdesk início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Wdesk a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-wdesk-from-the-gallery"></a>Adicionar Wdesk a partir da Galeria
Para configurar a integração de Wdesk com o Azure AD, terá de adicionar Wdesk a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Wdesk a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Wdesk**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. No painel de resultados, selecione **Wdesk**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Wdesk com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Wdesk é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Wdesk tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Wdesk.

Para configurar e testar o Azure AD-início de sessão único com Wdesk, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Wdesk](#creating-a-wdesk-test-user)**  - para ter um homólogo de Britta Simon Wdesk que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Wdesk.

**Para configurar o Azure AD-início de sessão único com Wdesk, execute os seguintes passos:**

1. No portal do Azure, no **Wdesk** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. No **Wdesk domínio e os URLs** secção, se pretender configurar a aplicação no **IDP** modo iniciado execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

4. Verifique **Mostrar avançadas definições de URL**. Se pretender configurar a aplicação no **SP** iniciada modo, executar o passo seguinte:

    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Pode obter estes valores do portal de WDesk quando configurar o SSO. 
  
5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. Numa janela do browser web diferente, no início de sessão Wdesk como um administrador de segurança.

8. Na parte inferior esquerda, clique em **Admin** e escolha **administrador da conta**:
 
     ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. No Wdesk administrador, navegue para **segurança**, em seguida, **SAML** > **SAML definições**:

    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. Em **definições gerais**, verifique o **ativar SAML início de sessão único**:

    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. Em **os detalhes do fornecedor de serviço**, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copiar o **URL de início de sessão** e cole-a no **Url de início de sessão** caixa de texto no portal do Azure.
   
      b. Copiar o **Url de metadados** e cole-a no **identificador** caixa de texto no portal do Azure.
       
      c. Copiar o **url de consumidor** e cole-a no **Url de resposta** caixa de texto no portal do Azure.
   
      d. Clique em **guardar** no portal do Azure para guardar as alterações.      

12. Clique em **configurar definições de IdP** para abrir **editar definições de IdP** caixa de diálogo. Clique em **Escolher ficheiro** para localizar o **Metadata.xml** ficheiro guardado no portal do Azure, em seguida, carregue-o.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. Clique em **guardar alterações**.

    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-wdesk-test-user"></a>Criar um utilizador de teste Wdesk

Para permitir que os utilizadores do Azure AD iniciem sessão nos Wdesk, têm de ser aprovisionados para Wdesk. No Wdesk, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Wdesk como um administrador de segurança.
2. Navegue para **Admin** > **conta Administrador**.

     ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Clique em **membros** em **pessoas**.

4. Agora, clique em **Add Member** para abrir **Add Member** caixa de diálogo. 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. No **utilizador** texto, introduza o nome de utilizador do utilizador, como  **brittasimon@contoso.com**  e clique em **continuar** botão.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  Introduza os detalhes conforme mostrado abaixo:
  
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. No **correio electrónico** texto, introduza o e-mail do utilizador, como  **brittasimon@contoso.com** .

    b. No **nome próprio** texto, introduza o nome de utilizador como **Britta**.

    c. No **Apelido** texto, introduza o apelido do utilizador, como **Simon**.

7. Clique em **guardar membro** botão.  

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Wdesk.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Wdesk, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Wdesk**.

    ![Configurar o início de sessão único](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Wdesk no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Wdesk.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png

