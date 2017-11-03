---
title: "Tutorial: Integração do Azure Active Directory com o sistema de monitorização do SensoScientific sem fios temperatura | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e do sistema de monitorização do SensoScientific sem fios temperatura."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.openlocfilehash: fa6242cf7f9559ca394ffde2e5e734cb935b03dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Tutorial: Integração do Azure Active Directory com o sistema de monitorização do SensoScientific sem fios temperatura

Neste tutorial, irá aprender a integrar o sistema de monitorização do SensoScientific sem fios temperatura com o Azure Active Directory (Azure AD).

Integrar o sistema de monitorização do SensoScientific sem fios temperatura com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao sistema de monitorização do SensoScientific sem fios temperatura
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o sistema de monitorização do SensoScientific sem fios temperatura (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o sistema de monitorização do SensoScientific sem fios temperatura, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um sistema de monitorização do SensoScientific sem fios temperatura início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar SensoScientific sem fios temperatura monitorização sistema a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Adicionar SensoScientific sem fios temperatura monitorização sistema a partir da Galeria
Para configurar a integração do sistema de monitorização do SensoScientific sem fios temperatura com o Azure AD, tem de adicionar SensoScientific sem fios temperatura monitorização sistema na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o sistema de monitorização do SensoScientific sem fios temperatura na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **SensoScientific sem fios temperatura monitorização sistema**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

5. No painel de resultados, selecione **SensoScientific sem fios temperatura monitorização sistema**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com SensoScientific sem fios temperatura monitorização sistema com base num utilizador teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no sistema de monitorização do SensoScientific sem fios temperatura for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no sistema de monitorização do SensoScientific sem fios temperatura tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no sistema de monitorização do SensoScientific sem fios temperatura.

Para configurar e testar o Azure AD-início de sessão único com o sistema de monitorização do SensoScientific sem fios temperatura, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do sistema de monitorização do SensoScientific sem fios temperatura](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)**  - para ter um homólogo de Britta Simon SensoScientific sem fios temperatura monitorização sistema que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de sistema de monitorização do SensoScientific sem fios temperatura.

**Para configurar o Azure AD-início de sessão único com o sistema de monitorização do SensoScientific sem fios temperatura, execute os seguintes passos:**

1. No portal do Azure, no **SensoScientific sem fios temperatura monitorização sistema** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

3. No **URLs e de domínio de sistema de monitorização SensoScientific sem fios temperatura** secção, não é necessário para efetuar quaisquer passos dado que a aplicação já está previamente integrada com o Azure:

    ![Configurar o início de sessão único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_400.png)

6. No **configuração do sistema de monitorização SensoScientific sem fios temperatura** secção, clique em **configurar SensoScientific sem fios temperatura monitorização sistema** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, ID de entidade de SAML** e **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

7. Início de sessão para a aplicação de sistema de monitorização do SensoScientific sem fios temperatura como administrador.

8. No menu de navegação na parte superior, clique em **configuração** e goto **configurar** em **início de sessão único** para abrir o início de sessão único em definições.

    ![Configurar o início de sessão único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

9. No **início de sessão único em definições** formulário execute os seguintes passos:
 
    a. Selecione **nome do emissor** como do Azure AD.
    
    b. Colar o **ID de entidade de SAML** que copiou do portal do Azure para a caixa de texto do URL do emissor.
    
    c. Colar o **único início de sessão no URL do serviço SAML** que copiou do portal do Azure para a caixa de texto único URL de início de sessão no serviço.

    d. Colar o **Sign-Out URL** que copiou do portal do Azure para o URL do serviço Sign-Out única caixa de texto.

    e. Procure o certificado que transferiram a partir do portal do Azure e carregar aqui.
    
    f. Clique em **Guardar**.
  
> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Criar um utilizador de teste do sistema de monitorização do SensoScientific sem fios temperatura

Para ativar a utilizadores do Azure AD iniciar sessão no sistema de monitorização do SensoScientific sem fios temperatura, tem de ser aprovisionados no sistema de monitorização do SensoScientific sem fios temperatura. Trabalhar com [equipa de suporte do sistema de monitorização do SensoScientific sem fios temperatura](https://www.sensoscientific.com/contact-us/) para adicionar os utilizadores na plataforma SensoScientific sem fios temperatura monitorização do sistema. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao sistema de monitorização do SensoScientific sem fios temperatura.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon SensoScientific sem fios temperatura monitorização sistema, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **SensoScientific sem fios temperatura monitorização sistema**.

    ![Configurar o início de sessão único](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso. Clique no mosaico do sistema de monitorização do SensoScientific sem fios temperatura no painel de acesso, irão ser automaticamente com sessão iniciada para a aplicação de sistema de monitorização do SensoScientific sem fios temperatura. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_203.png

