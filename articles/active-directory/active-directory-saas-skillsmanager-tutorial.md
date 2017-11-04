---
title: "Tutorial: Integração do Azure Active Directory com o Gestor de competências | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Gestor de competências."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dab8debd-3b7b-4656-9bf0-1963ad8fce05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: a6c7bc08c7258ae463902999cc6c73198d54ac66
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="tutorial-azure-active-directory-integration-with-skills-manager"></a>Tutorial: Integração do Azure Active Directory com o Gestor de competências

Neste tutorial, irá aprender a integrar competências gestor no Azure Active Directory (Azure AD).

Integrar as competências Manager com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Gestor de competências.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada no Gestor de competências (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Gestor de competências, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Gestor de competências-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de Gestor de competências de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-skills-manager-from-the-gallery"></a>A adição de Gestor de competências de galeria
Para configurar a integração do Gestor de competências de com o Azure AD, tem de adicionar Gestor de competências na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Gestor de competências de galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **competências Manager**, selecione **competências Manager** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Gestor de competências na lista de resultados](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com o Gestor de competências com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Gestor de competências é um utilizador no Azure AD. Por outras palavras, uma relação entre um utilizador do Azure AD e o utilizador relacionado no Gestor de competências de ligação tem de ser estabelecida.

No Gestor de competências, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o Gestor de competências, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Gestor de competências](#create-a-skills-manager-test-user)**  - para ter um homólogo de Britta Simon no Gestor de competências que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de competências Manager.

**Para configurar o Azure AD-início de sessão único com o Gestor de competências, execute os seguintes passos:**

1. No portal do Azure, no **competências Manager** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_samlbase.png)

3. No **URLs e de domínio do Gestor de competências** secção, execute os seguintes passos:

    ![Domínio do Gestor de competências e os URLs únicos de informações de início de sessão](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://subdomain.skills-manager.com/kennametal`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://subdomain.skills-manager.com/public/SamlLogin2.aspx`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador e o URL de resposta real. Contacte [equipa de suporte do Gestor de competências](https://www.ibm.com/support/uk/?lnk=msu_uk) para obter estes valores.
 
4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_400.png)

6. No **competências Gestor de configuração** secção, clique em **configurar Gestor de competências** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração do Gestor de competências](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_configure.png) 

7. Para configurar o início de sessão único em **competências Manager** lado, terá de enviar o transferido **Certificate(Base64)**, **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** para [equipa de suporte do Gestor de competências](https://www.ibm.com/support/uk/?lnk=msu_uk). Se definir esta definição para que a ligação de SAML SSO corretamente em ambos os lados.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-skillsmanager-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-skills-manager-test-user"></a>Criar um utilizador de teste do Gestor de competências

Nesta secção, vai criar um utilizador chamado Britta Simon no Gestor de competências. Trabalhar com [equipa de suporte do Gestor de competências](https://www.ibm.com/support/uk/?lnk=msu_uk) para adicionar os utilizadores na plataforma competências Manager. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar Azure-início de sessão único, concedendo acesso competências gestor.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon ao Gestor de competências, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **competências Manager**.

    ![A ligação do Gestor de competências na lista de aplicações](./media/active-directory-saas-skillsmanager-tutorial/tutorial_skillsmanager_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do Gestor de competências no painel de acesso, deve obter automaticamente com sessão iniciada para o Gestor de competências de aplicação.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-skillsmanager-tutorial/tutorial_general_203.png

