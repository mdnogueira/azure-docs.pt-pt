---
title: "Tutorial: Integração do Azure Active Directory com Litmos | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Litmos."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: ef1b5860ba0a406022bbd11afb366d14bee2c57d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Tutorial: Integração do Azure Active Directory com Litmos

Neste tutorial, irá aprender a integrar Litmos com o Azure Active Directory (Azure AD).

Integrar Litmos com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Litmos.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Litmos (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Litmos, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Litmos-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Litmos a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-litmos-from-the-gallery"></a>Adicionar Litmos a partir da Galeria
Para configurar a integração de Litmos com o Azure AD, terá de adicionar Litmos a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Litmos a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Litmos**, selecione **Litmos** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Litmos na lista de resultados](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Litmos com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Litmos é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Litmos tem de ser estabelecida.

No Litmos, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Litmos, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Litmos](#create-a-litmos-test-user)**  - para ter um homólogo de Britta Simon Litmos que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Litmos.

**Para configurar o Azure AD-início de sessão único com Litmos, execute os seguintes passos:**

1. No portal do Azure, no **Litmos** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_samlbase.png)

3. No **Litmos domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio Litmos e os URLs únicos de informações de início de sessão](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.litmos.com/account/Login`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualizar estes valores com o real identificador e o URL de resposta, que serão explicados de forma mais tarde no tutorial ou contacte [Litmos suporta equipa](https://www.litmos.com/contact-us/) para obter estes valores.

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_certificate.png)

5. Como parte da configuração, tem de personalizar o **atributos Token SAML** para a sua aplicação Litmos.

    ![Secção de atributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute.png)
           
    | Nome do atributo   | Valor do atributo |   
    | ---------------  | ----------------|
    | Nome próprio |User.givenName |
    | Apelido  |User.Surname |
    | E-mail |User.Mail |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Adicionar atributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_04.png)

    ![Adicionar atributo Dailog](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_05.png)

    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **OK**.     

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-litmos-tutorial/tutorial_general_400.png)

7. Numa janela do browser diferente, início de sessão no site da sua empresa Litmos como administrador.

8. Na barra de navegação no lado esquerdo, clique em **contas**.
   
    ![Secção de contas no lado de aplicação][22] 

9. Clique em de **integrações** separador.
   
    ![Separador de integração][23] 

10. No **integrações** separador, desloque para baixo até **3rd integrações de terceiros**e, em seguida, clique em **SAML 2.0** separador.
   
    ![SAML 2.0 secção][24] 

11. Copie o valor em **SAML o ponto final para litmos é:** e cole-o para o **URL de resposta** textbox no **Litmos domínio e os URLs** secção no portal do Azure. 
   
    ![Ponto final SAML][26] 

12. No seu **Litmos** aplicação, execute os seguintes passos:
    
     ![Aplicação de Litmos][25] 
     
     a. Clique em **ativar SAML**.
    
     b. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado x. 509 de SAML** caixa de texto.
     
     c. Clique em **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-litmos-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-litmos-test-user"></a>Criar um utilizador de teste Litmos

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon Litmos.  
A aplicação de Litmos suporta Just-in-Time o aprovisionamento. Isto significa uma conta de utilizador é criada automaticamente se necessário durante uma tentativa de aceder à aplicação através do painel de acesso.

**Para criar um utilizador chamado Britta Simon Litmos, execute os seguintes passos:**

1. Numa janela do browser diferente, início de sessão no site da sua empresa Litmos como administrador.

2. Na barra de navegação no lado esquerdo, clique em **contas**.
   
    ![Secção de contas no lado de aplicação][22] 

3. Clique em de **integrações** separador.
   
    ![Separador integrações][23] 

4. No **integrações** separador, desloque para baixo até **3rd integrações de terceiros**e, em seguida, clique em **SAML 2.0** separador.
   
    ![SAML 2.0][24] 
    
5. Selecione **Autogenerate utilizadores**
   
    ![Utilizadores de AutoGenerate][27] 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Litmos.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Litmos, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Litmos**.

    ![A ligação de Litmos na lista de aplicações](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.  

Quando clica no mosaico Litmos no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Litmos. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png

