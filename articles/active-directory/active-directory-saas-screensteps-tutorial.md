---
title: "Tutorial: Integração do Azure Active Directory com ScreenSteps | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e ScreenSteps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: b6ded8ba1adf03fdccbdb7573c09fae1857c8b16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integração do Azure Active Directory com ScreenSteps

Neste tutorial, irá aprender a integrar ScreenSteps com o Azure Active Directory (Azure AD).

Integrar ScreenSteps com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ScreenSteps.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para ScreenSteps (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ScreenSteps, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um ScreenSteps-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar ScreenSteps a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-screensteps-from-the-gallery"></a>Adicionar ScreenSteps a partir da Galeria
Para configurar a integração de ScreenSteps com o Azure AD, terá de adicionar ScreenSteps a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar ScreenSteps a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **ScreenSteps**, selecione **ScreenSteps** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![ScreenSteps na lista de resultados](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com ScreenSteps com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no ScreenSteps é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ScreenSteps tem de ser estabelecida.

No ScreenSteps, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com ScreenSteps, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste ScreenSteps](#create-a-screensteps-test-user)**  - para ter um homólogo de Britta Simon ScreenSteps que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação ScreenSteps.

**Para configurar o Azure AD-início de sessão único com ScreenSteps, execute os seguintes passos:**

1. No portal do Azure, no **ScreenSteps** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. No **ScreenSteps domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio ScreenSteps e os URLs únicos de informações de início de sessão](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o real início de sessão no URL, que é explicado posteriormente neste tutorial. 

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-screensteps-tutorial/tutorial_general_400.png)

6. No **ScreenSteps configuração** secção, clique em **configurar ScreenSteps** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa ScreenSteps como administrador.

8. Clique em **definições da conta**.

    ![Gestão de contas](./media/active-directory-saas-screensteps-tutorial/ic778523.png "gestão de contas")

9. Clique em **de sessão único-**.

    ![Autenticação remota](./media/active-directory-saas-screensteps-tutorial/ic778524.png "autenticação remota")

10. Clique em **criar o ponto final de início de sessão único**.

    ![Autenticação remota](./media/active-directory-saas-screensteps-tutorial/ic778525.png "autenticação remota")

11. No **ponto final de início de sessão único criar** secção, execute os seguintes passos:

    ![Criar um ponto final de autenticação](./media/active-directory-saas-screensteps-tutorial/ic778526.png "criar um ponto final de autenticação")
    
    a. No **título** caixa de texto, escreva um título.
    
    b. Do **modo** lista, selecione **SAML**.
    
    c. Clique em **Criar**.

12. **Editar** o novo ponto final.

    ![Editar endpoint](./media/active-directory-saas-screensteps-tutorial/ic778528.png "editar ponto final")

13. No **ponto final de início de sessão único editar** secção, execute os seguintes passos:

    ![Ponto final de autenticação remota](./media/active-directory-saas-screensteps-tutorial/ic778527.png "ponto final de autenticação remota")

    a. Clique em **carregamento novo ficheiro de certificado de SAML**e, em seguida, carregue o certificado que transferiu a partir do portal do Azure.
    
    b. Colar **único início de sessão no URL do serviço SAML** valor que copiou do portal do Azure para o **URL de início de sessão remoto** caixa de texto.
    
    c. Colar **Sign-Out URL** valor que copiou do portal do Azure para o **URL para terminar sessão** caixa de texto.
    
    d. Selecione um **grupo** para atribuir utilizadores a quando terem sido aprovisionados.
    
    e. Clique em **atualização**.

    f. Copiar o **SAML consumidor URL** para a área de transferência e cole ao **URL de início de sessão** textbox em **ScreenSteps domínio e os URLs** secção.
    
    g. Volte à **editar ponto final de início de sessão único**.
    
    h. Clique em de **predefinir para conta** botão para utilizar este ponto final para todos os utilizadores que inicie sessão no ScreenSteps. Em alternativa, pode clicar no **adicionar ao Site** botão para utilizar este ponto final para sites específicos de **ScreenSteps**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-screensteps-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-screensteps-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-screensteps-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-screensteps-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-screensteps-test-user"></a>Criar um utilizador de teste ScreenSteps

Nesta secção, vai criar um utilizador chamado Britta Simon ScreenSteps. Trabalhar com [equipa de suporte de cliente ScreenSteps](https://www.screensteps.com/contact) para adicionar os utilizadores na plataforma ScreenSteps. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para ScreenSteps.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a ScreenSteps, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **ScreenSteps**.

    ![A ligação de ScreenSteps na lista de aplicações](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico ScreenSteps no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de ScreenSteps.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_203.png

