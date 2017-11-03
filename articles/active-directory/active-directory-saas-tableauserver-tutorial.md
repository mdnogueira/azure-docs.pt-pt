---
title: "Tutorial: Integração do Azure Active Directory com o servidor de Tableau | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Tableau servidor."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 6b35609d88fbbf649e15863901d521886db2a4d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integração do Azure Active Directory com o servidor de Tableau

Neste tutorial, irá aprender a integrar o servidor de Tableau com o Azure Active Directory (Azure AD).

Integrar o Tableau servidor com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao servidor Tableau
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao servidor Tableau (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o servidor de Tableau, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um servidor de Tableau-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar o servidor de Tableau a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-tableau-server-from-the-gallery"></a>Adicionar o servidor de Tableau a partir da Galeria
Para configurar a integração do servidor de Tableau com o Azure AD, tem de adicionar servidor Tableau na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o servidor de Tableau de galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Tableau servidor**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_search.png)

5. No painel de resultados, selecione **Tableau servidor**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o servidor Tableau com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Tableau Server é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no servidor de Tableau tem de ser estabelecida.

No servidor de Tableau, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o servidor de Tableau, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do servidor de Tableau](#creating-a-tableau-server-test-user)**  - para ter um homólogo de Britta Simon Tableau servidor que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de servidor Tableau.

**Para configurar o Azure AD-início de sessão único com o servidor de Tableau, execute os seguintes passos:**

1. No portal do Azure, no **Tableau servidor** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. No **URLs e de domínio do servidor Tableau** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://azure.<domain name>.link`
    
    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://azure.<domain name>.link`

    c. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > Os valores anteriores não são valores reais. Posteriormente, atualize os valores com o URL e o identificador da página de configuração de servidor Tableau real. 

4. Aplicação de servidor tableau espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos do **"Atributos de utilizador"** secção na página de integração de aplicações. A seguinte captura de ecrã mostra um exemplo para o mesmo.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-tableauserver-tutorial/3.png)
    
5. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributos token SAML, conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |    
    | o nome de utilizador | *User.DisplayName* |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_04.png)

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**


6. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Para obter SSO configurado para a sua aplicação, terá de iniciar sessão no seu inquilino Tableau servidor como administrador.
   
   a. Na configuração do servidor de Tableau, clique em de **SAML** separador.
  
    ![Configurar o início de sessão único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Selecione a caixa de verificação de **SAML de utilização para o início de sessão único**.
   
   c. URL de retorno de servidor tableau — o URL que os utilizadores do Tableau acederão, tais como http://tableau_server. Não é recomendado utilizar http://localhost. Utilizar um URL com uma barra (por exemplo, http://tableau_server/) não é suportada. Cópia **URL de retorno de servidor Tableau** e cole-o para o Azure AD **URL de início de sessão** textbox em **URLs e de domínio do servidor Tableau** secção.
   
   d. ID de entidade SAML — o ID de entidade identifica de forma a instalação de servidor Tableau para IdP. Pode introduzir o URL do servidor Tableau novamente aqui, se assim o desejar, mas não tem de ser o URL do servidor Tableau. Cópia **ID de entidade SAML** e cole-o para o Azure AD **identificador** textbox em **URLs e de domínio do servidor Tableau** secção.
     
   e. Clique em de **Exportar ficheiro de metadados** e abra-o na aplicação do editor de texto. Localizar o URL do serviço de consumidor asserção com Http Post e o índice 0 e copie o URL. Agora cole-o para o Azure AD **URL de resposta** textbox em **URLs e de domínio do servidor Tableau** secção.
   
   f. Localize o ficheiro de metadados da Federação transferido a partir do portal do Azure e, em seguida, carregá-la no **ficheiro de metadados de SAML Idp**.
   
   g. Clique em de **OK** botão na página de configuração do servidor Tableau.
   
    >[!NOTE] 
    >Cliente tem de carregar qualquer certificado na configuração Tableau servidor SAML SSO e irá obter ignorada no fluxo de SSO.
    >Se precisar de ajuda a configurar SAML no servidor de Tableau consulte este artigo [configurar SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-tableau-server-test-user"></a>Criar um utilizador de teste do servidor de Tableau

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon no servidor de Tableau. Terá de aprovisionar todos os utilizadores no servidor de Tableau. 

Esse nome de utilizador do utilizador deve corresponder ao valor que configurou o atributo personalizado do Azure AD de **username**. Com o mapeamento correto da integração deverão funcionar [configurar do Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar o administrador do servidor de Tableau na sua organização.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao servidor Tableau.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon Tableau servidor, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Tableau servidor**.

    ![Configurar o início de sessão único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de servidor Tableau no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de servidor Tableau.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png

