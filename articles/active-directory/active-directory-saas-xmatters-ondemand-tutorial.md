---
title: "Tutorial: Integração do Azure Active Directory com xMatters OnDemand | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e xMatters OnDemand."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 9bfcb44ed19f167872b3cd9119e2dbdd35c82604
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Integração do Azure Active Directory com xMatters OnDemand

Neste tutorial, irá aprender a integrar xMatters OnDemand com o Azure Active Directory (Azure AD).

Integrar xMatters OnDemand com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao xMatters OnDemand
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para xMatters OnDemand (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com xMatters OnDemand, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um xMatters OnDemand-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar xMatters OnDemand a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Adicionar xMatters OnDemand a partir da Galeria
Para configurar a integração de xMatters OnDemand com o Azure AD, terá de adicionar xMatters OnDemand a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar xMatters OnDemand a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **xMatters OnDemand**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

5. No painel de resultados, selecione **xMatters OnDemand**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com xMatters que ondemand com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber que o utilizador de homólogo xMatters OnDemand for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no xMatters OnDemand tem de ser estabelecida.

No xMatters OnDemand, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com xMatters OnDemand, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de OnDemand xMatters](#creating-a-xmatters-ondemand-test-user)**  - para ter um homólogo de Britta Simon no xMatters OnDemand que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua xMatters OnDemand aplicação.

**Para configurar o Azure AD-início de sessão único com xMatters OnDemand, execute os seguintes passos:**

1. No portal do Azure, no **xMatters OnDemand** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

3. No **xMatters OnDemand domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:   
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador e o URL de resposta real. Contacte [xMatters OnDemand suporta equipa](https://www.xmatters.com/company/contact-us/) para obter estes valores.

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado localmente como **c:\\XMatters OnDemand.cer**.

    ![Configurar o início de sessão único](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)
    
    > [!IMPORTANT]
    > É necessário reencaminhar o certificado para o [xMatters OnDemand suporta equipa](https://www.xmatters.com/company/contact-us/). O certificado tem de ser carregado pela equipa de suporte do xMatters antes de pode finalizar a configuração de início de sessão único. 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_400.png)

6. No **xMatters OnDemand configuração** secção, clique em **configurar xMatters OnDemand** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa XMatters OnDemand como administrador.

8. Na barra de ferramentas na parte superior, clique em **Admin**e, em seguida, clique em **os dados da empresa** na barra de navegação no lado esquerdo.
   
    ![Administração](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

9. No **configuração SAML** página, execute os seguintes passos:
   
    ![Configuração de SAML](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "configuração SAML")
   
    a. Selecione **ativar SAML**.
   
    b. Colar **ID de entidade de SAML**, que copiou do portal do Azure para o **ID do fornecedor de identidade** caixa de texto.
   
    c. Colar **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure para o **início de sessão único no URL** caixa de texto.
   
    d. Colar **Sign-Out URL**, que copiou do portal do Azure para o **URL de fim de sessão único** caixa de texto.
   
    e. Na página de detalhes da empresa, na parte superior, clique em **guardar alterações**.
    
    ![Detalhes de empresa](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "da empresa detalhes")

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-xmatters-ondemand-test-user"></a>Criar um utilizador de teste de OnDemand xMatters

Para permitir que os utilizadores do Azure AD iniciem sessão nos XMatters OnDemand, têm de ser aprovisionados para XMatters OnDemand. No caso de XMatters OnDemand, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionar contas de utilizador, execute os seguintes passos:
1. Inicie sessão no seu **XMatters OnDemand** inquilino.

2.  Clique em **utilizadores** separador. e, em seguida, clique em **adicionar utilizador**.
  
    ![Os utilizadores](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "utilizadores")

3. No **adicionar um utilizador** secção, execute os seguintes passos:
   
    ![Adicionar um utilizador](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "adicionar um utilizador")

    a. Selecione **Active Directory**.

    b. No **ID de utilizador** caixa de texto, como o tipo de id de utilizador do utilizador Brittasimon@contoso.com.
   
    c. No **nome próprio** caixa de texto, nome do primeiro tipo de utilizador como Britta.

    d. No **Apelido** caixa de texto, apelido tipo do utilizador como Simon.
    
    e. No **Site** caixa de texto, introduza o site válido de um Azure válido conta do AD que pretende aprovisionar.
    
    f. Clique em **Guardar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso aos xMatters OnDemand.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a xMatters OnDemand, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **xMatters OnDemand**.

    ![Configurar o início de sessão único](./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Ao clicar o xMatters OnDemand mosaico no painel de acesso, deve obter automaticamente com sessão iniciada para sua xMatters OnDemand aplicação.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-xmatters-ondemand-tutorial/tutorial_general_203.png

