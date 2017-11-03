---
title: "Tutorial: Integração do Azure Active Directory com tela Lms | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e LMS da tela."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: 2212b7a81b66d1afd1aa78d1487b07b6d7b84129
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Tutorial: Integração do Azure Active Directory com LMS tela

Neste tutorial, irá aprender a integrar tela com o Azure Active Directory (Azure AD).

Integrar tela com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à tela
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a tela (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com tela, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um tela início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar tela a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-canvas-from-the-gallery"></a>Adicionar tela a partir da Galeria
Para configurar a integração da tela com o Azure AD, tem de adicionar tela da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar tela da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **tela**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_search.png)

5. No painel de resultados, selecione **tela**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com tela com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na tela é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na tela tem de ser estabelecida.

Na tela, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com tela, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste da tela](#creating-a-canvas-test-user)**  - para ter um homólogo de Britta Simon na tela, que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação da tela.

**Para configurar o Azure AD-início de sessão único com tela, execute os seguintes passos:**

1. No portal do Azure, no **tela** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

3. No **domínio tela e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenant-name>.instructure.com`

    b. No **identificador** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://<tenant-name>.instructure.com/saml2`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente da tela](https://community.canvaslms.com/community/help) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, copie o **THUMBPRINT** valor do certificado.

    ![Configurar o início de sessão único](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_400.png)

6. No **tela configuração** secção, clique em **configurar tela** para abrir **configurar início de sessão** janela. Copiar o **URL de palavra-passe de alteração, Sign-Out URL, ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
7. Numa janela do browser web diferente, inicie sessão no site da sua empresa tela como administrador.

8. Aceda a **Courses \> contas geridas \> Microsoft**.
   
    ![Tela](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "tela")

9. No painel de navegação à esquerda, selecione **autenticação**e, em seguida, clique em **Adicionar nova configuração de SAML**.
   
    ![Autenticação](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "autenticação")

10. Na página integração atual, execute os seguintes passos:
   
    ![Integração atual](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "integração atual")

    a. No **ID da entidade IdP** caixa de texto, colar o valor de **ID de entidade de SAML** que copiou do portal do Azure.

    b. No **registo no URL** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.

    c. No **URL terminar sessão** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure.

    d. No **hiperligação palavra-passe** caixa de texto, cole o valor de **alterar palavra-passe URL** que copiou do portal do Azure. 

    e. No **impressão digital do certificado** caixa de texto, cole o **Thumbprint** valor do certificado que copiou do portal do Azure.      
        
    f. Do **atributo de início de sessão** lista, selecione **NameID**.

    g. Do **identificador formato** lista, selecione **emailAddress**.

    h. Clique em **guardar as definições de autenticação**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-canvas-test-user"></a>Criar um utilizador de teste da tela

Para permitir que os utilizadores do Azure AD iniciem sessão tela, têm de ser aprovisionados para a tela.

Em caso de tela, o aprovisionamento de utilizador é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **tela** inquilino.

2. Aceda a **Courses \> contas geridas \> Microsoft**.
   
   ![Tela](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "tela")

3. Clique em **Utilizadores**.
   
   ![Os utilizadores](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "utilizadores")

4. Clique em **adicionar novo utilizador**.
   
   ![Os utilizadores](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "utilizadores")

5. Em Adicionar uma página de caixa de diálogo novo utilizador, execute os seguintes passos:
   
   ![Adicionar utilizador](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "adicionar utilizador")
   
   a. No **nome completo** caixa de texto, introduza o nome de utilizador como **BrittaSimon**.

   b. No **E-Mail** caixa de texto, introduza o e-mail do utilizador, como  **brittasimon@contoso.com** .

   c. No **início de sessão** caixa de texto, introduza o endereço de correio eletrónico do utilizador do Azure AD como  **brittasimon@contoso.com** .

   d. Selecione **o utilizador sobre a criação de conta de E-Mail**.

   e. Clique em **adicionar utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras tela utilizador conta criação ferramentas ou APIs fornecidas pelo tela para aprovisionar contas de utilizador do AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à tela.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a tela, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **tela**.

    ![Configurar o início de sessão único](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico da tela no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação da tela.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_203.png

