---
title: "Tutorial: Integração do Azure Active Directory com o suporte técnico de Jitbit | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o suporte técnico de Jitbit."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 6523ee3179dafd79528093b856b0ec10fafb4f7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração do Azure Active Directory com o suporte técnico de Jitbit

Neste tutorial, irá aprender a integrar o suporte técnico de Jitbit com o Azure Active Directory (Azure AD).

Integrar o suporte técnico de Jitbit com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao suporte técnico de Jitbit
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o suporte técnico de Jitbit (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o suporte técnico de Jitbit, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um técnico de Jitbit-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de suporte técnico de Jitbit de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>A adição de suporte técnico de Jitbit de galeria
Para configurar a integração do suporte técnico de Jitbit com o Azure AD, tem de adicionar o suporte técnico de Jitbit na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o suporte técnico de Jitbit na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Jitbit técnico**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

5. No painel de resultados, selecione **Jitbit técnico**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o suporte técnico de Jitbit baseado na chamado "Britta Simon" um utilizador de teste.

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no suporte técnico de Jitbit é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no suporte técnico de Jitbit tem de ser estabelecida.

No suporte técnico de Jitbit, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o suporte técnico de Jitbit, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de suporte técnico de Jitbit](#creating-a-jitbit-helpdesk-test-user)**  - para ter um homólogo de Britta Simon no suporte técnico Jitbit que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Jitbit técnico.

**Para configurar o Azure AD-início de sessão único com o suporte técnico de Jitbit, execute os seguintes passos:**

1. No portal do Azure, no **Jitbit técnico** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

3. No **Jitbit domínio de suporte técnico e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte: 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de suporte técnico Jitbit](https://www.jitbit.com/support/) para obter este valor. 
    
    b.  No **identificador** caixa de texto, escreva um URL seguinte forma:`https://www.jitbit.com/web-helpdesk/`

    
 


4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_400.png)

6. No **configuração de suporte técnico Jitbit** secção, clique em **configurar o suporte técnico de Jitbit** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Jitbit técnico como administrador.

8. Na barra de ferramentas na parte superior, clique em **administração**.
   
    ![Administração](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "administração")

9. Clique em **definições gerais**.
   
    ![Os utilizadores, as empresas e permissões](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777680.png "utilizadores, as empresas e permissões")

10. No **definições de autenticação** configuração secção, execute os seguintes passos:
   
    ![Definições de autenticação](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777683.png "definições de autenticação")
    
    a. Selecione **ativar SAML 2.0 único início de sessão**, para iniciar sessão com o único Sign-On (SSO), com **OneLogin**.

    b. No **URL de ponto final** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.

    c. Abra o **base-64** codificado de certificado no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado x. 509** caixa de texto

    d. Clique em **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, nome do tipo como **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Criar um utilizador de teste de suporte técnico de Jitbit

Para permitir que os utilizadores do Azure AD sessão Jitbit técnico, têm de ser aprovisionados para Jitbit técnico.  No caso de suporte técnico de Jitbit, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Jitbit técnico** inquilino.

2. No menu na parte superior, clique em **administração**.
   
    ![Administração](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "administração")

3. Clique em **permissões de utilizadores, as empresas e**.
   
    ![Os utilizadores, as empresas e permissões](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777682.png "utilizadores, as empresas e permissões")

4. Clique em **adicionar utilizador**.
   
    ![Adicionar utilizador](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777685.png "adicionar utilizador")
   
5. Na secção de criar, escreva os dados da conta do Azure AD que pretende aprovisionar da seguinte forma:

    ![Criar](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777686.png "criar")
   
   a. No **Username** caixa de texto, tipo **BrittaSimon**, o nome de utilizador como no portal do Azure.

   b. No **E-Mail** caixa de texto, como o tipo de e-mail do utilizador  **BrittaSimon@contoso.com** .

   c. No **nome próprio** caixa de texto, nome do primeiro tipo de utilizador como **Britta**.

   d. No **Apelido** caixa de texto, apelido tipo do utilizador, como **Simon**.
   
   e. Clique em **Criar**.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de suporte técnico de Jitbit ou APIs fornecidas pelo suporte técnico de Jitbit aprovisionar contas de utilizador do Azure AD.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para o suporte técnico de Jitbit.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon para o suporte técnico de Jitbit, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Jitbit técnico**.

    ![Configurar o início de sessão único](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de suporte técnico de Jitbit no painel de acesso, deve obter a página de início de sessão da aplicação do suporte técnico de Jitbit.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_203.png

