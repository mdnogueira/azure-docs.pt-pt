---
title: "Tutorial: Integração do Azure Active Directory com QPrism | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e QPrism."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: jeedes
ms.openlocfilehash: a380e9c07710e0c79a44e036f9c5bd72a731fe40
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Tutorial: Integração do Azure Active Directory com QPrism

Neste tutorial, irá aprender a integrar QPrism com o Azure Active Directory (Azure AD).

Integrar QPrism com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao QPrism.
- Pode permitir aos utilizadores obter iniciada automaticamente para QPrism (-início de sessão único) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central: portal do Azure.

Para obter mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com QPrism, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um QPrism início de sessão único subscrição ativado

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar QPrism a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="add-qprism-from-the-gallery"></a>Adicionar QPrism a partir da Galeria
Para configurar a integração de QPrism com o Azure AD, terá de adicionar QPrism a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar QPrism a partir da galeria:**

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **do Azure Active Directory**. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar uma nova aplicação, na parte superior da caixa de diálogo, selecione **nova aplicação**.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **QPrism**e selecione **QPrism** partir do painel de resultados. Em seguida, clique em **adicionar** para adicionar a aplicação.

    ![QPrism na lista de resultados](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configurar e testar o Azure AD-início de sessão único com QPrism, com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber que o utilizador homólogo no QPrism for para um utilizador no Azure AD. Por outras palavras, tem de existir uma relação entre um utilizador do Azure AD e o utilizador relacionado no QPrism ligada.

Para estabelecer esta relação na QPrism, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username**.

Para configurar e testar o Azure AD-início de sessão único com QPrism, conclua os blocos modulares seguintes:

1. [Configurar o Azure AD-início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar esta funcionalidade.
2. [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) para testar do Azure AD-início de sessão único com Britta Simon.
3. [Criar um utilizador de teste QPrism](#create-a-qprism-test-user) para ter um homólogo de Britta Simon no QPrism que está ligado a representação do Azure AD do utilizador.
4. [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. [Teste o início de sessão único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, ativar o Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação QPrism.

1. No portal do Azure, no **QPrism** página de integração de aplicações, selecione **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. No **QPrism domínio e os URLs** secção, efetue o seguinte:

    ![Domínio QPrism e os URLs únicos de informações de início de sessão](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL que utiliza o padrão do seguinte:`https://<customer domain>.qmyzone.com/login`

    b. No **identificador** caixa de texto, escreva um URL que utiliza o padrão do seguinte:`https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualizar estes valores com o identificador real e início de sessão no URL. Contacte [equipa de suporte de cliente QPrism](mailto:qsupport-ce@quatrro.com) para obter estes valores. 

4. Para gerar o **metadados** URL, efetue o seguinte procedimento:

    a. Selecione **registos de aplicação**.
    
    ![Configurar os registos de aplicação de início de sessão único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appregistrations.png)
   
    b. Selecione **pontos finais** para abrir **pontos finais** caixa de diálogo.  
    
    ![Configurar o ponto final de início de sessão único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpointicon.png)

    c. Selecione o botão Copiar para copiar o **documento de METADADOS de Federação** URL e cole-o bloco de notas.
    
    ![Configurar o ponto final de início de sessão único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpoint.png)
     
    d. Agora, a página de propriedades do **QPrism**e copie o **ID da aplicação** utilizando **cópia**. Em seguida, cole-o bloco de notas.
 
    ![Configurar o ID da aplicação de início de sessão único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appid.png)

    e. Gerar o **URL de metadados** utilizando o padrão do seguinte:`<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

5. Selecione **Guardar**.

    ![Configurar o início de sessão de gravação botão](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. Para configurar o início de sessão único no **QPrism** lado, enviar o **URL de metadados** para o [equipa de suporte de QPrism](mailto:qsupport-ce@quatrro.com). Estes Certifique-se de que a SAML único início de sessão na ligação está corretamente em ambos os lados.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação. Depois de adicionar esta aplicação a partir de **do Active Directory** > **aplicações empresariais** secção, basta clicar no **Single Sign-On** separador e aceder a incorporados documentação através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD:**

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**.

    ![O botão do Azure Active Directory](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, na parte superior do **todos os utilizadores** caixa de diálogo, selecione **adicionar**.

    ![O botão de adição](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, efetue o seguinte:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Selecione **Criar**.
 
### <a name="create-a-qprism-test-user"></a>Criar um utilizador de teste QPrism

Nesta secção, vai criar um utilizador chamado Britta Simon QPrism. Trabalhar com o [equipa de suporte de QPrism](mailto:qsupport-ce@quatrro.com) para adicionar utilizadores na plataforma QPrism. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para QPrism.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a QPrism:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório. Aceda a **aplicações empresariais**e selecione **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **QPrism**.

    ![A ligação de QPrism na lista de aplicações](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Selecione **Adicionar**. Em seguida, em **adicionar atribuição**, selecione **utilizadores e grupos**.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** no **utilizadores** lista.

6. No **utilizadores e grupos** caixa de diálogo, selecione **selecione**.

7. Em **adicionar atribuição**, selecione **atribuir**.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, teste configuração do Azure AD único início de sessão utilizando o painel de acesso.

No painel de acesso, quando seleciona o mosaico QPrism, esta deve obter automaticamente iniciada à sua aplicação QPrism.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

