---
title: "Tutorial: Integração do Azure Active Directory com Panorama9 | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Panorama9."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 934c0743464fd32398071aa3d07f7af76fdf7e3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integração do Azure Active Directory com Panorama9

Neste tutorial, irá aprender a integrar Panorama9 com o Azure Active Directory (Azure AD).

Integrar Panorama9 com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Panorama9
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Panorama9 (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Panorama9, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Panorama9-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Panorama9 a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-panorama9-from-the-gallery"></a>Adicionar Panorama9 a partir da Galeria
Para configurar a integração de Panorama9 com o Azure AD, terá de adicionar Panorama9 a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Panorama9 a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Panorama9**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_search.png)

5. No painel de resultados, selecione **Panorama9**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-

Nesta secção, configure e teste do Azure AD-início de sessão único com Panorama9 com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Panorama9 é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Panorama9 tem de ser estabelecida.

No Panorama9, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Panorama9, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Panorama9](#creating-a-panorama9-test-user)**  - para ter um homólogo de Britta Simon Panorama9 que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Panorama9.

**Para configurar o Azure AD-início de sessão único com Panorama9, execute os seguintes passos:**

1. No portal do Azure, no **Panorama9** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_samlbase.png)

3. No **Panorama9 domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL como:`https://dashboard.panorama9.com/saml/access/3262`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`http://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente Panorama9](https://support.panorama9.com) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, copie o **THUMBPRINT** valor do certificado.

    ![Configurar o início de sessão único](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-panorama9-tutorial/tutorial_general_400.png)

6. No **Panorama9 configuração** secção, clique em **configurar Panorama9** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_configure.png) 

5. Numa janela do browser web diferente, inicie sessão no site da sua empresa Panorama9 como administrador.

6. Na barra de ferramentas na parte superior, clique em **gerir**e, em seguida, clique em **extensões**.
   
   ![Extensões](./media/active-directory-saas-panorama9-tutorial/ic790023.png "extensões")
7. No **extensões** caixa de diálogo, clique em **Single Sign-On**.
   
   ![De sessão único-](./media/active-directory-saas-panorama9-tutorial/ic790024.png "Single Sign-On")
8. No **definições** secção, execute os seguintes passos:
   
   ![Definições](./media/active-directory-saas-panorama9-tutorial/ic790025.png "definições")
   
    a. No **URL do fornecedor de identidade** caixa de texto, cole o valor de **URL Single Sign-On serviço**, que copiou do portal do Azure.
   
    b. No **impressão digital do certificado** caixa de texto, cole o **Thumbprint** valor do certificado, o qual copiou a partir do portal do Azure.    
         
9. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-panorama9-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-panorama9-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-panorama9-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-panorama9-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-panorama9-test-user"></a>Criar um utilizador de teste Panorama9

Para permitir que os utilizadores do Azure AD sessão Panorama9, têm de ser aprovisionados para Panorama9.  

No caso de Panorama9, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Panorama9** site da empresa como administrador.

2. No menu na parte superior, clique em **gerir**e, em seguida, clique em **utilizadores**.
   
  ![Os utilizadores](./media/active-directory-saas-panorama9-tutorial/ic790027.png "utilizadores")

3. Na secção utilizadores, clique em  **+**  para adicionar novo utilizador.

 ![Os utilizadores](./media/active-directory-saas-panorama9-tutorial/ic790028.png "utilizadores")

4. Consulte a secção de dados do utilizador, escreva o endereço de e-mail de um utilizador válido do Azure Active Directory que pretende aprovisionar no **E-Mail** caixa de texto.

5. São fornecidos na secção utilizadores, clique em **guardar**.
   
> [!NOTE]
    > Titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue-se de uma ligação para confirmar a respetiva conta para ficar ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Panorama9.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Panorama9, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Panorama9**.

    ![Configurar o início de sessão único](./media/active-directory-saas-panorama9-tutorial/tutorial_panorama9_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Panorama9 no painel de acesso, deve obter automaticamente com sessão iniciada a aplicação de Panorama9.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-panorama9-tutorial/tutorial_general_203.png

