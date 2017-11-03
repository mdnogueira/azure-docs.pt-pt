---
title: "Tutorial: Integração do Azure Active Directory com StatusPage | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e StatusPage."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: fa16cdec7b89404c140435fe57d5aa4b08cfa985
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutorial: Integração do Azure Active Directory com StatusPage

Neste tutorial, irá aprender a integrar StatusPage com o Azure Active Directory (Azure AD).

Integrar StatusPage com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao StatusPage
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para StatusPage (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com StatusPage, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um StatusPage-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar StatusPage a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-statuspage-from-the-gallery"></a>Adicionar StatusPage a partir da Galeria
Para configurar a integração de StatusPage com o Azure AD, terá de adicionar StatusPage a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar StatusPage a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **StatusPage**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_search.png)

5. No painel de resultados, selecione **StatusPage**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com StatusPage com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no StatusPage é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no StatusPage tem de ser estabelecida.

No StatusPage, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com StatusPage, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste StatusPage](#creating-a-statuspage-test-user)**  - para ter um homólogo de Britta Simon StatusPage que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação StatusPage.

**Para configurar o Azure AD-início de sessão único com StatusPage, execute os seguintes passos:**

1. No portal do Azure, no **StatusPage** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_samlbase.png)

3. No **StatusPage domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte: 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Contacte a equipa de suporte de StatusPage, [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)para pedir os metadados necessários para configurar o início de sessão único. 
    >
    >a. Dos metadados, copie o valor de emissor e, em seguida, cole-o para o **identificador** caixa de texto.
    >
    >b. Dos metadados, copie o URL de resposta e, em seguida, cole-o para o **URL de resposta** caixa de texto.

4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_general_400.png)

6. No **StatusPage configuração** secção, clique em **configurar StatusPage** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_configure.png) 

7. Na outra janela do browser, inicie sessão site da sua empresa StatusPage como administrador.

8. Na barra de ferramentas principal, clique em **gerir conta**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 

10. Clique em de **Single Sign-on** separador. 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 

11. Na página de configuração de SSO, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. No **URL de destino de SSO** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.

    b. Abra o seu certificado transferido no bloco de notas, copie o conteúdo e, em seguida, cole-o para o **certificado** caixa de texto. 

    c. Clique em **Guardar configuração**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-statuspage-test-user"></a>Criar um utilizador de teste StatusPage

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon StatusPage.

StatusPage suportam o aprovisionamento de just-in-time. Que já ativou-lo na [configurar do Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on).

**Para criar um utilizador chamado Britta Simon StatusPage, execute os seguintes passos:**

1. Início de sessão no site da sua empresa StatusPage como administrador.

2. No menu na parte superior, clique em **gerir conta**.

    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png)

3. Clique em de **os membros da equipa** separador. 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 

4. Clique em **membro do agrupamento de adicionar**. 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 

5. Tipo de **endereço de correio eletrónico**, **nome próprio**, e **Sur nome** de um utilizador válido que pretende aprovisionar num relacionados caixas de texto. 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 

6. Como **função**, escolha **cliente administrador**.

7. Clique em **criar conta**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para StatusPage.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a StatusPage, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **StatusPage**.

    ![Configurar o início de sessão único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico StatusPage no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de StatusPage.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png

