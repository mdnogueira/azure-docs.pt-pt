---
title: "Tutorial: Integração do Azure Active Directory com Evernote | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Evernote."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: be94152a84bbbeacb623d7dd8b540e3981931a8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Tutorial: Integração do Azure Active Directory com Evernote

Neste tutorial, irá aprender a integrar Evernote com o Azure Active Directory (Azure AD).

Integrar Evernote com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Evernote.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Evernote (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Evernote, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Evernote-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Evernote a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-evernote-from-the-gallery"></a>Adicionar Evernote a partir da Galeria
Para configurar a integração de Evernote com o Azure AD, terá de adicionar Evernote a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Evernote a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Evernote**, selecione **Evernote** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Evernote na lista de resultados](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Evernote com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Evernote é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Evernote tem de ser estabelecida.

No Evernote, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Evernote, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Evernote](#create-an-evernote-test-user)**  - para ter um homólogo de Britta Simon Evernote que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Evernote.

**Para configurar o Azure AD-início de sessão único com Evernote, execute os seguintes passos:**

1. No portal do Azure, no **Evernote** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. No **Evernote domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no modo de IDP iniciado:

    ![Domínio Evernote e os URLs únicos de informações de início de sessão](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url.png)

    No **identificador** caixa de texto, escreva o URL:`https://www.evernote.com/saml2`

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Domínio Evernote e os URLs únicos de informações de início de sessão](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url1.png)

    No **iniciar sessão no URL** caixa de texto, escreva o URL:`https://www.evernote.com/Login.action`   

5. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

7. No **Evernote configuração** secção, clique em **configurar Evernote** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de Evernote](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

8. Numa janela do browser web diferente, inicie sessão no site da sua empresa Evernote como administrador.

9. Aceda a **consola de administração**

    ![Consola de administração](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

10. Do **consola de administração**, aceda a **'Security'** e selecione **' Single Sign-On'**

    ![Definição de SSO](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

11. Configure os seguintes valores:

    ![Definição de certificado](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Ativar a SSO:** SSO está ativado por predefinição (clique **desativar Single Sign-on** para remover o requisito de SSO)

    b. Colar **SAML-início de sessão único URL de serviço** valor que copiou do portal do Azure para o **URL de pedido de HTTP de SAML** caixa de texto.

    c. Abra o certificado transferido do Azure AD num bloco de notas e copie o conteúdo, incluindo "Começar CERTIFICATE" e "END CERTIFICATE" e colar no **certificado x. 509** caixa de texto. 

    d.Click **guardar alterações**

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-an-evernote-test-user"></a>Criar um utilizador de teste Evernote

Para permitir que os utilizadores do Azure AD sessão Evernote, têm de ser aprovisionados para Evernote.  
No caso de Evernote, o aprovisionamento é uma tarefa manual.

**Aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Evernote como administrador.

2. Clique em de **consola de administração**.

    ![Consola de administração](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Do **consola de administração**, aceda a **'Adicionar utilizadores'**.

    ![TestUser adicionar](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. **Adicionar os membros da equipa** no **E-Mail** caixa de texto, escreva o endereço de e-mail da conta de utilizador e clique em **convidar.**

    ![TestUser adicionar](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. Depois do convite é enviada, o titular da conta do Azure Active Directory irão receber um e-mail para aceitar o convite.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Evernote.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Evernote, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Evernote**.

    ![A ligação de Evernote na lista de aplicações](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Evernote no painel de acesso, deve obter com sessão iniciada para a aplicação de Evernote. Será possível iniciar sessão como uma organização de conta, mas, em seguida, tem de iniciar sessão com a sua conta pessoal. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

