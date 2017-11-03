---
title: "Tutorial: Integração do Azure Active Directory com NetDocuments | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e NetDocuments."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 87c3338d611daa837aa5f079c4b68e0e6fc58455
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Tutorial: Integração do Azure Active Directory com NetDocuments

Neste tutorial, irá aprender a integrar NetDocuments com o Azure Active Directory (Azure AD).

Integrar NetDocuments com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao NetDocuments
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para NetDocuments (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com NetDocuments, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um NetDocuments-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar NetDocuments a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-netdocuments-from-the-gallery"></a>Adicionar NetDocuments a partir da Galeria
Para configurar a integração de NetDocuments com o Azure AD, terá de adicionar NetDocuments a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar NetDocuments a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **NetDocuments**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netdocuments-tutorial/tutorial_netdocuments_search.png)

5. No painel de resultados, selecione **NetDocuments**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netdocuments-tutorial/tutorial_netdocuments_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com NetDocuments com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no NetDocuments é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no NetDocuments tem de ser estabelecida.

No NetDocuments, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com NetDocuments, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste NetDocuments](#creating-a-netdocuments-test-user)**  - para ter um homólogo de Britta Simon NetDocuments que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação NetDocuments.

**Para configurar o Azure AD-início de sessão único com NetDocuments, execute os seguintes passos:**

1. No portal do Azure, no **NetDocuments** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-netdocuments-tutorial/tutorial_netdocuments_samlbase.png)

3. No **NetDocuments domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-netdocuments-tutorial/tutorial_netdocuments_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<user identifier>`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão real e o URL de resposta. Contacte [NetDocuments suporta equipa](https://support.netdocuments.com/hc/) para obter estes valores.
 
4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-netdocuments-tutorial/tutorial_netdocuments_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-netdocuments-tutorial/tutorial_general_400.png)

6. Numa janela do browser web diferente, inicie sessão no site da sua empresa NetDocuments como administrador.

7. Aceda a **Admin**.

8. Clique em **adicionar e remover utilizadores e grupos**.
   
    ![Repositório](./media/active-directory-saas-netdocuments-tutorial/ic795047.png "repositório")

9. Clique em **configurar opções de autenticação avançadas**.
    
    ![Configurar opções de autenticação avançadas](./media/active-directory-saas-netdocuments-tutorial/ic795048.png "configurar opções de autenticação avançadas")

10. No **identidade federada** caixa de diálogo, execute os seguintes passos:
   
    ![Federado Identitty](./media/active-directory-saas-netdocuments-tutorial/ic795049.png "federado Identitty")
   
    a. Como **federado o tipo de identidade do servidor**, selecione **serviços de Federação do Active Directory**.
   
    b. Clique em **Escolher ficheiro**, carregar o ficheiro de metadados transferido que transferiu a partir do portal do Azure.
   
    c. Clique em **OK**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netdocuments-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netdocuments-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netdocuments-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netdocuments-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-netdocuments-test-user"></a>Criar um utilizador de teste NetDocuments

Para permitir que os utilizadores do Azure AD iniciem sessão nos NetDocuments, têm de ser aprovisionados para NetDocuments.  
No caso de NetDocuments, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Iniciar sessão no seu **NetDocuments** site da empresa como administrador.

2. No menu na parte superior, clique em **Admin**.
   
    ![Administração](./media/active-directory-saas-netdocuments-tutorial/ic795051.png "Admin")

3. Clique em **adicionar e remover utilizadores e grupos**.
   
    ![Repositório](./media/active-directory-saas-netdocuments-tutorial/ic795047.png "repositório")

4. No **endereço de correio eletrónico** caixa de texto, escreva o endereço de e-mail de uma conta válida do Azure Active Directory que pretende aprovisionar e, em seguida, clique em **adicionar utilizador**.
   
    ![Endereço de correio eletrónico](./media/active-directory-saas-netdocuments-tutorial/ic795053.png "endereço de E-Mail")
   
   >[!NOTE]
   >O marcador de posição de conta do Azure Active Directory irá receber uma mensagem de e-mail que inclui uma ligação para confirmar a conta para ficar ativa. Pode utilizar quaisquer outras NetDocuments utilizador conta criação ferramentas ou APIs fornecidas pelo NetDocuments para aprovisionar o Azure Active Directory contas de utilizador.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para NetDocuments.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a NetDocuments, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **NetDocuments**.

    ![Configurar o início de sessão único](./media/active-directory-saas-netdocuments-tutorial/tutorial_netdocuments_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico NetDocuments no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de NetDocuments.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netdocuments-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netdocuments-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netdocuments-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netdocuments-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netdocuments-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netdocuments-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netdocuments-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netdocuments-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netdocuments-tutorial/tutorial_general_203.png

