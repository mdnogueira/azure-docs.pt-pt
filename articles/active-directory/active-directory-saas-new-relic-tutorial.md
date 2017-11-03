---
title: "Tutorial: Integração do Azure Active Directory com New Relic | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e do New Relic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: 45106b499b9a541d5e6120538cfe1715608be118
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Integração do Azure Active Directory com New Relic

Neste tutorial, irá aprender a integração do New Relic com o Azure Active Directory (Azure AD).

Integração do New Relic com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao New Relic.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para New Relic (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com New Relic, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um New Relic-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição do New Relic na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-new-relic-from-the-gallery"></a>A adição do New Relic na galeria do
Para configurar a integração do New Relic com o Azure AD, terá de adicionar New Relic na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar New Relic na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **New Relic**, selecione **New Relic** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![New Relic na lista de resultados](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com New Relic com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no New Relic é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no New Relic tem de ser estabelecida.

No New Relic, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com New Relic, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do New Relic](#create-a-new-relic-test-user)**  - para ter um homólogo de Britta Simon New Relic que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação do New Relic.

**Para configurar o Azure AD-início de sessão único com New Relic, execute os seguintes passos:**

1. No portal do Azure, no **New Relic** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_samlbase.png)

3. No **novo domínio Relic e URLs** secção, execute os seguintes passos:

    ![Novo domínio Relic e os URLs únicos de informações de início de sessão](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.newrelic.com`

    b. No **identificador** caixa de texto, digite o valor:`rpm.newrelic.com`

    > [!NOTE] 
    > Início de sessão do valor de URL não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte do novo cliente Relic](https://support.newrelic.com/) para obter este valor. 

4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-new-relic-tutorial/tutorial_general_400.png)

6. No **nova configuração de Relic** secção, clique em **configurar New Relic** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Nova configuração de Relic](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no seu **New Relic** site da empresa como administrador.

8. No menu na parte superior, clique em **as definições da conta**.
   
    ![Definições da conta](./media/active-directory-saas-new-relic-tutorial/ic797036.png "definições da conta")

9. Clique em de **segurança e autenticação** separador e, em seguida, clique em de **início de sessão único** separador.
   
    ![De sessão único-](./media/active-directory-saas-new-relic-tutorial/ic797037.png "Single Sign-On")

10. Na página de caixa de diálogo SAML, execute os seguintes passos:
   
    ![SAML](./media/active-directory-saas-new-relic-tutorial/ic797038.png "SAML")
   
   a. Clique em **Escolher ficheiro** para carregar o certificado transferido do Azure Active Directory.

   b. No **URL de início de sessão remoto** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.
   
   c. No **destino URL de fim de sessão** caixa de texto, cole o valor de **Sign-Out URL**, que copiou do portal do Azure.

   d. Clique em **guardar as minhas alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-new-relic-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-new-relic-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-new-relic-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-new-relic-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-new-relic-test-user"></a>Criar um utilizador de teste do New Relic

Para permitir que os utilizadores do Azure Active Directory inicie sessão no New Relic, têm de ser aprovisionados para New Relic. No caso do New Relic, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador para New Relic, execute os seguintes passos:**

1. Inicie sessão no seu **New Relic** site da empresa como administrador.

2. No menu na parte superior, clique em **as definições da conta**.
   
    ![Definições da conta](./media/active-directory-saas-new-relic-tutorial/ic797040.png "definições da conta")

3. No **conta** painel no lado esquerdo, clique em **resumo**e, em seguida, clique em **adicionar utilizador**.
   
    ![Definições da conta](./media/active-directory-saas-new-relic-tutorial/ic797041.png "definições da conta")

4. No **utilizadores ativos** caixa de diálogo, execute os seguintes passos:
   
    ![Utilizadores ativos](./media/active-directory-saas-new-relic-tutorial/ic797042.png "utilizadores ativos")
   
    a. No **E-Mail** caixa de texto, escreva o endereço de e-mail de um utilizador válido do Azure Active Directory que pretende aprovisionar.

    b. Como **função** selecione **utilizador**.

    c. Clique em **adicione este utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras New Relic utilizador conta criação ferramentas ou APIs fornecidas pelo New Relic para aprovisionar contas de utilizador do AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder acesso a New Relic.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a New Relic, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **New Relic**.

    ![A ligação do New Relic na lista de aplicações](./media/active-directory-saas-new-relic-tutorial/tutorial_new-relic_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do New Relic no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação do New Relic.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-new-relic-tutorial/tutorial_general_203.png

