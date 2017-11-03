---
title: "Tutorial: Integração do Azure Active Directory com o SSO Kantega para Bitbucket | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Kantega SSO para Bitbucket."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 6656c9abf8483ee98c0cb1a16c06d078e32240f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Tutorial: Integração do Azure Active Directory com o SSO Kantega para Bitbucket

Neste tutorial, irá aprender a integrar Kantega SSO para Bitbucket com o Azure Active Directory (Azure AD).

Integrar Kantega SSO para Bitbucket com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Kantega SSO para Bitbucket
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Kantega SSO para Bitbucket (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SSO Kantega para Bitbucket, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um SSO Kantega para Bitbucket-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Kantega SSO para Bitbucket na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Adicionar Kantega SSO para Bitbucket na galeria do
Para configurar a integração do SSO Kantega para Bitbucket com o Azure AD, tem de adicionar Kantega SSO para Bitbucket na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Kantega SSO para Bitbucket a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Kantega SSO para Bitbucket**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_search.png)

5. No painel de resultados, selecione **Kantega SSO para Bitbucket**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o SSO Kantega para Bitbucket com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo em Kantega SSO para Bitbucket é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em Kantega SSO para Bitbucket tem de ser estabelecida.

Em Kantega SSO para Bitbucket, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o SSO Kantega para Bitbucket, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um SSO Kantega para o utilizador de teste de Bitbucket](#creating-a-kantega-sso-for-bitbucket-test-user)**  - para ter um homólogo de Britta Simon Kantega SSO para Bitbucket que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua SSO Kantega para aplicação Bitbucket.

**Para configurar o Azure AD-início de sessão único com o SSO Kantega para Bitbucket, execute os seguintes passos:**

1. No portal do Azure, no **Kantega SSO para Bitbucket** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_samlbase.png)

3. No **IDP** iniciada modo, no **Kantega SSO para o domínio de Bitbucket e URLs** secção executar o passo seguinte:

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url1.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. No **SP** modo iniciado, verifique **Mostrar avançadas definições de URL** e executar o passo seguinte:

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url2.png)
    
    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Estes valores são recebidos durante a configuração do plug-in de Bitbucket que é explicada mais tarde no tutorial.

5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_400.png)

7. Numa janela do browser web diferente, inicie sessão no portal de administração de Bitbucket como administrador.

8. Clique em cog e clique em de **localizar novos suplementos**.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon1.png)

9. Pesquisa **Kantega SSO para Bitbucket SAML & Kerberos** e clique em **instalar** botão para instalar o novo plug-in SAML.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon2.png)

10. Inicia a instalação de plug-in.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon31.png)

11. Assim que a instalação estiver concluída. Clique em **Fechar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon33.png)

12. Clique em **Gerir**.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon34.png)
    
13. Clique em **configurar** para configurar o plug-in de novo.    

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon35.png)

14. No **SAML** secção. Selecione **Azure Active Directory (Azure AD)** do **Adicionar fornecedor de identidade** pendente.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon4.png)

15. Selecione o nível de subscrição como **básico**.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon5.png)

16. No **as propriedades da aplicação** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon6.png)

    a. Copiar o **URI de ID de aplicação** valor e utilizá-lo como **identificador, o URL de resposta e o URL de início de sessão** no **Kantega SSO para o domínio de Bitbucket e URLs** secção no portal do Azure.

    b. Clique em **Seguinte**.

17. No **importar metadados** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon7.png)

    a. Selecione **ficheiro de metadados no meu computador**e o ficheiro de metadados do carregamento, que transferiu a partir do portal do Azure.

    b. Clique em **Seguinte**.

18. No **localização nome e SSO** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon8.png)

    a. Adicione o nome do fornecedor de identidade no **nome do fornecedor de identidade** caixa de texto (por exemplo, do Azure AD).

    b. Clique em **Seguinte**.

19. Verificar o certificado de assinatura e clique em **seguinte**.  

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon9.png)

20. No **contas de utilizador Bitbucket** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon10.png)

    a. Selecione **criar utilizadores no diretório interno do Bitbucket se for necessário** e introduza o nome adequado do grupo de utilizadores (pode ser não vários. de grupos separados por vírgulas).

    b. Clique em **Seguinte**.

21. Clique em **Concluir**.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon11.png)

22. No **conhecido domínios para o Azure AD** secção, execute os seguintes passos: 

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/addon12.png)

    a. Selecione **conhecido domínios** do painel esquerdo da página.

    b. Introduza o nome de domínio no **conhecido domínios** caixa de texto.

    c. Clique em **Guardar**.  

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-kantegassoforbitbucket-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-kantegassoforbitbucket-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-kantegassoforbitbucket-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-kantegassoforbitbucket-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-kantega-sso-for-bitbucket-test-user"></a>Criar um SSO Kantega para o utilizador de teste de Bitbucket

Para permitir que os utilizadores do Azure AD iniciem sessão nos Bitbucket, têm de ser aprovisionados para Bitbucket. Em Kantega SSO para Bitbucket, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Bitbucket como administrador.

2. Clique no ícone de definições.

    ![Adicionar empregado](./media/active-directory-saas-kantegassoforbitbucket-tutorial/user1.png) 

3. Em **administração** separador de secção, clique em **utilizadores**.

    ![Adicionar empregado](./media/active-directory-saas-kantegassoforbitbucket-tutorial/user2.png)

4. Clique em **criar utilizador**.

    ![Adicionar empregado](./media/active-directory-saas-kantegassoforbitbucket-tutorial/user3.png)     

5. No **criar utilizador** diálogo página, execute os seguintes passos:

    ![Adicionar empregado](./media/active-directory-saas-kantegassoforbitbucket-tutorial/user4.png) 

    a. No **Username** caixa de texto, como o tipo de mensagem de correio eletrónico do utilizador Brittasimon@contoso.com.
    
    b. No **nome completo** caixa de texto, nome completo do tipo de utilizador como Britta Simon.
    
    c. No **endereço de correio eletrónico** caixa de texto, como o tipo de endereço de correio eletrónico do utilizador Brittasimon@contoso.com.

    d. No **palavra-passe** caixa de texto, escreva a palavra-passe do utilizador.  

    e. No **Confirmar palavra-passe** caixa de texto, reintroduza a palavra-passe do utilizador.

    f. Clique em **criar utilizador**.   

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Kantega SSO para Bitbucket.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Kantega SSO para Bitbucket, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Kantega SSO para Bitbucket**.

    ![Configurar o início de sessão único](./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Ao clicar o SSO Kantega para Bitbucket mosaico no painel de acesso, deve obter automaticamente com sessão iniciada para o SSO Kantega para aplicação Bitbucket.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforbitbucket-tutorial/tutorial_general_203.png

