---
title: "Tutorial: Integração do Azure Active Directory com Bpm'online | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Bpm'online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 052db91d-ccff-4098-8ae3-2f76eca90539
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: jeedes
ms.openlocfilehash: 52518cd76738f93162b3c1c8b76abff95f34fc2f
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bpmonline"></a>Tutorial: Integração do Azure Active Directory com Bpm'online

Neste tutorial, irá aprender a integrar Bpm'online com o Azure Active Directory (Azure AD).

Integrar Bpm'online com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Bpm'online.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Bpm'online (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Bpm'online, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Bpm'online início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Bpm'online a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-bpmonline-from-the-gallery"></a>Adicionar Bpm'online a partir da Galeria
Para configurar a integração de Bpm'online com o Azure AD, terá de adicionar Bpm'online a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Bpm'online a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Bpm'online**, selecione **Bpm'online** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Bpm'online na lista de resultados](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Bpm'online com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Bpm'online é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Bpm'online tem de ser estabelecida.

No Bpm'online, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Bpm'online, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Bpm'online](#create-a-bpmonline-test-user)**  - para ter um homólogo de Britta Simon Bpm'online que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Bpm'online.

**Para configurar o Azure AD-início de sessão único com Bpm'online, execute os seguintes passos:**

1. No portal do Azure, no **Bpm'online** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_samlbase.png)

3. No **Bpm'online domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Domínio Bpm'online e os URLs únicos de informações de início de sessão](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<client site name>.bpmonline.com/`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<client site name>.bpmonline.com/ServiceModel/AuthService.svc/SsoLogin`

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Domínio Bpm'online e os URLs únicos de informações de início de sessão](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_url1.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<client site name>.bpmonline.com/`
     
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [equipa de suporte de cliente Bpm'online](mailto:support@bpmonline.com) para obter estes valores. 

5. Para gerar o **metadados** url, execute os seguintes passos:

    a. Clique em **registos de aplicação**.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_appregistrations.png)
   
    b. Clique em **pontos finais** para abrir **pontos finais** caixa de diálogo.  
    
    ![Configurar o início de sessão único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_endpointicon.png)

    c. Clique no botão Copiar para copiar **documento de METADADOS de Federação** url e cole-o bloco de notas.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_endpoint.png)
     
    d. Agora, a página de propriedades do **Bpm'online** e copie o **ID da aplicação** utilizando **cópia** botão e cole-o bloco de notas.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_appid.png)

     e. Gerar o **URL de metadados** utilizando o padrão do seguinte:`<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-bpmonline-tutorial/tutorial_general_400.png)
    
8. Para configurar o início de sessão único em **Bpm'online** lado, terá de enviar o **URL de metadados** para [equipa de suporte de Bpm'online](mailto:support@bpmonline.com). Se definir esta definição para que a ligação de SAML SSO corretamente em ambos os lados.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-bpmonline-test-user"></a>Criar um utilizador de teste Bpm'online

Nesta secção, vai criar um utilizador chamado Britta Simon Bpm'online. Trabalhar com [equipa de suporte de Bpm'online](mailto:support@bpmonline.com) para adicionar os utilizadores na plataforma Bpm'online. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Bpm'online.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Bpm'online, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Bpm'online**.

    ![A ligação de Bpm'online na lista de aplicações](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Bpm'online no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Bpm'online.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_203.png

