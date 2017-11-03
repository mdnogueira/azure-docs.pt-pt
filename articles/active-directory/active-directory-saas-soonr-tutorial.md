---
title: "Tutorial: Integração do Azure Active Directory a área de trabalho Soonr | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Soonr área de trabalho."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.openlocfilehash: 39a82e581d9b1eee19b5be9a5aa8a425c150dcdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Tutorial: Integração do Azure Active Directory a área de trabalho Soonr

Neste tutorial, irá aprender a integrar Soonr área de trabalho com o Azure Active Directory (Azure AD).

Integrar Soonr área de trabalho com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à área de trabalho Soonr
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a área de trabalho Soonr (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Soonr área de trabalho, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma área de trabalho Soonr-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de Soonr área de trabalho de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-soonr-workplace-from-the-gallery"></a>A adição de Soonr área de trabalho de galeria
Para configurar a integração da área de trabalho Soonr com o Azure AD, tem de adicionar à área de trabalho Soonr na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Soonr área de trabalho a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **à área de trabalho Soonr**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_search.png)

5. No painel de resultados, selecione **à área de trabalho Soonr**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Soonr área de trabalho baseado na chamado "Britta Simon" um utilizador de teste.

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na área de trabalho Soonr é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na área de trabalho Soonr tem de ser estabelecida.

Na área de trabalho Soonr, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Soonr área de trabalho, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste da área de trabalho Soonr](#creating-a-soonr-workplace-test-user)**  - para ter um homólogo de Britta Simon Soonr área de trabalho que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Soonr área de trabalho.

**Para configurar o Azure AD-início de sessão único a área de trabalho Soonr, execute os seguintes passos:**

1. No portal do Azure, no **à área de trabalho Soonr** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_samlbase.png)

3. No **Soonr à área de trabalho domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<servername>.soonr.com/singlesignon/saml/metadata`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<servername>.soonr.com/singlesignon/saml/SSO`

4. No **Soonr à área de trabalho domínio e os URLs** secção, se pretender configurar a aplicação no **SP iniciada modo**, execute os seguintes passos:
    
    ![Configurar o início de sessão único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_url1.png)

    a. Clique em de **Mostrar avançadas definições de URL**.

    b. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<servername>.soonr.com/singlesignon/saml/SSO`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualizar estes valores com o identificador real, inicie sessão no URL e o URL de resposta. Contacte [equipa de suporte de área de trabalho Soonr](https://awp.autotask.net/help/) para obter estes valores.
 
5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-soonr-tutorial/tutorial_general_400.png)

7. No **Soonr à área de trabalho configuração** secção, clique em **configurar a área de trabalho de Soonr** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_configure.png) 

8. Para configurar o início de sessão único em **à área de trabalho Soonr** lado, terá de enviar o transferido **XML de metadados**, **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** para [equipa de suporte de área de trabalho Soonr](https://awp.autotask.net/help/). Se definir esta definição para que a ligação de SAML SSO corretamente em ambos os lados.

    >[!Note]
    >Se necessitar de assistência com a configuração de Autotask área de trabalho, consulte [nesta página](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obter assistência com a sua conta da área de trabalho.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-soonr-workplace-test-user"></a>Criar um utilizador de teste Soonr área de trabalho

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon na área de trabalho Soonr. Trabalhar com [equipa de suporte de área de trabalho Soonr](https://awp.autotask.net/help/) para criar um utilizador na plataforma. Pode aumentar o pedido de suporte com Soonr de <a href="https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsoonr.com%2FAWPHelp%2FContent%2F0_HOME%2FSupport_for_End_Clients.htm&data=01%7C01%7Cv-saikra%40microsoft.com%7Ccbb4367ab09b4dacaac408d3eebe3f42%7C72f988bf86f141af91ab2d7cd011db47%7C1&sdata=FB92qtE6m%2Fd8yox7AnL2f1h%2FGXwSkma9x9H8Pz0955M%3D&reserved=0/">aqui</a>.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à área de trabalho Soonr.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon à área de trabalho Soonr, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **à área de trabalho Soonr**.

    ![Configurar o início de sessão único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.  

Quando clica no mosaico Soonr área de trabalho no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Soonr área de trabalho.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_203.png

