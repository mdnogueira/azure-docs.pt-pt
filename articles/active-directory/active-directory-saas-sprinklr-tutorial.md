---
title: "Tutorial: Integração do Azure Active Directory com Sprinklr | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Sprinklr."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 6e1622cd55e3b0e8063604ac9dc0cb0673fa9753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Integração do Azure Active Directory com Sprinklr

Neste tutorial, irá aprender a integrar Sprinklr com o Azure Active Directory (Azure AD).

Integrar Sprinklr com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Sprinklr
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Sprinklr (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Sprinklr, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Sprinklr início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Sprinklr a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-sprinklr-from-the-gallery"></a>Adicionar Sprinklr a partir da Galeria
Para configurar a integração de Sprinklr com o Azure AD, terá de adicionar Sprinklr a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Sprinklr a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Sprinklr**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_search.png)

5. No painel de resultados, selecione **Sprinklr**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Sprinklr com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Sprinklr é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Sprinklr tem de ser estabelecida.

No Sprinklr, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Sprinklr, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Sprinklr](#creating-a-sprinklr-test-user)**  - para ter um homólogo de Britta Simon Sprinklr que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Sprinklr.

**Para configurar o Azure AD-início de sessão único com Sprinklr, execute os seguintes passos:**

1. No portal do Azure, no **Sprinklr** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

3. No **Sprinklr domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.sprinklr.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize o valor com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente Sprinklr](https://www.sprinklr.com/contact-us/) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-sprinklr-tutorial/tutorial_general_400.png)

6. No **Sprinklr configuração** secção, clique em **configurar Sprinklr** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Sprinklr como administrador.

8. Aceda a **administração \> definições**.
   
    ![Administração](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "administração")

9. Aceda a **gerir parceiro \> início de sessão único** no painel esquerdo.
   
    ![Gerir parceiro](./media/active-directory-saas-sprinklr-tutorial/ic782908.png "gerir parceiro")

10. Clique em **+ adicionar inícios de sessão único**.
   
    ![Único inícios de sessão](./media/active-directory-saas-sprinklr-tutorial/ic782909.png "único inícios de sessão")

11. No **início de sessão único** página, execute os seguintes passos:
   
    ![Único inícios de sessão](./media/active-directory-saas-sprinklr-tutorial/ic782910.png "único inícios de sessão")

    a. No **nome** caixa de texto, escreva um nome para a sua configuração (por exemplo: *WAADSSOTest*).

    b. Selecione **ativada**.

    c. Selecione **utilizar o novo certificado de SSO**.
             
    e. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado do fornecedor de identidade** caixa de texto.

    f. Colar o **ID de entidade de SAML** valor que copiou do Portal do Azure para o **Id de entidade** caixa de texto.

    g. Colar o **único início de sessão no URL do serviço SAML** valor que copiou do Portal do Azure para o **URL de início de sessão do fornecedor de identidade** caixa de texto.

    h. Colar o **Sign-Out URL** valor que copiou do Portal do Azure para o **URL de fim de sessão do fornecedor de identidade** caixa de texto.
     
    posso. Como **tipo de ID de utilizador de SAML**, selecione **asserção contém utilizador "nome de utilizador do s sprinklr.com**.

    j. Como **localização de ID de utilizador de SAML**, selecione **ID de utilizador está a ser o elemento identificador de nome da declaração de assunto**.

    k. Clique em **Guardar**.
       
    ![SAML](./media/active-directory-saas-sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sprinklr-test-user"></a>Criar um utilizador de teste Sprinklr

1. Inicie sessão no site da sua empresa Sprinklr como administrador.

2. Aceda a **administração \> definições**.
   
    ![Administração](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "administração")

3. Aceda a **cliente gerir \> utilizadores** no painel esquerdo.
   
    ![Definições](./media/active-directory-saas-sprinklr-tutorial/ic782914.png "definições")

4. Clique em **adicionar utilizador**.
   
    ![Definições](./media/active-directory-saas-sprinklr-tutorial/ic782915.png "definições")

5. No **Editar utilizador** caixa de diálogo, execute os seguintes passos:
   
    ![Editar utilizador](./media/active-directory-saas-sprinklr-tutorial/ic782916.png "Editar utilizador") 

    a. No **E-Mail**, **nome próprio** e **Apelido** caixas de texto, escreva as informações de uma conta de utilizador do Azure AD que pretende aprovisionar.

    b. Selecione **desativado de palavra-passe**.

    c. Selecione **idioma**.

    d. Selecione **tipo de utilizador**.

    e. Clique em **atualização**.
   
     >[!IMPORTANT]
     >**Palavra-passe desativado** tem de ser selecionado para permitir que um utilizador inicie sessão através de um fornecedor de identidade. 
     
6. Aceda a **função**e, em seguida, execute os seguintes passos:
   
    ![Funções do parceiro](./media/active-directory-saas-sprinklr-tutorial/ic782917.png "funções do parceiro")

    a. Do **Global** lista, selecione **todos os\_permissões**.  

    b. Clique em **atualização**.

>[!NOTE]
>Pode utilizar quaisquer outras Sprinklr utilizador conta criação ferramentas ou APIs fornecidas pelo Sprinklr aprovisionar contas de utilizador do Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Sprinklr.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Sprinklr, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Sprinklr**.

    ![Configurar o início de sessão único](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Sprinklr no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Sprinklr para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_203.png

