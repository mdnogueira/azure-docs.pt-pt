---
title: "Tutorial: Integração do Azure Active Directory com Clarizen | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Clarizen."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: 574c6877bddac8be7d6d541bfabbdc10f6be3101
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Tutorial: Integração do Azure Active Directory com Clarizen

Neste tutorial, irá aprender a integração do Azure Active Directory (Azure AD) com Clarizen. Esta integração proporciona as seguintes vantagens:

- Pode controlar, no Azure AD, quem tem acesso às Clarizen.
- Pode permitir que os utilizadores ser iniciada automaticamente para Clarizen (-início de sessão único) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central, o portal do Azure.

O cenário deste tutorial consiste em duas tarefas principais:

1. Adicione Clarizen a partir da galeria.
2. Configurar e testar o Azure AD-início de sessão único.

Se pretender obter mais detalhes sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, veja [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com Clarizen, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma subscrição de Clarizen está ativada para o início de sessão único

Para testar os passos neste tutorial, siga estas recomendações:

- Teste do Azure AD-início de sessão único num ambiente de teste. Não utilize o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de teste do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Adicionar Clarizen a partir da Galeria
Para configurar a integração de Clarizen com o Azure AD, adicione Clarizen na Galeria à sua lista de aplicações SaaS geridas.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, clique em de **do Azure Active Directory** ícone.

    ![Ícone do Active Directory do Azure][1]

2. Clique em **aplicações empresariais**. Em seguida, clique em **todas as aplicações**.

    ![Ao clicar em "As aplicações empresariais" e "Todas as aplicações"][2]

3. Clique em de **adicionar** botão na parte superior da caixa de diálogo.

    ![O botão "Adicionar"][3]

4. Na caixa de pesquisa, escreva **Clarizen**.

    ![Escrever "Clarizen" na caixa de pesquisa](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. No painel de resultados, selecione **Clarizen**e, em seguida, clique em **adicionar** para adicionar a aplicação.

    ![Selecionar Clarizen no painel de resultados](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nas secções seguintes, configure e teste do Azure AD-início de sessão único com Clarizen com base no utilizador de teste Britta Simon.

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Clarizen é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Clarizen tem de ser estabelecida. Estabelecer esta relação de ligação ao atribuir o valor de **nome de utilizador** no Azure AD como o valor do **Username** no Clarizen.

Para configurar e testar o Azure AD-início de sessão único com Clarizen, conclua os blocos modulares seguintes:

1. **[Configurar o Azure AD-início de sessão único](#configure-azure-ad-single-sign-on)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar do Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Clarizen](#create-a-clarizen-test-user)**  para ter um homólogo de Britta Simon no Clarizen que está ligada a representação do Azure AD da forma.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único
Ativar o Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Clarizen.

1. No portal do Azure, no **Clarizen** página de integração de aplicações, clique em **de sessão único-**.

    ![Ao clicar em "Single sign-on"][4]

2. No **de sessão único-** caixa de diálogo, para **modo**, selecione **baseados em SAML início de sessão** para ativar o início de sessão único.

    ![Selecionar "baseados em SAML início de sessão"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. No **Clarizen domínio e os URLs** secção, execute os seguintes passos:

    ![Caixas de URL de identificador e resposta](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. No **identificador** caixa, digite o valor como: **Clarizen**

    b. No **URL de resposta** caixa, escreva um URL, utilizando o padrão do seguinte: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Estes não são os valores reais. Tem de utilizar o identificador real e URL de resposta. Aqui sugerimos que utilize o valor exclusivo de uma cadeia como o identificador. Para obter os valores reais, contacte o [equipa de suporte de Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. No **certificado de assinatura de SAML** secção, clique em **criar novo certificado**.

    ![Clicar em "Criar o novo certificado"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)  

5. No **criar o novo certificado** diálogo caixa, clique no ícone de calendário e selecione uma data de expiração. Em seguida, clique em **Guardar**.

    ![Selecionar e guardar uma data de expiração](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. No **certificado de assinatura de SAML** secção, selecione **tornar o novo certificado ativa**e, em seguida, clique em **guardar**.

    ![Selecionar a caixa de verificação para efetuar o novo certificado Active Directory](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. No **o certificado de Rollover** caixa de diálogo, clique em **OK**.

    ![Ao clicar em "OK" para confirmar que pretende tornar o certificado ativa](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Ao clicar em "Certificate (Base64)" para iniciar a transferência](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. No **Clarizen configuração** secção, clique em **configurar Clarizen** para abrir o **configurar início de sessão** janela.

    ![Clicar em "Configurar Clarizen"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    ![Janela "Configurar o início de sessão", incluindo URLs e ficheiros](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. Numa janela do browser web diferente, inicie sessão no site da sua empresa Clarizen como administrador.

11. Clique em seu nome de utilizador e, em seguida, clique em **definições**.

    ![Ao clicar em "Definições" em seu nome de utilizador](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "definições")

12. Clique em de **definições globais** separador. Em seguida, junto a **autenticação federada**, clique em **editar**.

    ![Separador "Definições globais"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "definições globais")

13. No **autenticação federada** diálogo caixa, execute os seguintes passos:

    ![Caixa de diálogo "Autenticação federada"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "autenticação federada")

    a. Selecione **ativar federado autenticação**.

    b. Clique em **carregar** para carregar o certificado transferido.

    c. No **URL de início de sessão** box, introduza o valor de **único início de sessão no URL do serviço SAML** na janela de configuração de aplicação do Azure AD.

    d. No **Sign-out URL** box, introduza o valor de **Sign-Out URL** na janela de configuração de aplicação do Azure AD.

    e. Selecione **utilizar POST**.

    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
No portal do Azure, crie um utilizador de teste chamado Britta Simon.

![Nome e endereço de e-mail do utilizador de teste do Azure AD][100]

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** ícone.

    ![Ícone do Active Directory do Azure](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. Clique em **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores** para apresentar a lista de utilizadores.

    ![Ao clicar em "Utilizadores e grupos" e "Todos os utilizadores"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.

    ![O botão "Adicionar"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![Caixa de diálogo "Utilizador" com o nome, endereço de correio eletrónico e palavra-passe preenchidos](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail da conta Britta Simon.

    c. Selecione **mostrar palavra-passe** e anote o valor de **palavra-passe**.

    d. Clique em **Criar**.

### <a name="create-a-clarizen-test-user"></a>Criar um utilizador de teste Clarizen
Para ativar o Azure AD aos utilizadores iniciar sessão Clarizen, terá de aprovisionar contas de utilizador. No caso de Clarizen, o aprovisionamento é uma tarefa manual.

1. Inicie sessão no site da sua empresa Clarizen como administrador.

2. Clique em **pessoas**.

    ![Clicar em "As pessoas"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "pessoas")

3. Clique em **convidar utilizador**.

    ![Botão "Convidar utilizadores"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "convidar utilizadores")

4. No **convidar pessoas** diálogo caixa, execute os seguintes passos:

    ![Caixa de diálogo "Convidar pessoas"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "convidar pessoas")

    a. No **E-Mail** caixa, escreva o endereço de e-mail da conta Britta Simon.

    b. Clique em **convidar**.

    > [!NOTE]
    > O marcador de posição de conta do Azure Active Directory irá receber um e-mail e siga uma ligação para confirmar a respetiva conta para ficar ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD
Ative Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao Clarizen.

![Utilizador de teste atribuído][200]

1. Do Azure portal, abra as aplicações ver, navegue para a vista de diretório, clique em **aplicações empresariais**e, em seguida, clique em **todas as aplicações**.

    ![Ao clicar em "As aplicações empresariais" e "Todas as aplicações"][201]

2. Na lista de aplicações, selecione **Clarizen**.

    ![Selecionar Clarizen na lista](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. No painel esquerdo, clique em **utilizadores e grupos**.

    ![Ao clicar em "Utilizadores e grupos"][202]

4. Clique em de **adicionar** botão. Em seguida, no **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![O botão "Adicionar" e a caixa de diálogo "Atribuição adicionar"][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista de utilizadores.

6. No **utilizadores e grupos** caixa de diálogo, clique em de **selecione** botão.

7. No **adicionar atribuição** caixa de diálogo, clique em de **atribuir** botão.

### <a name="test-single-sign-on"></a>Teste o início de sessão único
Teste a configuração do Azure AD único início de sessão utilizando o painel de acesso.

Quando clica no mosaico Clarizen no painel de acesso, esta deve ser iniciada automaticamente aplicação Clarizen.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png
