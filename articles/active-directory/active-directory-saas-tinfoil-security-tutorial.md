---
title: "Tutorial: Integração do Azure Active Directory com o TINFOIL SECURITY | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o TINFOIL SECURITY."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 614e4de3335574f4b56c7d641af4fcfafdb17d12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: Integração do Azure Active Directory com o TINFOIL SECURITY

Neste tutorial, irá aprender a integrar o TINFOIL SECURITY com o Azure Active Directory (Azure AD).

Integrar o TINFOIL SECURITY com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao TINFOIL SECURITY
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para TINFOIL SECURITY (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o TINFOIL SECURITY, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um TINFOIL SECURITY-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar o TINFOIL SECURITY a partir da Galeria
2. Configurar e testar o Azure AD-início de sessão único

## <a name="add-tinfoil-security-from-the-gallery"></a>Adicionar o TINFOIL SECURITY a partir da Galeria
Para configurar a integração do TINFOIL SECURITY com o Azure AD, tem de adicionar o TINFOIL SECURITY na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o TINFOIL SECURITY na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **TINFOIL SECURITY**, selecione **TINFOIL SECURITY** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![TINFOIL SECURITY da Galeria](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com o TINFOIL SECURITY com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no TINFOIL SECURITY é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TINFOIL SECURITY tem de ser estabelecida.

TINFOIL SECURITY, atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o TINFOIL SECURITY, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**  - para ter um homólogo de Britta Simon TINFOIL SECURITY que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação do TINFOIL SECURITY.

**Para configurar o Azure AD-início de sessão único com o TINFOIL SECURITY, execute os seguintes passos:**

1. No portal do Azure, no **TINFOIL SECURITY** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![SAML com base em início de sessão](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. No **URLs e de domínio de segurança do TINFOIL** secção, o utilizador não tem de efetuar quaisquer passos, tal como a aplicação já está pré-integrada com o Azure.

    ![Configurar o início de sessão único](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. No **certificado de assinatura de SAML** secção, copie o **THUMBPRINT** valor.

    ![Secção de certificado de assinatura de SAML](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. Para adicionar os mapeamentos de atributos necessários, execute os seguintes passos:
    
    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "atributos")
    
    | Nome do atributo    |   Valor do atributo |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Clique em **adicionar o atributo de utilizador**.
    
    ![Adicionar atributo](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "atributos")
    
    ![Adicionar atributo](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "atributos")
    
    b. No **nome de atributo** caixa de texto, tipo **accountid**.
    
    c. No **valor do atributo** caixa de texto, valor de colar o ID de conta que irá obter mais tarde no tutorial.
    
    d. Clique em **OK**.    

6. Clique em **guardar** botão.

    ![botão Guardar](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_400.png)

7. No **configuração de segurança do TINFOIL** secção, clique em **configurar TINFOIL SECURITY** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de segurança do TINFOIL](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. Numa janela do browser web diferente, inicie sessão no site da sua empresa TINFOIL SECURITY como administrador.

9. Na barra de ferramentas na parte superior, clique em **minha conta**.
   
    ![Dashboard](./media/active-directory-saas-tinfoil-security-tutorial/ic798971.png "Dashboard")

10. Clique em **segurança**.
   
    ![Segurança](./media/active-directory-saas-tinfoil-security-tutorial/ic798972.png "segurança")

11. No **Single Sign-On** configuração página, execute os seguintes passos:
   
    ![De sessão único-](./media/active-directory-saas-tinfoil-security-tutorial/ic798973.png "Single Sign-On")
   
    a. Selecione **ativar SAML**.
   
    b. Clique em **configuração Manual**.
   
    c. No **SAML Post URL** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure
   
    d. No **impressão digital do certificado de SAML** caixa de texto, cole o valor de **Thumbprint** que copiou do **certificado de assinatura de SAML** secção.
  
    e. Cópia **o ID de conta** valor e cole o valor no **valor do atributo** caixa de texto em **adicionar atributo** secção no portal do Azure.
   
    f. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Utilizadores e grupos -> todos os utilizadores ](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Utilizador](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Criar um utilizador de teste do TINFOIL SECURITY

Para permitir que os utilizadores do Azure AD sessão TINFOIL SECURITY, têm de ser aprovisionados para TINFOIL SECURITY. No caso do TINFOIL SECURITY, o aprovisionamento é uma tarefa manual.

**Para obter um utilizador aprovisionado, execute os seguintes passos:**

1. Se o utilizador faz parte de uma conta de empresa, terá de [contacte a equipa de suporte do TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) para obter a conta de utilizador que criou.

2. Se o utilizador for um utilizador normal do TINFOIL SaaS de segurança, em seguida, o utilizador pode adicionar um funcionário para qualquer um dos sites do utilizador. Isto aciona um processo para enviar um convite para o e-mail especificado para criar uma nova conta de utilizador do TINFOIL SECURITY.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador TINFOIL SECURITY ou APIs fornecidas pelos TINFOIL SECURITY para aprovisionar contas de utilizador do Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para o TINFOIL SECURITY.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a TINFOIL SECURITY, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **TINFOIL SECURITY**.

    ![Selecione o TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do TINFOIL SECURITY no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação do TINFOIL SECURITY. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_203.png

