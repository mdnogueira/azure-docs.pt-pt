---
title: "Tutorial: Integração do Azure Active Directory com SkyDesk E-Mail | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e SkyDesk E-Mail."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 0ffcca4161fc836192fc9c9871a905f36ea76b32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Tutorial: Integração do Azure Active Directory com SkyDesk E-Mail

Neste tutorial, irá aprender a integrar SkyDesk E-Mail com o Azure Active Directory (Azure AD).

Integrar SkyDesk E-Mail com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao E-Mail SkyDesk
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao E-Mail SkyDesk (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SkyDesk E-Mail, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um E-Mail SkyDesk-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui [oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar SkyDesk E-Mail a partir da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-skydesk-email-from-the-gallery"></a>Adicionar SkyDesk E-Mail a partir da galeria do
Para configurar a integração de SkyDesk E-Mail com o Azure AD, terá de adicionar SkyDesk E-Mail a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar SkyDesk E-Mail a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **SkyDesk E-Mail**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_search.png)

5. No painel de resultados, selecione **SkyDesk E-Mail**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configura e teste do Azure AD-início de sessão único com SkyDesk E-Mail com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no E-Mail de SkyDesk é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na mensagem de correio eletrónico SkyDesk tem de ser estabelecida.

No E-Mail de SkyDesk, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com SkyDesk E-Mail, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de E-Mail SkyDesk](#creating-a-skydesk-email-test-user)**  - para ter um homólogo de Britta Simon no E-Mail de SkyDesk que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de E-Mail SkyDesk.

**Para configurar o Azure AD-início de sessão único com SkyDesk E-Mail, execute os seguintes passos:**

1. No portal do Azure, no **SkyDesk E-Mail** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

3. No **URLs e de domínio de correio eletrónico SkyDesk** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de E-Mail SkyDesk](https://www.skydesk.sg/support/) para obter o valor. 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_400.png)

6. No **configuração do E-Mail SkyDesk** secção, clique em **configurar E-Mail de SkyDesk** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

7. Para ativar a SSO em **SkyDesk E-Mail**, execute os seguintes passos:

    a. Início de sessão para a conta de E-Mail SkyDesk como administrador.

    b. No menu na parte superior, clique em **configuração**e selecione **Org**. 
    
      ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. Clique em **domínios** do painel esquerdo.
    
      ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Clique em **Adicionar domínio**.
    
      ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Introduza o nome de domínio e, em seguida, verifique o domínio.
    
      ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Clique em **autenticação SAML** do painel esquerdo.
    
      ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)

8. No **autenticação SAML** diálogo página, execute os seguintes passos:
   
      ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >Para utilizar autenticação SAML com base, deve ter **domínio verificado** ou **portal URL** programa de configuração. Pode definir o portal de URL com o nome exclusivo.
    > 
    > 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. No **URL de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.
   
    b. No **terminar** caixa de texto de URL, cole o valor de **Sign-Out URL**, que copiou do portal do Azure.

    c. **Altere o URL de palavra-passe** é opcional, por isso, pode deixar em branco.

    d. Clique em **obter chave a partir de um ficheiro** selecione o seu certificado transferido a partir do portal do Azure e, em seguida, clique em **abra** para carregar o certificado.

    e. Como **algoritmo**, selecione **RSA**.

    f. Clique em **Ok** para guardar as alterações.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-skydesk-email-test-user"></a>Criar um utilizador de teste SkyDesk E-Mail

Nesta secção, vai criar um utilizador chamado Britta Simon no E-Mail de SkyDesk.

1. Clique em **acesso de utilizador** da esquerda painel no E-Mail de SkyDesk e, em seguida, introduza o nome de utilizador. 

    ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Se precisar de criar utilizadores em volume, terá de contactar o [equipa de suporte de cliente de E-Mail SkyDesk](https://www.skydesk.sg/support/).


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao E-Mail SkyDesk.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon ao E-Mail SkyDesk, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **SkyDesk E-Mail**.

    ![Configurar o início de sessão único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico de E-Mail SkyDesk no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de E-Mail SkyDesk.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png

