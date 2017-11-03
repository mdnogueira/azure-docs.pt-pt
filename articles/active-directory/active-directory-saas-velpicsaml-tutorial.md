---
title: "Tutorial: Integração do Azure Active Directory com o Velpic SAML | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Velpic SAML."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: 5325f3cca00167e6b7b687509ce43435447ad2f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Tutorial: Integração do Azure Active Directory com Velpic SAML

Neste tutorial, irá aprender a integrar Velpic SAML com o Azure Active Directory (Azure AD).

Integrar Velpic SAML com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Velpic SAML
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Velpic SAML (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal de gestão do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Velpic SAML, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Velpic SAML início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de Velpic SAML de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-velpic-saml-from-the-gallery"></a>A adição de Velpic SAML de galeria
Para configurar a integração de Velpic SAML com o Azure AD, tem de adicionar Velpic SAML na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Velpic SAML a partir da galeria, execute os seguintes passos:**

1. No  **[Azure Management Portal](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Velpic SAML**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_search.png)

5. No painel de resultados, selecione **Velpic SAML**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configura e teste do Azure AD-início de sessão único com Velpic SAML com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Velpic SAML é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Velpic SAML tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Velpic SAML.

Para configurar e testar o Azure AD-início de sessão único com Velpic SAML, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de Velpic SAML](#creating-a-velpic-saml-test-user)**  - para ter um homólogo de Britta Simon no SAML Velpic que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal de gestão do Azure e configurar o início de sessão único na sua aplicação Velpic SAML.

**Para configurar o Azure AD-início de sessão único com Velpic SAML, execute os seguintes passos:**

1. No portal de gestão do Azure, no **Velpic SAML** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, como **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

3. Introduza os detalhes no **Velpic SAML domínio e os URLs** secção -

    ![Configurar o início de sessão único](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. No **URL de início de sessão** caixa de texto, digite o valor como:`https://<sub-domain>.velpicsaml.net`

    b. No **identificador** caixa de texto, cole o **'Único URL de início de sessão'** valor`https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Tenha em atenção que o URL de início de sessão será fornecido pela equipa do Velpic SAML e o valor do identificador estará disponível quando configurar o plug-in do SSO no lado de Velpic SAML. Tem de copiar esse valor a partir da página da aplicação de Velpic SAML e cole-a aqui.

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_400.png)

6. Na secção de configuração de SAML Velpic, clique em Configurar SAML Velpic para abrir a janela de início de sessão de configurar. Copie o ID de entidade de SAML da secção de referência rápida.

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Velpic SAML como administrador.

8. Clique em **gerir** separador e aceda a **integração** secção onde tem de clicar em **plug-ins** botão para criar o novo plug-in para início de sessão.

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_1.png)

9. Clique em de **'Adicionar plug-in'** botão.
    
    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_2.png)

10. Clique em de **SAML** mosaico na página Adicionar plug-in.
    
    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_3.png)

11. Introduza o nome do novo plug-in do SAML e clique em de **'Add'** botão.

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_4.png)

12. Introduza os detalhes da seguinte forma:

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_5.png)

    a. No **nome** caixa de texto, escreva o nome do plug-in SAML.

    b. No **URL do emissor** caixa de texto, cole o **ID de entidade de SAML** que copiou do **configurar início de sessão** janela do portal do Azure.

    c. No **fornecedor de configuração de metadados** carregar o ficheiro XML de metadados que transferiu a partir do portal do Azure.

    d. Também pode optar por ativar SAML apenas no tempo de aprovisionamento, Ativando a **'Automática criar novos utilizadores'** caixa de verificação. Se um utilizador não existe na Velpic e este sinalizador não estiver ativado, o início de sessão a partir do Azure irá falhar. Se o sinalizador estiver ativado o utilizador será automaticamente aprovisionado para Velpic no momento de início de sessão. 

    e. Copiar o **num URL de início de sessão único** do texto caixa e cole-o no portal do Azure.
    
    f. Clique em **Guardar**.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal de gestão do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-velpicsaml-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Criar um utilizador de teste Velpic SAML

Este passo, normalmente, não é necessário dado que a aplicação suporta apenas no aprovisionamento de utilizadores de tempo. Se o aprovisionamento de utilizador automáticas não estiver ativado, em seguida, a criação manual do utilizador pode ser feita conforme descrito abaixo.

Inicie sessão no site da sua empresa Velpic SAML como administrador e execute os seguintes passos:
    
1. Clique no separador de gerir e aceda à secção de utilizadores e, em seguida, clique no botão novo para adicionar utilizadores.

    ![Adicionar utilizador](./media/active-directory-saas-velpicsaml-tutorial/velpic_7.png)

2. No **"Criar o novo utilizador"** diálogo página, execute os seguintes passos.

    ![utilizador](./media/active-directory-saas-velpicsaml-tutorial/velpic_8.png)
    
    a. No **nome próprio** caixa de texto, nome do tipo do primeiro do Britta Simon.

    b. No **Apelido** caixa de texto, escreva o último nome do Britta Simon.

    c. No **nome de utilizador** caixa de texto, escreva o nome de utilizador do Britta Simon.

    d. No **E-Mail** caixa de texto, escreva o endereço de correio eletrónico da conta de Britta Simon.

    e. O resto das informações é opcional, pode introduzir se for necessário.
    
    f. Clique em **GUARDAR**.  

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao Velpic SAML.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Velpic SAML, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Velpic SAML**.

    ![Configurar o início de sessão único](./media/active-directory-saas-velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

1. Quando clica no mosaico de Velpic SAML no painel de acesso, deve obter a página de início de sessão da aplicação Velpic SAML. Deverá ver o **'Iniciar sessão no Azure AD'** botão na página de início de sessão.

    ![Plug-in](./media/active-directory-saas-velpicsaml-tutorial/velpic_6.png)

2. Clique em de **'Iniciar sessão no Azure AD'** botão Iniciar sessão no Velpic utilizando a sua conta do Azure AD.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-velpicsaml-tutorial/tutorial_general_203.png

