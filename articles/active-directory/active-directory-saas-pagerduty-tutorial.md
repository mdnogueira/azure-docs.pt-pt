---
title: "Tutorial: Integração do Azure Active Directory com PagerDuty | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e PagerDuty."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: bf5263ce4d8fbc231029c101f167f4b55a921e60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Tutorial: Integração do Azure Active Directory com PagerDuty

Neste tutorial, irá aprender a integrar PagerDuty com o Azure Active Directory (Azure AD).

Integrar PagerDuty com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao PagerDuty
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para PagerDuty (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com PagerDuty, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um PagerDuty-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar PagerDuty a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-pagerduty-from-the-gallery"></a>Adicionar PagerDuty a partir da Galeria
Para configurar a integração de PagerDuty com o Azure AD, terá de adicionar PagerDuty a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar PagerDuty a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **PagerDuty**, selecione **PagerDuty** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com PagerDuty com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no PagerDuty é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no PagerDuty tem de ser estabelecida.

No PagerDuty, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com PagerDuty, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste PagerDuty](#create-a-pagerduty-test-user)**  - para ter um homólogo de Britta Simon PagerDuty que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação PagerDuty.

**Para configurar o Azure AD-início de sessão único com PagerDuty, execute os seguintes passos:**

1. No portal do Azure, no **PagerDuty** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

3. No **PagerDuty domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio PagerDuty e os URLs únicos de informações de início de sessão](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenant-name>.pagerduty.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenant-name>.pagerduty.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente PagerDuty](https://www.pagerduty.com/support/) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-pagerduty-tutorial/tutorial_general_400.png)

6. No **PagerDuty configuração** secção, clique em **configurar PagerDuty** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de PagerDuty](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Pagerduty como administrador.

8. No menu na parte superior, clique em **as definições da conta**.
   
    ![Definições da conta](./media/active-directory-saas-pagerduty-tutorial/ic778535.png "definições da conta")

9. Clique em **de sessão único-**.
   
    ![De sessão único-](./media/active-directory-saas-pagerduty-tutorial/ic778536.png "de sessão único-")

10. No **ativar-início de sessão único (SSO)** página, execute os seguintes passos:
   
    ![Ativar o início de sessão único](./media/active-directory-saas-pagerduty-tutorial/ic778537.png "ativar o início de sessão único")
   
    a. Abra o certificado codificado de base-64 transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado x. 509** caixa de texto
  
    b. No **URL de início de sessão** caixa de texto, colar **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
  
    c. No **URL de fim de sessão** caixa de texto, colar **Sign-Out URL** que copiou do portal do Azure.
 
    d. Selecione **ativar início de sessão único**.
 
    e. Clique em **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![O botão de adição](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-pagerduty-test-user"></a>Criar um utilizador de teste PagerDuty

Para permitir que os utilizadores do Azure AD iniciem sessão nos PagerDuty, têm de ser aprovisionados para PagerDuty.  
No caso de PagerDuty, o aprovisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras Pagerduty utilizador conta criação ferramentas ou APIs fornecidas pelo Pagerduty para aprovisionar o Azure Active Directory contas de utilizador.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Pagerduty** inquilino.

2. No menu na parte superior, clique em **utilizadores**.

3. Clique em **adicionar utilizadores**.
   
    ![Adicionar utilizadores](./media/active-directory-saas-pagerduty-tutorial/ic778539.png "adicionar utilizadores")

4.  No **convidar a sua equipa** caixa de diálogo, execute os seguintes passos:
   
    ![Convidar a sua equipa](./media/active-directory-saas-pagerduty-tutorial/ic778540.png "convidar a sua equipa")

    a. Tipo de **primeiro e último nome** do utilizador, como **Britta Simon**. 
   
    b. Introduza **E-Mail** como o endereço do utilizador  **brittasimon@contoso.com** .
   
    c. Clique em **adicionar**e, em seguida, clique em **enviar Invites**.
   
    >[!NOTE]
    >Todos os utilizadores adicionados irão receber um convite para criar uma conta de PagerDuty.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para PagerDuty.

![Atribuir a função de utilizador][200]

**Para atribuir Britta Simon a PagerDuty, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **PagerDuty**.

    ![A ligação de PagerDuty na lista de aplicações](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico PagerDuty no Panelyou acesso deve obter automaticamente com sessão iniciada a sua aplicação PagerDuty.

Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_203.png

