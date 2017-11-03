---
title: "Tutorial: Integração do Azure Active Directory com ajudar Scout | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e ajudar Scout."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: fe8775bd39173b2adf1f82d32f5e851ef1c19931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: Integração do Azure Active Directory com Scout ajudar

Neste tutorial, irá aprender a integrar Scout ajudar com o Azure Active Directory (Azure AD).

Integrar Scout ajudar com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Scout ajudar.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para ajudar a Scout (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Scout ajudar, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Scout ajudar-início de sessão único subscrição ativada

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Scout ajudar a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-help-scout-from-the-gallery"></a>Adicionar Scout ajudar a partir da Galeria
Para configurar a integração de Scout ajudar com o Azure AD, tem de adicionar Scout ajudar a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Scout ajudar a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **ajudar Scout**, selecione **ajudar Scout** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Ajuda Scout na lista de resultados](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Scout ajudar com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no ajudar Scout for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ajudar Scout tem de ser estabelecida.

Ajuda Scout utiliza endereços de correio eletrónico para inícios de sessão, por isso, para estabelecer a relação de ligação, utilize o mesmo **endereço de correio eletrónico** como **nome de utilizador** no Azure AD.

Para configurar e testar o Azure AD-início de sessão único com Scout ajudar, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de ajudar Scout](#create-a-help-scout-test-user)**  - para ter um homólogo de Britta Simon ajudar Scout que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Scout ajudar.

**Para configurar o Azure AD-início de sessão único com Scout ajudar, execute os seguintes passos:**

1. No portal do Azure, no **ajudar Scout** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. No **URLs e de domínio de Scout ajudar** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Ajudar os URLs e Scout domínio único início de sessão informações](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Identificador** é o **"Público-alvo URI (ID de entidade do fornecedor de serviço)"** de ajudar Scout, começa com`urn:`

    b. **URL de resposta** é o **"URL de pós-back (asserção consumidor o URL do serviço)"** de ajudar Scout, começa com`https://` 

    > [!NOTE] 
    > Os valores existentes nestes URLs são para a demonstração apenas. Terá de atualizar estes valores do URL de resposta real e o identificador. Pode obter estes valores do **Single Sign-On** separador na secção de autenticação, o que é explicada mais tarde no tutorial.

4. Se pretender configurar a aplicação no **SP** modo iniciado, verifique **Mostrar avançadas definições de URL** e executar o passo seguinte:

    ![Ajudar os URLs e Scout domínio único início de sessão informações](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    No **URL de início de sessão** caixa de texto, escreva um URL como:`https://secure.helpscout.net/members/login/`
     
5. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. No **ajudar a configuração de Scout** secção, clique em **Scout de ajudar a configurar** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida**.

    ![Configurar o início de sessão único](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. Numa janela do browser web diferente, inicie sessão no site da sua empresa ajudar Scout como administrador.

9. Depois de a sessão iniciada no, clique em **"Gerir"** no menu superior e, em seguida, selecione **"Empresa"** no menu de lista pendente.

    ![Configurar o início de sessão único](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. Selecione **"Autenticação"** no menu esquerdo. 

    ![Configurar o início de sessão único](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. Isto leva-o para a secção de definições de SAML e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. Copiar o **URL de reposição (URL do serviço do consumidor de asserção)** valor e cole o valor no **URL de resposta** caixa no portal do Azure, em ajudar Scout **domínios e URLs** secção.
    
    b. Copiar o **URI de audiência (ID de entidade do fornecedor de serviço)** valor e cole o valor no **identificador** caixa no portal do Azure, em ajudar Scout **domínios e URLs** secção.

12. Ativar/desativar **ativar SAML** no e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. No **URL de início de sessão único** caixa de texto, cole o valor de **URL Single Sign-On serviço**, que copiou do portal do Azure.
    
    b. Clique em **carregar certificado** para carregar o **Certificate(Base64)** transferido a partir do portal do Azure.

    c. Introduza a e-mail domínios e.x. - sua organização `contoso.com` no **domínios de E-Mail** caixa de texto. Pode separar vários domínios com uma vírgula. Em qualquer altura um utilizador de Scout ajudar ou administrador que introduz esse domínio específico no [página de início de sessão ajudar Scout](https://secure.helpscout.net/members/login/) serão encaminhados para o fornecedor de identidade para autenticar com as respetivas credenciais.

    d. Por último, pode alternar **SAML impor o início de sessão** se pretende que os utilizadores apenas iniciar sessão para ajudar a Scout através de através deste método. Se ainda pretende deixar a opção iniciar sessão com as respetivas credenciais de ajudar Scout serem, pode deixar a alternada desativado. Mesmo se esta opção estiver ativada, um proprietário da conta será sempre conseguirão iniciar sessão no Scout ajudar com a palavra-passe de conta.

    e. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-help-scout-test-user"></a>Criar um utilizador de teste Scout ajudar

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon Scout ajudar. Ajuda Scout suporta o aprovisionamento de just-in-time, que é por predefinição ativada.

Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe na Scout ajudar, uma nova é criada quando tentar aceder à ajuda Scout.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para ajudar a Scout.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon para ajudar a Scout, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **ajudar Scout**.

    ![A ligação de ajuda Scout na lista de aplicações](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Scout ajudar no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de ajudar Scout.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png

