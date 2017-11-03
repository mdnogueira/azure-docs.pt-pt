---
title: "Tutorial: Integração do Azure Active Directory com Questetra BPM Suite | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Questetra BPM Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 89d1ebc090a7b667885f5dcbab14312efd767f0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração do Azure Active Directory com Questetra BPM Suite

Neste tutorial, irá aprender a integrar Questetra BPM Suite com o Azure Active Directory (Azure AD).

Integrar Questetra BPM Suite com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Questetra BPM Suite
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Questetra BPM Suite (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Questetra BPM Suite, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Questetra BPM Suite-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Questetra BPM Suite a partir da Galeria
2. Configurar e testar o Azure AD-início de sessão único

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Adicionar Questetra BPM Suite a partir da Galeria
Para configurar a integração do Questetra BPM Suite com o Azure AD, terá de adicionar Questetra BPM Suite a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Questetra BPM Suite a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Questetra BPM Suite**, selecione **Questetra BPM Suite** do painel de resultados e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Adicionar a partir da Galeria](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com Questetra BPM Suite com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Questetra BPM Suite é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Questetra BPM Suite tem de ser estabelecida.

No Questetra BPM Suite, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Questetra BPM Suite, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)**  - para ter um homólogo de Britta Simon Questetra BPM Suite que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Questetra BPM Suite.

**Para configurar o Azure AD-início de sessão único com Questetra BPM Suite, execute os seguintes passos:**

1. No portal do Azure, no **Questetra BPM Suite** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Baseados em SAML início de sessão](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. No **Questetra BPM Suite domínio e os URLs** secção, execute os seguintes passos:

    ![Secção Questetra BPM Suite domínio e os URLs](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Pode obter estes valores na **SP informações** secção no seu **Questetra BPM Suite** site da empresa, que é explicada mais tarde no tutorial ou contacte [suporte Questetra BPM Suite cliente equipa](https://www.questetra.com/contact/). 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base 64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Secção de certificado de assinatura de SAML](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. Clique em **guardar** botão.

    ![Botão Guardar](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. No **Questetra BPM Suite configuração** secção, clique em **configurar o Questetra BPM Suite** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Secção de configuração do Questetra BPM Suite](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no seu **Questetra BPM Suite** site da empresa como administrador.

8. No menu na parte superior, clique em **definições do sistema**. 
   
    ![Azure AD início de sessão único][10]

9. Para abrir o **SingleSignOnSAML** página, clique em **SSO (SAML)**. 
   
    ![Azure AD início de sessão único][11]

10. No seu **Questetra BPM Suite** da empresa site, no **SP informações** secção, execute os seguintes passos:

    a. Copiar o **URL de ACS**e, em seguida, cole-o para o **URL de início de sessão** textbox no **Questetra BPM Suite domínio e os URLs** secção a partir do portal do Azure.
    
    b. Copiar o **ID de entidade**e, em seguida, cole-o para o **identificador** textbox no **Questetra BPM Suite domínio e os URLs** secção a partir do portal do Azure.

11. No seu **Questetra BPM Suite** da empresa site, execute os seguintes passos: 
   
    ![Configurar o início de sessão único][15]
   
    a. Selecione **ativar o início de sessão único**.
   
    b. No **ID de entidade** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.
    
    c. No **URL de página de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
    
    d. No **URL da página de início de sessão** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure.
    
    e. No **NameID formato** caixa de texto, tipo `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Abra o **Base-64** certificado codificado no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-o para o **certificado validação** caixa de texto. 

    g. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Criar um utilizador de teste Questetra BPM Suite

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon no Questetra BPM Suite.

**Para criar um utilizador chamado Britta Simon no Questetra BPM Suite, execute os seguintes passos:**

1. Inicie sessão site da sua empresa Questetra BPM Suite como administrador.
2. Aceda a **definições do sistema > lista de utilizadores > novo utilizador**. 
3. Na caixa de diálogo novo utilizador, execute os seguintes passos: 
   
    ![Criar utilizador de teste][300] 
   
    a. No **nome** caixa de texto, tipo **nome** do utilizador  **britta.simon@contoso.com** .
   
    b. No **E-Mail** caixa de texto, tipo **e-mail** do utilizador**britta.simon@contoso.com**
   
    c. No **palavra-passe** caixa de texto, escreva um **palavra-passe** do utilizador.
    
    d. Clique em **adicionar novo utilizador**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Questetra BPM Suite.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Questetra BPM Suite, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Questetra BPM Suite**.

    ![Questetra BPM Suite na lista de aplicações](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Questetra BPM Suite no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Questetra BPM Suite.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

