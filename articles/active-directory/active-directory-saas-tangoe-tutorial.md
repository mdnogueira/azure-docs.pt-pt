---
title: "Tutorial: Integração do Azure Active Directory com Tangoe comando Premium Mobile | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Tangoe comando Premium Mobile."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 595541e7248a7486e58123927389c552af0e50f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Tutorial: Integração do Azure Active Directory com Tangoe comando Premium Mobile

Neste tutorial, irá aprender a integrar Tangoe comando Premium Mobile com o Azure Active Directory (Azure AD).

Integrar Tangoe comando Premium Mobile com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Tangoe comando Premium Mobile
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Tangoe comando Premium Mobile (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Tangoe comando Premium móveis, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Tangoe comando Premium Mobile-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Tangoe comando Premium Mobile a partir da Galeria
2. Configurar e testar o Azure AD-início de sessão único

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Adicionar Tangoe comando Premium Mobile a partir da Galeria
Para configurar a integração de Tangoe comando Premium Mobile com o Azure AD, tem de adicionar Tangoe comando Premium Mobile na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Tangoe comando Premium Mobile a partir da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Tangoe comando Premium Mobile**, selecione **Tangoe comando Premium Mobile** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Adicionar Tangoe comando Premium móveis a partir da Galeria ](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com Tangoe comando Premium móvel com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Tangoe comando Premium móvel é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Tangoe comando Premium Mobile tem de ser estabelecida.

No Tangoe comando Premium Mobile, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Tangoe comando Premium móveis, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Tangoe comando Premium Mobile](#create-a-tangoe-command-premium-mobile-test-user)**  - para ter um homólogo de Britta Simon Tangoe comando Premium móvel que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação móvel do Tangoe comando Premium.

**Para configurar o Azure AD-início de sessão único com Tangoe comando Premium Mobile, execute os seguintes passos:**

1. No portal do Azure, no **Tangoe comando Premium Mobile** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Baseados em SAML início de sessão](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_samlbase.png)

3. No **Tangoe comando Premium Mobile domínio e os URLs** secção, execute os seguintes passos:

    ![Comando Tangoe Premium móveis domínio e os URLs](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de resposta real e o URL de início de sessão. Contacte [equipa de suporte de cliente de Mobile Tangoe comando Premium](https://www.tangoe.com/contact-2/) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Secção de certificado de assinatura de SAML](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_certificate.png) 

5. Clique em **guardar** botão.

    ![botão Guardar](./media/active-directory-saas-tangoe-tutorial/tutorial_general_400.png)
    
6. No **Tangoe comando Premium Mobile configuração** secção, clique em **configurar Tangoe comando Premium Mobile** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Secção de configuração de móvel Tangoe comando Premium](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_configure.png) 

7. Para obter SSO configurado para a sua aplicação, contacte o seu [equipa de suporte de cliente de Mobile Tangoe comando Premium](https://www.tangoe.com/contact-2/) e fornecer o seguinte:

   - O ficheiro de metadados transferido
   - O **ID de entidade SAML**
   - O **URL do serviço de início de sessão único SAML**
   - O **URL de início de sessão**

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tangoe-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Utilizadores e grupos -> todos os utilizadores](./media/active-directory-saas-tangoe-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Adicionar utilizador](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Página de caixa de diálogo de utilizador](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Criar um utilizador de teste Tangoe comando Premium Mobile

Nesta secção, vai criar um utilizador chamado Britta Simon Tangoe comando Premium Mobile. 

Aplicação Tangoe comando Premium Mobile tem todos os utilizadores a aprovisionar na aplicação antes de efetuar início de sessão único. Por isso, consulte o trabalho com o [equipa de suporte de cliente de Mobile Tangoe comando Premium](https://www.tangoe.com/contact-2/) para aprovisionar estes utilizadores para a aplicação. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Tangoe comando Premium Mobile.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Tangoe comando Premium Mobile, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Tangoe comando Premium Mobile**.

    ![Tangoe comando Premium Mobile na lista de aplicações](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, pode testa a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico Tangoe comando Premium Mobile no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação móvel do Tangoe comando Premium. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png

