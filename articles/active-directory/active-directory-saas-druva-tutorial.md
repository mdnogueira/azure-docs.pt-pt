---
title: "Tutorial: Integração do Azure Active Directory com Druva | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Druva."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: b23e73c47b9a00893e036b67826e4b7ead819a1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Tutorial: Integração do Azure Active Directory com Druva

Neste tutorial, irá aprender a integrar Druva com o Azure Active Directory (Azure AD).

Integrar Druva com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Druva.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Druva (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Druva, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Druva-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Druva a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-druva-from-the-gallery"></a>Adicionar Druva a partir da Galeria
Para configurar a integração de Druva com o Azure AD, terá de adicionar Druva a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Druva a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Druva**, selecione **Druva** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Druva na lista de resultados](./media/active-directory-saas-druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Druva com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Druva é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Druva tem de ser estabelecida.

No Druva, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Druva, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Druva](#create-a-druva-test-user)**  - para ter um homólogo de Britta Simon Druva que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Druva.

**Para configurar o Azure AD-início de sessão único com Druva, execute os seguintes passos:**

1. No portal do Azure, no **Druva** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-druva-tutorial/tutorial_druva_samlbase.png)

3. No **Druva domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-druva-tutorial/tutorial_druva_url.png)

    No **URL de início de sessão** caixa de texto, escreva o URL:`https://cloud.druva.com/home`

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-druva-tutorial/tutorial_druva_certificate.png) 

5. A aplicação de Druva espera as asserções de SAML num formato específico, que necessita para adicionar mapeamentos de atributos personalizados para o **atributos Token SAML** configuração. 

    ![Configurar o início de sessão único](./media/active-directory-saas-druva-tutorial/tutorial_druva_attribute.png)

6. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributos token SAML, conforme mostrado na imagem anterior e execute os seguintes passos:

    | Nome do atributo      | Valor do atributo      |
    | ------------------- | -------------------- |
    | insync\_auth\_token |Introduza o valor do token gerado |
    
    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-druva-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o início de sessão único](./media/active-directory-saas-druva-tutorial/tutorial_attribute_05.png)
    
    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha. O valor gerado token é explicado mais tarde no tutorial.
    
    d. Clique em **OK**.    

7. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-druva-tutorial/tutorial_general_400.png)

8. No **Druva configuração** secção, clique em **configurar Druva** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-druva-tutorial/tutorial_druva_configure.png) 

9. Numa janela do browser web diferente, inicie sessão no site da sua empresa Druva como administrador.

10. Aceda a **gerir \> definições**.

    ![Definições](./media/active-directory-saas-druva-tutorial/ic795091.png "definições")

11. Na caixa de diálogo Definições de início de sessão único, execute os seguintes passos:

    ![Single Sign-On definições](./media/active-directory-saas-druva-tutorial/ic795092.png "único as definições de início de sessão")
    
    a. Colar **único início de sessão no URL do serviço SAML** valor que copiou do portal do Azure para o **URL de início de sessão do fornecedor de ID** caixa de texto.
    
    b. Colar **Sign-Out URL** valor que copiou do portal do Azure para o **URL de fim de sessão do fornecedor de ID** caixa de texto.
    
     c. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **ID fornecedor certificado** caixa de texto
     
     d. Para abrir o **definições** página, clique em **guardar**.

12. No **definições** página, clique em **gerar Token SSO**.

    ![Definições](./media/active-directory-saas-druva-tutorial/ic795093.png "definições")

13. No **único início de sessão no Token de autenticação** caixa de diálogo, execute os seguintes passos:

    ![Token SSO](./media/active-directory-saas-druva-tutorial/ic795094.png "SSO Token")
    
    a. Clique em **cópia**, colar copiados valor o **valor** textbox no **adicionar atributo** secção.
    
    b. Clique em **Fechar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-druva-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-druva-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-druva-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-druva-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-druva-test-user"></a>Criar um utilizador de teste Druva

Para permitir que os utilizadores do Azure AD iniciem sessão nos Druva, têm de ser aprovisionados para Druva. No caso de Druva, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Druva** site da empresa como administrador.

2. Aceda a **gerir \> utilizadores**.
   
   ![Gerir utilizadores](./media/active-directory-saas-druva-tutorial/ic795097.png "gerir utilizadores")

3. Clique em **criar novos**.
   
   ![Gerir utilizadores](./media/active-directory-saas-druva-tutorial/ic795098.png "gerir utilizadores")

4. Na caixa de diálogo Criar novo utilizador, execute os seguintes passos:
   
   ![Criar NewUser](./media/active-directory-saas-druva-tutorial/ic795099.png "criar NewUser")
   
   a. No **endereço de correio eletrónico** caixa de texto, introduza o e-mail do utilizador, como  **brittasimon@contoso.com** .
   
   b. No **nome** caixa de texto, introduza o nome de utilizador como **BrittaSimon**.
   
   c. Clique em **criar utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras Druva utilizador conta criação ferramentas ou APIs fornecidas pelo Druva aprovisionar contas de utilizador do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Druva.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Druva, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Druva**.

    ![A ligação de Druva na lista de aplicações](./media/active-directory-saas-druva-tutorial/tutorial_druva_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Druva no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Druva.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-druva-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-druva-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-druva-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-druva-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-druva-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-druva-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-druva-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-druva-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-druva-tutorial/tutorial_general_203.png

