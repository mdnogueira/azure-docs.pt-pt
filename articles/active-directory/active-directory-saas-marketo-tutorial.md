---
title: "Tutorial: Integração do Azure Active Directory com Marketo | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e do Marketo."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: e146fd5a8075bc9c7ba049b25e5f301fc2645ed9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: Integração do Azure Active Directory com Marketo

Neste tutorial, irá aprender a integração do Marketo no Azure Active Directory (Azure AD).

Integração do Marketo com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Marketo
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Marketo (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Marketo, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Marketo início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Marketo a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-marketo-from-the-gallery"></a>Adicionar Marketo a partir da Galeria
Para configurar a integração do Marketo com o Azure AD, terá de adicionar Marketo a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Marketo a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Marketo**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_search.png)

5. No painel de resultados, selecione **Marketo**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Marketo com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Marketo é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Marketo tem de ser estabelecida.

No Marketo, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Marketo, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Marketo](#creating-a-marketo-test-user)**  - para ter um homólogo de Britta Simon Marketo que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação do Marketo.

**Para configurar o Azure AD-início de sessão único com Marketo, execute os seguintes passos:**

1. No portal do Azure, no **Marketo** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_samlbase.png)

3. No **URLs e de domínio do Marketo** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://saml.marketo.com/sp`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador e o URL de resposta real. Contacte [equipa de suporte do Marketo](http://investors.marketo.com/contactus.cfm) para obter estes valores.
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_general_400.png)

6. No **Marketo configuração** secção, clique em **configurar Marketo** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_configure.png) 

7. Para obter o Id de Munchkin da sua aplicação, inicie sessão no Marketo utilizando credenciais de administrador e execute os seguintes ações:
   
    a. Inicie sessão na aplicação do Marketo com credenciais do administrador.
   
    b. Clique em de **Admin** botão no painel de navegação superior.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue para o menu de integração e clique o **ligação Munchkin**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie o Id de Munchkin apresentado no ecrã e concluir o seu URL de resposta no Assistente de configuração do Azure AD.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png) 

8. Para configurar o SSO na aplicação, siga os passos abaixo:
   
    a. Inicie sessão na aplicação do Marketo com credenciais do administrador.
   
    b. Clique em de **Admin** botão no painel de navegação superior.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue para o menu de integração e clique em **início de sessão único**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para ativar as definições de SAML, clique em **editar** botão.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Ativado** Single Sign-On definições.
   
    f. Colar o **ID de entidade de SAML**, no **emissor ID** caixa de texto.
   
    g. No **ID de entidade** caixa de texto, introduza o URL como `http://saml.marketo.com/sp`.
   
    h. Selecione a localização de ID de utilizador como **elemento identificador de nome**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se o identificador do seu utilizador não é o valor UPN e altere o valor no separador de atributo.
   
    posso. Carregue o certificado que transferiu a partir do Assistente de configuração do Azure AD. **Guardar** as definições.
   
    j. Edite as definições de páginas de redirecionamento.
   
    k. Colar o **único início de sessão no URL do serviço SAML** no **URL de início de sessão** caixa de texto.
   
    l. Colar o **Sign-Out URL** no **URL de fim de sessão** caixa de texto.
   
    m. No **erro URL**, copiar o **URL de instância do Marketo** e clique em **guardar** botão guardar as definições.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

9. Para ativar o SSO para que os utilizadores, conclua as seguintes ações:
   
    a. Inicie sessão na aplicação do Marketo com credenciais do administrador.
   
    b. Clique em de **Admin** botão no painel de navegação superior.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue para o **segurança** menu e **definições de início de sessão**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Verifique o **requerem SSO** opção e **guardar** as definições.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-marketo-test-user"></a>Criar um utilizador de teste do Marketo

Nesta secção, vai criar um utilizador chamado Britta Simon Marketo. Siga estes passos para criar um utilizador na plataforma do Marketo.

1. Inicie sessão na aplicação do Marketo com credenciais do administrador.

2. Clique em de **Admin** botão no painel de navegação superior.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 

3. Navegue para o **segurança** menu e **utilizadores e funções**
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  

4. Clique em de **convidar novo utilizador** ligação no separador de utilizadores
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 

5. No Assistente de convidar novo utilizador preencher as seguintes informações
   
    a. Introduza o utilizador **E-Mail** endereço na caixa de texto
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. Introduza o **nome próprio** na caixa de texto
   
    c. Introduza o **Apelido** na caixa de texto
   
    d. Clique em **Seguinte**

6. No **permissões** separador, selecione o **userRoles** e clique em **seguinte**
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Clique em de **enviar** botão para enviar o convite de utilizador
   
    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)

8. Recebe a notificação de correio eletrónico e tem de clicar na ligação e alterar a palavra-passe para ativar a conta de utilizador. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder acesso ao Marketo.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon ao Marketo, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Marketo**.

    ![Configurar o início de sessão único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do Marketo no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação do Marketo.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png

