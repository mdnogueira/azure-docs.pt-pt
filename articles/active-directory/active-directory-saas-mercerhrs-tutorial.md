---
title: "Tutorial: Integração do Azure Active Directory com Mercer BenefitsCentral (MBC) | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Mercer BenefitsCentral (MBC)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3788b28c-49aa-4208-9acd-630362008e89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jeedes
ms.openlocfilehash: c577a0163af04bec5737a215e788c3fb92c653c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mercer-benefitscentral-mbc"></a>Tutorial: Integração do Azure Active Directory com Mercer BenefitsCentral (MBC)

Neste tutorial, irá aprender a integrar Mercer BenefitsCentral (MBC) com o Azure Active Directory (Azure AD).

Integrar Mercer BenefitsCentral (MBC) com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso Mercer BenefitsCentral (MBC).
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para Mercer BenefitsCentral (MBC) (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Mercer BenefitsCentral (MBC), terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Mercer BenefitsCentral (MBC)-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Mercer BenefitsCentral (MBC) a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-mercer-benefitscentral-mbc-from-the-gallery"></a>Adicionar Mercer BenefitsCentral (MBC) a partir da Galeria
Para configurar a integração de Mercer BenefitsCentral (MBC) com o Azure AD, terá de adicionar Mercer BenefitsCentral (MBC) a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Mercer BenefitsCentral (MBC) a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Mercer BenefitsCentral (MBC)**, selecione **Mercer BenefitsCentral (MBC)** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Mercer BenefitsCentral (MBC) na lista de resultados](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Mercer BenefitsCentral (MBC) com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Mercer BenefitsCentral (MBC) é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Mercer BenefitsCentral (MBC) tem de ser estabelecida.

No BenefitsCentral Mercer (MBC), atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Mercer BenefitsCentral (MBC), tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Mercer BenefitsCentral (MBC)](#create-a-mercer-benefitscentral-mbc-test-user)**  - para ter um homólogo de Britta Simon no Mercer BenefitsCentral (MBC) que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Mercer BenefitsCentral (MBC).

**Para configurar o Azure AD-início de sessão único com Mercer BenefitsCentral (MBC), execute os seguintes passos:**

1. No portal do Azure, no **Mercer BenefitsCentral (MBC)** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_samlbase.png)

3. No **domínio Mercer BenefitsCentral (MBC) e URLs** secção, execute os seguintes passos:

    ![Mercer BenefitsCentral (MBC) domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`stg.mercerhrs.com/saml2.0`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://ssous-stg.mercerhrs.com/SP2/Saml2AssertionConsumer.aspx`

    > [!NOTE] 
    > O valor de URL de resposta não é real. Atualize este valor com o URL de resposta real. Contacte [equipa de suporte de Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) para obter este valor.

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_400.png)

6. No **Mercer BenefitsCentral (MBC) configuração** secção, clique em **configurar Mercer BenefitsCentral (MBC)** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração do mercer BenefitsCentral (MBC)](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_configure.png) 

7. Para configurar o início de sessão único em **Mercer BenefitsCentral (MBC)** lado, terá de enviar o transferido **XML de metadados** e **único início de sessão no URL do serviço SAML** para [Equipa de suporte de mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html). Se definir esta definição para que a ligação de SAML SSO corretamente em ambos os lados.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-mercer-benefitscentral-mbc-test-user"></a>Criar um utilizador de teste Mercer BenefitsCentral (MBC)

Nesta secção, vai criar um utilizador chamado Britta Simon Mercer h. Trabalhar com [equipa de suporte de Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) para adicionar os utilizadores na plataforma Mercer h. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso Mercer BenefitsCentral (MBC).

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon Mercer BenefitsCentral (MBC), execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Mercer BenefitsCentral (MBC)**.

    ![A ligação de Mercer BenefitsCentral (MBC) na lista de aplicações](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Mercer BenefitsCentral (MBC) no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Mercer BenefitsCentral (MBC).
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_203.png

