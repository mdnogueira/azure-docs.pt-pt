---
title: "Tutorial: Integração do Azure Active Directory com Citrix ShareFile | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Citrix ShareFile."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: b85680104fe4f33638c559b2a12483a2312a4476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integração do Azure Active Directory com Citrix ShareFile

Neste tutorial, irá aprender a integrar Citrix ShareFile com o Azure Active Directory (Azure AD).

Integrar o Citrix ShareFile com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Citrix ShareFile.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para Citrix ShareFile (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Citrix ShareFile, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Citrix ShareFile-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Citrix ShareFile a partir da Galeria
2. Configurar e testar o Azure AD-início de sessão único

## <a name="add-citrix-sharefile-from-the-gallery"></a>Adicionar Citrix ShareFile a partir da Galeria
Para configurar a integração da Citrix ShareFile com o Azure AD, tem de adicionar Citrix ShareFile na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Citrix ShareFile na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Citrix ShareFile**, selecione **Citrix ShareFile** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Citrix ShareFile na lista de resultados](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Citrix ShareFile baseado na chamado "Britta Simon" um utilizador de teste.

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Citrix ShareFile é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Citrix ShareFile tem de ser estabelecida.

No Citrix ShareFile, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Citrix ShareFile, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Citrix ShareFile](#create-a-citrix-sharefile-test-user)**  - para ter um homólogo de Britta Simon Citrix ShareFile que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Citrix ShareFile.

**Para configurar o Azure AD-início de sessão único com Citrix ShareFile, execute os seguintes passos:**

1. No portal do Azure, no **Citrix ShareFile** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_samlbase.png)

3. No **Citrix ShareFile domínio e os URLs** secção, execute os seguintes passos:

    ![Citrix ShareFile domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_url.png)
    
    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenant-name>.sharefile.com/saml/login`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente do Citrix ShareFile](https://www.citrix.co.in/products/sharefile/support.html) para obter este valor. 

4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-sharefile-tutorial/tutorial_general_400.png)

6. No **configuração da Citrix ShareFile** secção, clique em **configurar ShareFile de Citrix** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração da Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no seu **Citrix ShareFile** site da empresa como administrador.

8. Na barra de ferramentas na parte superior, clique em **Admin**.

9. No painel de navegação esquerdo, selecione **configurar Single Sign-On**.
   
    ![Conta de administração](./media/active-directory-saas-sharefile-tutorial/ic773627.png "administração da conta")

10. No **Single Sign-On / SAML 2.0 configuração** página da caixa de diálogo em **definições básicas**, execute os seguintes passos:
   
    ![De sessão único-](./media/active-directory-saas-sharefile-tutorial/ic773628.png "de sessão único-")
   
    a. Clique em **ativar SAML**.
    
    b. No **o emissor de IDP / ID de entidade** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.

    c. Clique em **alteração** junto a **certificado x. 509** campo e, em seguida, carregue o certificado que transferiu a partir do portal do Azure.
    
    d. No **URL de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
    
    e. No **URL de fim de sessão** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure.

11. Clique em **guardar** no portal de gestão do Citrix ShareFile.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Criar um utilizador de teste Citrix ShareFile

Para permitir que os utilizadores do Azure AD sessão Citrix ShareFile, têm de ser aprovisionados para Citrix ShareFile. No caso de Citrix ShareFile, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Citrix ShareFile** inquilino.

2. Clique em **gerir utilizadores \> gerir utilizadores Home \> + criar empregado**.
   
   ![Criar empregado](./media/active-directory-saas-sharefile-tutorial/IC781050.png "criar empregado")

3. No **informações básicas** secção, execute passos abaixo:
   
   ![Informações básicas](./media/active-directory-saas-sharefile-tutorial/IC799951.png "informações básicas")
   
   a. No **endereço de correio eletrónico** caixa de texto, escreva o endereço de e-mail do Britta Simon como  **brittasimon@contoso.com** .
   
   b. No **nome próprio** caixa de texto, tipo **nome próprio** do utilizador como **Britta**.
   
   c. No **Apelido** caixa de texto, tipo **Apelido** do utilizador como **Simon**.

4. Clique em **adicionar utilizador**.
  
   >[!NOTE]
   >Titular da conta do Azure AD irá receber um e-mail e siga uma ligação para confirmar a respetiva conta para ficar ativa. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Citrix ShareFile ou APIs fornecidas pelos Citrix ShareFile para aprovisionar contas de utilizador do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Citrix ShareFile.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Citrix ShareFile, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Citrix ShareFile**.

    ![A ligação de Citrix ShareFile na lista de aplicações](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Citrix ShareFile no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação Citrix ShareFile.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png

