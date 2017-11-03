---
title: "Tutorial: Integração do Azure Active Directory com etouches | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e etouches."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 3cd9e9d6aae924369065ca492b1f6380c0ddc5fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Tutorial: Integração do Azure Active Directory com etouches

Neste tutorial, irá aprender a integrar etouches com o Azure Active Directory (Azure AD).

Integrar etouches com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao etouches
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para etouches (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com etouches, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um etouches-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar etouches a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-etouches-from-the-gallery"></a>Adicionar etouches a partir da Galeria
Para configurar a integração de etouches com o Azure AD, terá de adicionar etouches a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar etouches a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **etouches**, selecione **etouches** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![etouches na lista de resultados](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com etouches com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no etouches é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no etouches tem de ser estabelecida.

No etouches, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com etouches, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste etouches](#create-an-etouches-test-user)**  - para ter um homólogo de Britta Simon no etouches que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação etouches.

**Para configurar o Azure AD-início de sessão único com etouches, execute os seguintes passos:**

1. No portal do Azure, no **etouches** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_samlbase.png)

3. No **etouches domínios e URLs** secção, execute os seguintes passos:

    ![etouches URLs e de domínio único informações de início de sessão](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize o valor com o início de sessão real no URL e o identificador, o que é explicada mais tarde no tutorial.
    > 

4. aplicação de etouches espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos do **atributo de utilizador** da aplicação. A seguinte captura de ecrã mostra um exemplo para este. 

    ![Atributo de utilizador](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_attribute.png) 

5. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributo token SAML, conforme mostrado na imagem e efetuar os seguintes passos:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |
    | E-mail | User.Mail |    
    
    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Adicionar atributo](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_04.png)

    ![Atributo caixa de diálogo Adicionar](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_05.png)

    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **OK**. 

6. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_certificate.png) 

7. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-etouches-tutorial/tutorial_general_400.png)

8. Para obter SSO configurado para a sua aplicação, execute os passos seguintes na aplicação etouches: 

    ![configuração de etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png) 

    a. Início de sessão para **etouches** aplicação utilizando os direitos de administrador.
   
    b. Vá para o **SAML** configuração.

    c. No **definições gerais** secção, abra o seu certificado transferido a partir do portal do Azure no bloco de notas, copie o conteúdo e, em seguida, cole-o a caixa de texto de metadados do IDP. 

    d. Clique em de **Guardar & permaneça** botão.
  
    e. Clique em de **metadados de atualização** botão na secção de metadados de SAML. 

    f. Este procedimento abre a página e realizar SSO. Depois do SSO está a funcionar, em seguida, pode configurar o nome de utilizador.

    g. No campo do nome de utilizador, selecione o **emailaddress** conforme mostrado na imagem abaixo. 

    h. Copiar o **SP ID da entidade** valor e cole-o para o **identificador** caixa de texto, o que está a ser **etouches domínios e URLs** secção no portal do Azure.

    posso. Cópia o **SSO URL / ACS** valor e cole-o para o **iniciar sessão no URL** caixa de texto, o que está a ser **etouches domínios e URLs** secção no portal do Azure.
   
> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/active-directory-saas-etouches-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-etouches-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![O botão de adição](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-an-etouches-test-user"></a>Criar um utilizador de teste etouches

Nesta secção, vai criar um utilizador chamado Britta Simon etouches. Trabalhar com [etouches cliente suportar equipa](https://www.etouches.com/event-software/support/customer-support/) para adicionar os utilizadores na plataforma etouches.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso aos etouches.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a etouches, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **etouches**.

    ![A ligação de etouches na lista de aplicações](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único


O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico etouches no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de etouches.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png

