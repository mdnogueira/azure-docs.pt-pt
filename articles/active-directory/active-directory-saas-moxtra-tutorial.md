---
title: "Tutorial: Integração do Azure Active Directory com Moxtra | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Moxtra."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: db2f041a44b6771b0a4f734e58d899298ef0847b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Tutorial: Integração do Azure Active Directory com Moxtra

Neste tutorial, irá aprender a integrar Moxtra com o Azure Active Directory (Azure AD).

Integrar Moxtra com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Moxtra
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Moxtra (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Moxtra, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Moxtra-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Moxtra a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-moxtra-from-the-gallery"></a>Adicionar Moxtra a partir da Galeria
Para configurar a integração de Moxtra com o Azure AD, terá de adicionar Moxtra a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Moxtra a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Moxtra**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_search.png)

5. No painel de resultados, selecione **Moxtra**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Moxtra com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Moxtra é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Moxtra tem de ser estabelecida.

No Moxtra, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Moxtra, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Moxtra](#creating-a-moxtra-test-user)**  - para ter um homólogo de Britta Simon Moxtra que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Moxtra.

**Para configurar o Azure AD-início de sessão único com Moxtra, execute os seguintes passos:**

1. No portal do Azure, no **Moxtra** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. No **Moxtra domínio e os URLs** secção, executar o passo seguinte:

    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL como:`https://www.moxtra.com/service/#login`

4. Aplicação de Moxtra espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos a partir de "**atributos de utilizador**" secção na página de integração de aplicações. A seguinte captura de ecrã mostra um exemplo para esta configuração. 

    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributo token SAML, conforme mostrado na imagem e efetuar os seguintes passos:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | nome próprio | User.givenName |
    | Apelido | User.Surname |
    | idpid    | < ID de entidade SAML > 

    > [!Note]
    > O valor de **idpid** atributo não é real. Pode obter o valor real do **referência rápida** secção em **Moxtra configuração**.
    
    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_04.png)

    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_05.png)

    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.

    d. Clique em **OK**.
    
5. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_400.png)

7. No **Moxtra configuração** secção, clique em **configurar Moxtra** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_configure.png) 

8. Na outra janela do browser, inicie sessão site da sua empresa Moxtra como administrador.

9. Na barra de ferramentas no lado esquerdo, clique em **consola de administração > SAML-início de sessão único**e, em seguida, clique em **novo**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 

10. No **SAML** página, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. No **nome** caixa de texto, escreva um nome para a sua configuração (por ex.: *SAML*). 
  
    b. No **ID da entidade IdP** caixa de texto, colar o valor de **ID de entidade de SAML** que copiou do portal do Azure. 
 
    c. No **URL de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure. 
 
    d. No **AuthnContextClassRef** caixa de texto, tipo **urn: oasis: os nomes: tc: SAML:2.0:ac:classes:Password**. 
 
    e. No **NameID formato** caixa de texto, tipo **urn: oasis: os nomes: tc: SAML:1.1:nameid-formato: endereço de correio eletrónico**. 
 
    f. Abra certificado que transferiu a partir do portal do Azure no bloco de notas, copiar o conteúdo e, em seguida, cole-o para o **certificado** caixa de texto.    
 
    g. Na caixa de SAML e-mail domínio texto, escreva o seu domínio de e-mail SAML.    
  
    >[!NOTE]
    >Para ver os passos para verificar o domínio, clique o "**posso**" abaixo.

    h. Clique em **atualização**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-moxtra-test-user"></a>Criar um utilizador de teste Moxtra

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon Moxtra.

**Para criar um utilizador chamado Britta Simon Moxtra, execute os seguintes passos:**

1. Inicie sessão site da sua empresa Moxtra como administrador.

2. Na barra de ferramentas no lado esquerdo, clique em **consola de administração > Gestão de utilizadores**e, em seguida, **adicionar utilizador**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 

3. No **adicionar utilizador** caixa de diálogo, execute os seguintes passos:
  
    a. No **nome próprio** caixa de texto, tipo **Britta**.
  
    b. No **Apelido** caixa de texto, tipo **Simon**.
  
    c. No **E-Mail** caixa de texto, tipo de Britta endereço de correio eletrónico mesma que no portal do Azure.
  
    d. No **divisão** caixa de texto, tipo **Dev**.
  
    e. No **departamento** caixa de texto, tipo **IT**.
  
    f. Selecione **administrador**.
  
    g. Clique em **Adicionar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Moxtra.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Moxtra, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Moxtra**.

    ![Configurar o início de sessão único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Moxtra no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Moxtra.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png

