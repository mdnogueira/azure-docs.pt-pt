---
title: "Tutorial: Integração do Azure Active Directory com DocuSign | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e DocuSign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 29c99fdf39d366df90abc070f7b836320935035c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Integração do Azure Active Directory com DocuSign

Neste tutorial, irá aprender a integrar DocuSign com o Azure Active Directory (Azure AD).

Integrar DocuSign com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao DocuSign
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para DocuSign (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com DocuSign, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um DocuSign-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar DocuSign a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-docusign-from-the-gallery"></a>Adicionar DocuSign a partir da Galeria
Para configurar a integração de DocuSign com o Azure AD, terá de adicionar DocuSign a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar DocuSign a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **DocuSign**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. No painel de resultados, selecione **DocuSign**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com DocuSign com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no DocuSign é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no DocuSign tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no DocuSign.

Para configurar e testar o Azure AD-início de sessão único com DocuSign, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste DocuSign](#creating-a-docusign-test-user)**  - para ter um homólogo de Britta Simon DocuSign que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação DocuSign.

**Para configurar o Azure AD-início de sessão único com DocuSign, execute os seguintes passos:**

1. No portal do Azure, no **DocuSign** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. No **certificado de assinatura de SAML** secção, clique em **certificado (Base 64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. No **DocuSign configuração** secção do portal do Azure, clique em **configurar DocuSign** para abrir a janela de início de sessão de configurar. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**
    
    ![Configurar o início de sessão único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. Numa janela do browser web diferente, inicie sessão no seu **portal de administração de DocuSign** como administrador.

6. No menu de navegação à esquerda, clique em **domínios**.
   
    ![Configurar o início de sessão único][51]

7. No painel direito, clique em **afirmação domínio**.
   
    ![Configurar o início de sessão único][52]

8. No **de afirmações de um domínio** caixa de diálogo, no **nome de domínio** caixa de texto, escreva o domínio da sua empresa e, em seguida, clique em **afirmação**. Certifique-se que verifique o domínio e o estado está ativo.
   
    ![Configurar o início de sessão único][53]

9. No menu no lado esquerdo, clique em **fornecedores de identidade**  
   
    ![Configurar o início de sessão único][54]
10. No painel direito, clique em **Adicionar fornecedor de identidade**. 
   
    ![Configurar o início de sessão único][55]

11. No **as definições do fornecedor de identidade** página, execute os seguintes passos:
   
    ![Configurar o início de sessão único][56]

    a. No **nome** caixa de texto, escreva um nome exclusivo para a sua configuração. Não utilize espaços.

    b. Colar **ID de entidade de SAML** para o **emissor do fornecedor de identidade** caixa de texto.

    c. Colar **único início de sessão no URL do serviço SAML** para o **URL de início de sessão do fornecedor de identidade** caixa de texto.

    d. Colar **Sign-Out URL** para o **URL de fim de sessão do fornecedor de identidade** caixa de texto.

    e. Selecione **assinar pedido AuthN**.

    f. Como **AuthN enviar pedido pelo**, selecione **POST**.

    g. Como **envio fim de sessão pedido pelo**, selecione **obter**.

12. No **mapeamento de atributos personalizado** secção, escolha o campo que pretende mapear com afirmações do Azure AD. Neste exemplo, o **emailaddress** mapeada com o valor da afirmação **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. É o nome de afirmação predefinido do Azure AD para afirmações de e-mail. 
   
    > [!NOTE]
    > Utilize as adequadas **identificador de utilizador** ao mapear o utilizador do Azure AD para o mapeamento de utilizador DocuSign. Selecione o campo adequado e introduza o valor adequado com base nas definições da sua organização.
          
    ![Configurar o início de sessão único][57]

13. No **certificado do fornecedor de identidade** secção, clique em **adicionar certificado**e, em seguida, carregue o certificado que transferiu a partir do portal do Azure AD.   
   
    ![Configurar o início de sessão único][58]

14. Clique em **Guardar**.

15. No **fornecedores de identidade** secção, clique em **ações**e, em seguida, clique em **pontos finais**.   
   
    ![Configurar o início de sessão único][59]
 
16. No **ver SAML 2.0 pontos finais** secção no **portal de administração de DocuSign**, execute os seguintes passos:
   
    ![Configurar o início de sessão único][60]
   
    a. Copiar o **URL do emissor de fornecedor de serviço**e, em seguida, cole o **identificador** caixa de texto no **DocuSign domínio e os URLs** secção a seguir o padrão do portal do Azure: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Copiar o **URL de início de sessão do fornecedor de serviço**e, em seguida, cole o **URL de início de sessão** caixa de texto no **DocuSign domínio e os URLs** secção a seguir o padrão do portal do Azure: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/`.

    ![Configurar o início de sessão único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Clique em **fechar**
    
17. No portal do Azure, clique em **guardar**.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-docusign-test-user"></a>Criar um utilizador de teste DocuSign

Aplicação suporta **apenas no tempo de aprovisionamento de utilizador** e depois dos utilizadores de autenticação são criados automaticamente na aplicação.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao DocuSign.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a DocuSign, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **DocuSign**.

    ![Configurar o início de sessão único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico DocuSign no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de DocuSign.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o aprovisionamento de utilizadores](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png

