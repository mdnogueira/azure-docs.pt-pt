---
title: "Tutorial: Integração do Azure Active Directory com Syncplicity | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Syncplicity."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 1321fa71bcd625d6ea754432bfb402d3919e38f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Tutorial: Integração do Azure Active Directory com Syncplicity

Neste tutorial, irá aprender a integrar Syncplicity com o Azure Active Directory (Azure AD).

Integrar Syncplicity com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Syncplicity
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Syncplicity (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Syncplicity, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Syncplicity-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Syncplicity a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionar Syncplicity a partir da Galeria
Para configurar a integração de Syncplicity com o Azure AD, terá de adicionar Syncplicity a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Syncplicity a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Syncplicity**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_search.png)

5. No painel de resultados, selecione **Syncplicity**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Syncplicity com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Syncplicity é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Syncplicity tem de ser estabelecida.

No Syncplicity, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Syncplicity, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Syncplicity](#creating-a-syncplicity-test-user)**  - para ter um homólogo de Britta Simon Syncplicity que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Syncplicity.

**Para configurar o Azure AD-início de sessão único com Syncplicity, execute os seguintes passos:**

1. No portal do Azure, no **Syncplicity** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

3. No **Syncplicity domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.syncplicity.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente Syncplicity](https://www.syncplicity.com/contact-us) para obter estes valores. 
 

4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-syncplicity-tutorial/tutorial_general_400.png)

6. No **Syncplicity configuração** secção, clique em **configurar Syncplicity** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_configure.png) 

7. Inicie sessão no seu **Syncplicity** inquilino.

8. No menu na parte superior, clique em **admin**, selecione **definições**e, em seguida, clique em **domínio personalizado e o início de sessão único**.
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769545.png "Syncplicity")

9. No **único Sign-On (SSO)** diálogo página, execute os seguintes passos:
   
    ![De sessão único- \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. No **domínio personalizado** caixa de texto, escreva o nome do seu domínio.
  
    b. Selecione **ativada** como **Single Sign-On estado**.

    c. No **Id de entidade** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.

    d. No **URL de página de início de sessão** caixa de texto, cole o **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.

    e. No **terminar o URL da página** caixa de texto, cole o **Sign-Out URL** que copiou do portal do Azure.

    f. No **certificado do fornecedor de identidade**, clique em **Escolher ficheiro**e, em seguida, carregue o certificado que transferiu do portal do Azure. 

    g. Clique em **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-syncplicity-test-user"></a>Criar um utilizador de teste Syncplicity
Para os utilizadores AAD conseguir iniciar sessão, têm de ser aprovisionados para aplicação Syncplicity. Esta secção descreve como criar contas de utilizador do AAD no Syncplicity.

**Para Aprovisionar uma conta de utilizador para Syncplicity, execute os seguintes passos:**

1. Inicie sessão no seu **Syncplicity** inquilino (por exemplo: `https://company.Syncplicity.com`).

2. Clique em **admin** e selecione **contas de utilizador**.

3. Clique em **adicionar um utilizador**.
   
    ![Gerir utilizadores](./media/active-directory-saas-syncplicity-tutorial/ic769764.png "gerir utilizadores")

4. Tipo de **endereços de correio eletrónico** de uma conta do AAD que pretende aprovisionar, selecione **utilizador** como **função**e, em seguida, clique em **seguinte**.
   
    ![Informações de conta](./media/active-directory-saas-syncplicity-tutorial/ic769765.png "informações de conta")
   
    >[!NOTE]
    >O marcador de posição de conta do AAD obtém uma mensagem de e-mail, incluindo uma ligação para confirmar e ativar a conta. 
    > 

5. Selecione um grupo na sua empresa que o novo utilizador deve ser membro de e, em seguida, clique em **seguinte**.
   
    ![Associação ao grupo](./media/active-directory-saas-syncplicity-tutorial/ic769772.png "associação ao grupo")
   
    >[!NOTE]
    >Se não existem não existem grupos listados, clique em **seguinte**. 
    > 

6. Selecione as pastas que pretende colocar sob controlo da Syncplicity no computador do utilizador e, em seguida, clique em **seguinte**.
   
    ![Pastas de Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769773.png "Syncplicity pastas")

>[!NOTE]
>Pode utilizar quaisquer outras Syncplicity utilizador conta criação ferramentas ou APIs fornecidas pelo Syncplicity para aprovisionar contas de utilizador do AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Syncplicity.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Syncplicity, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Syncplicity**.

    ![Configurar o início de sessão único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Syncplicity no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Syncplicity.
## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_203.png

