---
title: "Tutorial: Integração do Azure Active Directory com Asana | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Asana."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeedes
ms.openlocfilehash: a2f0cecb93cc382bcfd710c44eb70f80ae67f9b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integração do Azure Active Directory com Asana

Neste tutorial, irá aprender a integrar Asana com o Azure Active Directory (Azure AD).

Integrar Asana com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Asana
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Asana (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Asana, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Asana início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Asana a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-asana-from-the-gallery"></a>Adicionar Asana a partir da Galeria
Para configurar a integração de Asana com o Azure AD, terá de adicionar Asana a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Asana a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Asana**, selecione **Asana** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Asana com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Asana é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Asana tem de ser estabelecida.

No Asana, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Asana, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Asana](#create-an-asana-test-user)**  - para ter um homólogo de Britta Simon Asana que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Asana.

**Para configurar o Azure AD-início de sessão único com Asana, execute os seguintes passos:**

1. No portal do Azure, no **Asana** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-asana-tutorial/tutorial_asana_samlbase.png)

3. No **Asana domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio Asana e os URLs únicos de informações de início de sessão](./media/active-directory-saas-asana-tutorial/tutorial_asana_url.png)

    a. No **URL de início de sessão** caixa de texto, o URL de tipo:`https://app.asana.com/`

    b. No **identificador** caixa de texto, o valor de tipo:`https://app.asana.com/`
 
4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-asana-tutorial/tutorial_asana_certificate.png)
    
5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-asana-tutorial/tutorial_general_400.png)

6. No **Asana configuração** secção, clique em **configurar Asana** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_configure.png) 

7. Numa janela do browser diferente, início de sessão na aplicação Asana. Para configurar o SSO no Asana, aceda as definições de área de trabalho ao clicar no nome da área de trabalho no canto superior direito do ecrã. Em seguida, clique em  **\<o nome da sua área de trabalho\> definições**. 
   
    ![Definições de sso Asana](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

8. No **definições de organização** janela, clique em **administração**. Em seguida, clique em **membros tem de iniciar sessão através de SAML** para ativar a configuração de SSO. A executar os seguintes passos:
   
    ![Configurar definições de organização de início de sessão único](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  

     a. No **URL de página de início de sessão** caixa de texto, cole o **único início de sessão no URL do serviço SAML**.

     b. Clique com o botão direito do rato no certificado transferido a partir do portal do Azure, em seguida, abra o ficheiro de certificado utilizando o bloco de notas ou no seu editor de texto preferido. Copie o conteúdo entre o início e o título de certificado do fim e cole-na **certificado x. 509** caixa de texto.

9. Clique em **Guardar**. Aceda a [Asana guia para configurar o SSO](https://asana.com/guide/help/premium/authentication#gl-saml) se necessitar de mais assistência.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/active-directory-saas-asana-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-asana-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![O botão de adição](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-an-asana-test-user"></a>Criar um utilizador de teste Asana

Nesta secção, vai criar um utilizador chamado Britta Simon Asana.

1. No **Asana**, vá para o **equipas** secção no painel esquerdo. Clique no botão de sinal de adição. 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. Escreva a mensagem de e-mail britta.simon@contoso.com na caixa de texto e, em seguida, selecione **convidar**.

3. Clique em **enviar o convite**. O novo utilizador irá receber uma mensagem de e-mail para a conta de e-mail. Ela será necessário criar e validar a conta.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Asana.

![Atribuir a função de utilizador][200]

**Para atribuir Britta Simon a Asana, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Asana**.

    ![A ligação de Asana na lista de aplicações](./media/active-directory-saas-asana-tutorial/tutorial_asana_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar o Azure AD-início de sessão único.

Aceda à página de início de sessão Asana. Na caixa de texto do endereço de correio eletrónico, introduza o endereço de e-mail britta.simon@contoso.com. Deixe a caixa de texto de palavra-passe em branco e, em seguida, clique em **início de sessão**. Será redirecionado para a página de início de sessão do Azure AD. Conclua as suas credenciais do Azure AD. Agora, são registadas no Asana.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-asana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
