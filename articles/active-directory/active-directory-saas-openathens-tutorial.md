---
title: "Tutorial: Integração do Azure Active Directory com OpenAthens | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e OpenAthens."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 2819e300d94a0bc3b0900419218561fc846effcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Tutorial: Integração do Azure Active Directory com OpenAthens

Neste tutorial, irá aprender a integrar OpenAthens com o Azure Active Directory (Azure AD).

Integrar OpenAthens com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao OpenAthens.
- Pode permitir aos utilizadores iniciar sessão automaticamente para OpenAthens (-início de sessão único) com as respetivas contas do Azure AD.
- Pode gerir as contas numa única localização central, o portal do Azure.

Para obter mais informações sobre a integração de aplicações SaaS com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com OpenAthens, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um OpenAthens-início de sessão único ativada subscrição

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar OpenAthens a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-openathens-from-the-gallery"></a>Adicionar OpenAthens a partir da Galeria
Para configurar a integração de OpenAthens com o Azure AD, terá de adicionar OpenAthens a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar OpenAthens a partir da Galeria**

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue até à **aplicações empresariais**e, em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, selecione o **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **OpenAthens**, selecione **OpenAthens** no painel de resultados e, em seguida, selecione o **adicionar** botão.

    ![OpenAthens na lista de resultados](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com OpenAthens com base num utilizador de teste com o nome "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no OpenAthens é o utilizador no Azure AD. Por outras palavras, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no OpenAthens.

No OpenAthens, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com OpenAthens, tem de concluir os blocos modulares seguintes:

1. [Configurar o Azure AD-início de sessão único](#configure-azure-ad-single-sign-on), para permitir aos utilizadores utilizar esta funcionalidade.
2. [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user), para testar o Azure AD-início de sessão único com Britta Simon.
3. [Criar um utilizador de teste OpenAthens](#create-a-openathens-test-user), para ter um homólogo de Britta Simon OpenAthens que está ligada a representação do Azure AD do utilizador.
4. [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user), para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. [Teste o início de sessão único](#test-single-sign-on), para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação OpenAthens.

**Para configurar o Azure AD-início de sessão único com OpenAthens**

1. No portal do Azure, no **OpenAthens** página de integração de aplicações, selecione **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. Para ativar o início de sessão, no **de sessão único-** caixa de diálogo, selecione **baseados em SAML início de sessão** como o **modo**.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. No **OpenAthens domínio e os URLs** secção, introduza o valor `https://login.openathens.net/saml/2/metadata-sp` no **identificador** caixa de texto.

    ![Domínio OpenAthens e os URLs únicos de informações de início de sessão](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. No **certificado de assinatura de SAML** secção, selecione **XML de metadados**e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O certificado de assinatura de AMSL transferir ligação](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Selecione o botão **Guardar**.

    ![O início de sessão de gravação botão](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. Numa janela do browser web diferente, inicie sessão no site da sua empresa OpenAthens como administrador.

7. Selecione **ligações** da lista sob o **gestão** separador. 

    ![Configurar o início de sessão único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Selecione **SAML 1.1/2.0**e, em seguida, selecione o **configurar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. Para adicionar a configuração, selecione o **procurar** botão para carregar o ficheiro. XML de metadados do que transferiu a partir do portal do Azure e, em seguida, selecione **adicionar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Execute os seguintes passos no **detalhes** separador.

    ![Configurar o início de sessão único](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. No **mapeamento de nome de apresentação**, selecione **utilizar atributo**.

    b. No **atributo de nome de apresentação** texto, introduza o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. No **mapeamento de utilizador exclusivo**, selecione **utilizar atributo**.

    d. No **atributo de utilizador exclusivo** texto, introduza o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. No **estado**, selecione todas as três caixas de verificação.

    f. No **criar contas locais**, selecione **automaticamente**.

    g. Selecione **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação. Depois de adicionar esta aplicação a partir de **do Active Directory** > **aplicações empresariais** secção, selecione o **Single Sign-On** separador e aceder a incorporados documentação através de **configuração** secção na parte inferior. Para obter mais informações sobre a funcionalidade de documentação embedded, consulte o [do Azure AD incorporado documentação](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado "Britta Simon."

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD**

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**.

    ![O botão do Azure Active Directory](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, selecione **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. No **nome** caixa de texto, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, escreva o endereço de e-mail para Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa de texto.

    d. Selecione **Criar**.
  
### <a name="create-an-openathens-test-user"></a>Criar um utilizador de teste OpenAthens

OpenAthens suporta just-in-time o aprovisionamento e os utilizadores são criados automaticamente após a autenticação com êxito. Não precisa de executar qualquer acção nesta secção.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao OpenAthens.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a OpenAthens**

1. No portal do Azure, abra as aplicações ver, navegue para a vista de diretório e aceda a **aplicações empresariais**e, em seguida, selecione **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. No **aplicações** lista, selecione **OpenAthens**.

    ![A ligação de OpenAthens na lista de aplicações](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Selecione o **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** painel.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** lista, selecione **Britta Simon**.

6. Selecione o **selecione** clique no botão no **utilizadores e grupos** lista.

7. Selecione o **atribuir** clique no botão no **adicionar atribuição** painel.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, teste configuração do Azure AD único início de sessão utilizando o painel de acesso.

Quando seleciona o **OpenAthens** na peça de mosaico do painel de acesso, esta deve ser iniciada automaticamente aplicação OpenAthens.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* Para obter uma lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory, consulte [tutoriais de integração de aplicações de SaaS para utilização com o Azure AD](active-directory-saas-tutorial-list.md).
* Para obter mais informações sobre o acesso a aplicações e início de sessão no Azure Active Directory, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png

