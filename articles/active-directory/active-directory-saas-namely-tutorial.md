---
title: "Tutorial: Integração do Azure Active Directory com nomeadamente | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e nomeadamente."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 1d7e8fbcfc757853ab909bbb05522f3dc387715d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Tutorial: Integração do Azure Active Directory com nomeadamente

Neste tutorial, irá aprender nomeadamente integrar com o Azure Active Directory (Azure AD).

Nomeadamente integrar com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso nomeadamente
- Pode permitir aos utilizadores obter automaticamente com sessão iniciada para nomeadamente (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar o Azure AD integração com nomeadamente, que tem os seguintes itens:

- Uma subscrição do Azure AD
- Um nomeadamente-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar nomeadamente a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-namely-from-the-gallery"></a>Adicionar nomeadamente a partir da Galeria
Para configurar a integração de nomeadamente com o Azure AD, tem de adicionar nomeadamente na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar nomeadamente na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **nomeadamente**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_search.png)

5. No painel de resultados, selecione **nomeadamente**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com nomeadamente com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no nomeadamente for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no nomeadamente tem de ser estabelecida.

Nomeadamente, atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único consigo nomeadamente, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste nomeadamente](#creating-a-namely-test-user)**  - para ter um homólogo de Britta Simon no nomeadamente que está ligado a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação nomeadamente.

**Para configurar o Azure AD-início de sessão único com nomeadamente, execute os seguintes passos:**

1. No portal do Azure, no **nomeadamente** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_samlbase.png)

3. No **nomeadamente domínios e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.namely.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente nomeadamente](https://www.namely.com/contact/) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_general_400.png)

6. No **nomeadamente configuração** secção, clique em **configurar nomeadamente** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_configure.png) 

7. Na outra janela do browser, inicie sessão no seu site de empresa nomeadamente como administrador.

8. Na barra de ferramentas na parte superior, clique em **empresa**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) 

9. Clique no separador **Definições**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) 

10. Clique em **SAML**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) 

11. No **SAML definições** página, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png)
 
    a. Clique em **ativar SAML**. 

    b. No **url SSO de fornecedor de identidade** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.
    
    c. Abra o seu certificado transferido no bloco de notas, copie o conteúdo e, em seguida, cole-o para o **certificado do fornecedor de identidade** caixa de texto.
     
    d. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-namely-test-user"></a>Criar um utilizador de teste nomeadamente

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon nomeadamente.

**Para criar um utilizador chamado Britta Simon nomeadamente, execute os seguintes passos:**

1. Início de sessão para o site de empresa nomeadamente como administrador.

2. Na barra de ferramentas na parte superior, clique em **pessoas**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) 

3. Clique em de **diretório** separador.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) 

4. Clique em **Adicionar nova pessoa**.

    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_12.png)

5. No **Adicionar nova pessoa** caixa de diálogo, execute os seguintes passos:

    a. No **nome próprio** caixa de texto, tipo **Britta**.

    b. No **Apelido** caixa de texto, tipo **Simon**.

    c. No **E-Mail** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    d. Clique em **Guardar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso aos nomeadamente.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon para nomeadamente, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **nomeadamente**.

    ![Configurar o início de sessão único](./media/active-directory-saas-namely-tutorial/tutorial_namely_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Ao clicar no nomeadamente na peça de mosaico do painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação nomeadamente

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-namely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png

