---
title: "Tutorial: Integração do Azure Active Directory com Slack | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Slack."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 5aca630b2077d3f7d4ce9273ee668ed6a5f9843d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Tutorial: Integração do Azure Active Directory com Slack

Neste tutorial, irá aprender a integrar Slack com o Azure Active Directory (Azure AD).

Integrar Slack com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Slack
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Slack (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Slack, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Slack-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Slack a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-slack-from-the-gallery"></a>Adicionar Slack a partir da Galeria
Para configurar a integração de Slack com o Azure AD, terá de adicionar Slack a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Slack a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Slack**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_search.png)

5. No painel de resultados, selecione **Slack**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Slack com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Slack é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Slack tem de ser estabelecida.

No Slack, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Slack, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Slack](#creating-a-slack-test-user)**  - para ter um homólogo de Britta Simon Slack que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Slack.

**Para configurar o Azure AD-início de sessão único com Slack, execute os seguintes passos:**

1. No portal do Azure, no **Slack** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-slack-tutorial/tutorial_slack_samlbase.png)

3. No **Slack domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-slack-tutorial/tutorial_slack_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.slack.com`

    b. No **identificador** caixa de texto, escreva o URL:`https://slack.com`

    > [!NOTE] 
    > O valor não é real. Tem de atualizar o valor com o início de sessão real no URL. Contacte [equipa de suporte Slack](https://slack.com/help/contact) para obter o valor
     
4. Aplicação slack espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos a partir de "**atributos de utilizador**" secção na página de integração de aplicações. A seguinte captura de ecrã mostra um exemplo para este.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute.png)

5. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, selecione **user.mail** como **identificador de utilizador** e para cada linha mostrada na tabela abaixo, execute os seguintes passos:
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | first_name | User.givenName |
    | last_name | User.Surname |
    | User.Email | User.Mail |  
    | User.Username | User.userPrincipalName |

    a. Clique em **atributo** para abrir **Editar atributo** diálogo caixa e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute1.png)

    a. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    b. Do **valor** lista, selecione o valor do atributo apresentado para essa linha.
    
    c. Clique em **OK**

6. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-slack-tutorial/tutorial_slack_certificate.png)

7. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

8. No **Slack configuração** secção, clique em **configurar Slack** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-slack-tutorial/tutorial_slack_configure.png) 

9.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Slack como administrador.

10.  Navegue para **Microsoft Azure AD** , em seguida, aceda a **definições Team**.

     ![Configurar o início de sessão único no lado de aplicação](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

11.  No **definições Team** secção, clique em de **autenticação** separador e, em seguida, clique em **alterar definições**.

     ![Configurar o início de sessão único no lado de aplicação](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

12. No **definições de autenticação SAML** caixa de diálogo, execute os seguintes passos:

    ![Configurar o início de sessão único no lado de aplicação](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  No **SAML 2.0 ponto final de protocolo HTTP ()** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.

    b.  No **emissor do fornecedor de identidade** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.

    c.  Abra o ficheiro de certificado transferido no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado público** caixa de texto.

    d. Configure as definições de três acima conforme adequado para a sua equipa Slack. Para obter mais informações sobre as definições, pode encontrar o **guia de configuração do Slack SSO** aqui. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Clique em **Guardar configuração**.
     
    <!-- Deselect **Allow users to change their email address**.

    e.  Select **Allow users to choose their own username**.

    f.  As **Authentication for your team must be used by**, select **It’s optional**. -->

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-slack-test-user"></a>Criar um utilizador de teste Slack

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon Slack. Slack suporta o aprovisionamento de just-in-time, que está por predefinição, ativada.

Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante a tentativa de aceder Slack se não existir ainda.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, terá de contactar o [equipa de suporte Slack](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao Slack.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon ao Slack, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Slack**.

    ![Configurar o início de sessão único](./media/active-directory-saas-slack-tutorial/tutorial_slack_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Slack no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação Slack.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png

