---
title: "Tutorial: Integração do Azure Active Directory com BlueJeans | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e BlueJeans."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: jeedes
ms.openlocfilehash: 03bf65852b8d3cf14aebf155891a028db86e78d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Tutorial: Integração do Azure Active Directory com BlueJeans

Neste tutorial, irá aprender a integrar BlueJeans com o Azure Active Directory (Azure AD).

Integrar BlueJeans com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao BlueJeans
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para BlueJeans (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com BlueJeans, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um BlueJeans início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar BlueJeans a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-bluejeans-from-the-gallery"></a>Adicionar BlueJeans a partir da Galeria
Para configurar a integração de BlueJeans com o Azure AD, terá de adicionar BlueJeans a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar BlueJeans a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **BlueJeans**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_search.png)

5. No painel de resultados, selecione **BlueJeans**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com BlueJeans com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no BlueJeans é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no BlueJeans tem de ser estabelecida.

No BlueJeans, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com BlueJeans, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste BlueJeans](#creating-a-bluejeans-test-user)**  - para ter um homólogo de Britta Simon no BlueJeans que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação BlueJeans.

**Para configurar o Azure AD-início de sessão único com BlueJeans, execute os seguintes passos:**

1. No portal do Azure, no **BlueJeans** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

3. No **BlueJeans domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.BlueJeans.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.BlueJeans.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente BlueJeans](https://support.bluejeans.com/contact) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-bluejeans-tutorial/tutorial_general_400.png)

6. No **BlueJeans configuração** secção, clique em **configurar BlueJeans** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o URL de palavra-passe de alteração e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no seu **BlueJeans** site da empresa como administrador.

8. Aceda a **ADMIN \> definições de grupo \> segurança**.
   
   ![Administração](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")

9. No **segurança** secção, execute os seguintes passos:
   
   ![SAML início de sessão único](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML início de sessão único")   
   
   a. Selecione **SAML início de sessão único**.
  
   b. Selecione **ativar aprovisionamento automático**.

10. Passar com os seguintes passos:

    ![Caminho do certificado](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "caminho de certificado")
    
    a. Clique em **Escolher ficheiro**e, em seguida, carregue o certificado transferido.
   
    b. Colar **único início de sessão no URL do serviço SAML** para o **URL de início de sessão** caixa de texto.
   
    c. Colar **alterar palavra-passe URL** para o **URL de alteração de palavra-passe** caixa de texto.
   
    d. Colar **Sign-Out URL** para o **URL de fim de sessão** caixa de texto.

11. Passar com os seguintes passos:
    
    ![Guardar as alterações](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "guardar alterações")
    
    a. No **id de utilizador** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
   
    b. No **E-Mail** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
   
    c. Clique em **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-bluejeans-test-user"></a>Criar um utilizador de teste BlueJeans

Para permitir que os utilizadores do Azure AD iniciem sessão nos BlueJeans, têm de ser aprovisionados para BlueJeans.  

Em caso de BlueJeans, o aprovisionamento é uma tarefa manual.

**Aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **BlueJeans** site da empresa como administrador.

2. Aceda a **ADMIN \> gerir utilizadores \> adicionar utilizador**.
   
   ![Administração](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")
   
   >[!IMPORTANT]
   >O **adicionar utilizador** separador apenas está disponível se, no **separador segurança**, **ativar aprovisionamento automático** está desmarcada. 
   
3. No **adicionar utilizador** secção, execute os seguintes passos:

    ![Adicionar utilizador](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "adicionar utilizador")
    
    a. Escreva um **BlueJeans Username**, um **endereço de correio eletrónico**, um **BlueJeans reunião ID**, um **Moderator código de acesso**, um **nome completo**, a **empresa** de uma conta válida do AAD, pretende aprovisionar para caixas de texto relacionadas.
    
    b. Clique em **adicionar utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras BlueJeans utilizador conta criação ferramentas ou APIs fornecidas pelo BlueJeans para aprovisionar contas de utilizador do AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para BlueJeans.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a BlueJeans, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **BlueJeans**.

    ![Configurar o início de sessão único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico BlueJeans no painel de acesso, deve obter a página de início de sessão da aplicação BlueJeans.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_203.png

