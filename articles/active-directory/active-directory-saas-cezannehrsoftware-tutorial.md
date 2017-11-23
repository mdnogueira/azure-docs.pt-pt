---
title: "Tutorial: Integração do Azure Active Directory com o Software de RH Cezanne | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Software de RH Cezanne."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: jeedes
ms.openlocfilehash: cf44d749ecbfcffb3d5a6e5e12aa49e66f7cde2e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integração do Azure Active Directory com o Software de RH Cezanne

Neste tutorial, irá aprender a integrar Cezanne HR Software com o Azure Active Directory (Azure AD).

Integrar Cezanne HR Software com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Software de RH Cezanne.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para o Software de RH Cezanne (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Software de RH Cezanne, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Software de RH Cezanne-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Cezanne HR Software a partir da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adicionar Cezanne HR Software a partir da galeria do
Para configurar a integração do Software de RH Cezanne com o Azure AD, terá de adicionar Cezanne HR Software a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Cezanne HR Software a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Cezanne HR Software**, selecione **Cezanne HR Software** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Software de RH Cezanne na lista de resultados](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com o Software de RH Cezanne com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Software de RH Cezanne é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Software de RH Cezanne tem de ser estabelecida.

No Software de RH Cezanne, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o Software de RH Cezanne, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Software de RH Cezanne](#create-a-cezannehrsoftware-test-user)**  - para ter um homólogo de Britta Simon Cezanne HR Software que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Cezanne HR Software.

**Para configurar o Azure AD-início de sessão único com o Software de RH Cezanne, execute os seguintes passos:**

1. No portal do Azure, no **Cezanne HR Software** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. No **Cezanne HR Software domínio e os URLs** secção, execute os seguintes passos:

    ![Cezanne HR Software domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva o URL:`https://w3.cezanneondemand.com/CezanneOnDemand/-/optyma`

    b. No **identificador** caixa de texto, escreva o URL:`https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. No **URL de resposta** caixa de texto, escreva o URL:`https://w3.cezanneondemand.com:443/cezanneondemand/-/optyma/Saml/samlp`

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

6. No **Cezanne HR Software configuração** secção, clique em **configurar Cezanne HR Software** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração do Software Cezanne HR](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. Numa janela do browser web diferente, início de sessão no seu inquilino Cezanne HR Software como um administrador.

8. No painel de navegação esquerdo, clique em **a configuração do sistema**. Aceda a **definições de segurança**. Em seguida, navegue para **configuração de início de sessão único**.

    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. No **permitem aos utilizadores iniciar sessão com o seguinte serviço único Sign-On (SSO)** painel, verifique o **SAML 2.0** caixa e selecione o **configuração avançada** opção.

    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Clique em **adicionar novo** botão.

    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. Execute os seguintes passos no **fornecedores de identidade SAML 2.0** secção.

    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Introduza o nome do seu fornecedor de identidade como o **nome a apresentar**.

    b. No **identificador da entidade** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure. 

    c. Alterar o **SAML enlace** para 'POST'.

    d. No **ponto final de serviço de Token segurança** caixa de texto, colar o valor de **SAML URL Single Sign-on serviço** que copiou do portal do Azure.

    e. Na caixa de texto nome do atributo de ID de utilizador, introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Clique em **carregar** ícone para carregar o certificado transferido a partir do portal do Azure.
    
    g. Clique no botão **OK**. 

12. Clique em **guardar** botão.

    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Criar um utilizador de teste Cezanne HR Software

Para permitir que os utilizadores do Azure AD inicie sessão no Software de RH Cezanne, têm de ser aprovisionados para Cezanne HR Software. No caso de Software de RH Cezanne, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1.  Inicie sessão no site da sua empresa Cezanne HR Software como um administrador.

2.  No painel de navegação esquerdo, clique em **a configuração do sistema**. Aceda a **gerir utilizadores**. Em seguida, navegue para **adicionar novo utilizador**.

    ![Novo utilizador](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "novo utilizador")

3.  No **pessoa detalhes** secção, execute passos abaixo:

    ![Novo utilizador](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "novo utilizador")
    
    a. Definir **utilizador interno** como OFF.
    
    b. No **nome próprio** caixa de texto, como o tipo do nome próprio do utilizador **Britta**.  
 
    c. No **Apelido** caixa de texto, como o tipo o último nome de utilizador **Simon**.
    
    d. No **correio electrónico** caixa de texto, como o tipo de endereço de correio eletrónico do utilizador Brittasimon@contoso.com.

4.  No **informações da conta** secção, execute passos abaixo:

    ![Novo utilizador](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "novo utilizador")
    
    a. No **Username** caixa de texto, como o tipo de mensagem de correio eletrónico do utilizador Brittasimon@contoso.com.
    
    b. No **palavra-passe** caixa de texto, escreva a palavra-passe do utilizador.
    
    c. Selecione **HR Professional** como **função de segurança**.
    
    d. Clique em **OK**.

5. Navegue para **Single Sign-On** separador e selecione **adicionar novo** no **SAML 2.0 identificadores** área.

    ![Utilizador](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "utilizador")

6. Escolha o fornecedor de identidade para o **fornecedor de identidade** e na caixa de texto de **identificador de utilizador**, introduza o endereço de correio eletrónico da conta de Britta Simon.

    ![Utilizador](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "utilizador")
    
7. Clique em **guardar** botão.

    ![Utilizador](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "utilizador")

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao Software de RH Cezanne.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon Cezanne HR software, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Cezanne HR Software**.

    ![A ligação ao Software de RH Cezanne na lista de aplicações](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do Software de RH Cezanne no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Software de RH Cezanne.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

