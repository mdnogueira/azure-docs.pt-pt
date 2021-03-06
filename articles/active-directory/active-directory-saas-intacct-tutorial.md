---
title: "Tutorial: Integração do Azure Active Directory com Intacct | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Intacct."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: c203b192b9da0d280cbd7f6c123219242ee4a3d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Tutorial: Integração do Azure Active Directory com Intacct

Neste tutorial, irá aprender a integrar Intacct com o Azure Active Directory (Azure AD).

Integrar Intacct com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Intacct
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Intacct (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Intacct, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Intacct-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Intacct a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-intacct-from-the-gallery"></a>Adicionar Intacct a partir da Galeria
Para configurar a integração de Intacct com o Azure AD, terá de adicionar Intacct a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Intacct a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Intacct**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_search.png)

5. No painel de resultados, selecione **Intacct**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Intacct com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Intacct é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Intacct tem de ser estabelecida.

No Intacct, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Intacct, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Intacct](#creating-an-intacct-test-user)**  - para ter um homólogo de Britta Simon Intacct que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Intacct.

**Para configurar o Azure AD-início de sessão único com Intacct, execute os seguintes passos:**

1. No portal do Azure, no **Intacct** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_samlbase.png)

3. No **Intacct domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_url.png)

    No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de resposta real. Contacte [equipa de suporte de Intacct](https://us.intacct.com/support) para obter este valor.

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-intacct-tutorial/tutorial_general_400.png)

6. No **Intacct configuração** secção, clique em **configurar Intacct** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Intacct como administrador.

8. Clique em de **empresa** separador e, em seguida, clique em **informações da empresa**.

    ![Empresa](./media/active-directory-saas-intacct-tutorial/ic790037.png "empresa")

9. Clique em de **segurança** separador e, em seguida, clique em **editar**.

    ![Segurança](./media/active-directory-saas-intacct-tutorial/ic790038.png "segurança")

10. No **início de sessão (SSO) único** secção, execute os seguintes passos:

    ![Início de sessão único](./media/active-directory-saas-intacct-tutorial/ic790039.png "início de sessão único")

    a. Selecione **ativar o início de sessão único em**.

    b. Como **tipo de fornecedor de identidade**, selecione **SAML 2.0**.

    c. No **URL do emissor** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.
   
    d. No **URL de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.

    e. Abra o **base-64** codificado de certificado no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado** caixa.
   
    f. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-intacct-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-intacct-test-user"></a>Criar um utilizador de teste Intacct

Para configurar utilizadores do Azure AD, para que poderem iniciar sessão para Intacct, têm de ser aprovisionados para Intacct. Intacct, o aprovisionamento é uma tarefa manual.

**Aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Intacct** inquilino.

2. Clique em de **empresa** separador e, em seguida, clique em **utilizadores**.

    ![Os utilizadores](./media/active-directory-saas-intacct-tutorial/ic790041.png "utilizadores")
3. Clique em de **adicionar** separador.

    ![Adicionar](./media/active-directory-saas-intacct-tutorial/ic790042.png "adicionar")
4. No **informações do utilizador** secção, execute os seguintes passos:

    ![Informações do utilizador](./media/active-directory-saas-intacct-tutorial/ic790043.png "informações do utilizador")

    a. Introduza o **ID de utilizador**, a **Apelido**, **nome próprio**, a **endereço de correio eletrónico**, a **título**e o **Phone** de uma conta do Azure AD que pretende aprovisionar no **informações do utilizador** secção.

    b. Selecione o **privilégios de administrador** de uma conta do Azure AD que pretende aprovisionar.
   
    c. Clique em **Guardar**. Titular da conta do Azure AD recebe uma mensagem de e-mail e segue-se de uma ligação para confirmar a respetiva conta para ficar ativa.

>[!NOTE]
>Aprovisionar contas de utilizador do Azure AD, pode utilizar outras ferramentas de criação de conta de utilizador Intacct ou APIs fornecidas pelo Intacct.
        
### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Intacct.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Intacct, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Intacct**.

    ![Configurar o início de sessão único](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, teste configuração do Azure AD único início de sessão utilizando o painel de acesso.

Quando clica no mosaico Intacct no painel de acesso, esta deve ser iniciada automaticamente aplicação Intacct.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

