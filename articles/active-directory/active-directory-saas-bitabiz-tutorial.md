---
title: "Tutorial: Integração do Azure Active Directory com BitaBIZ | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e BitaBIZ."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: dca0a460224436d3886cf9a9c354ce662f99ae84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Tutorial: Integração do Azure Active Directory com BitaBIZ

Neste tutorial, irá aprender a integrar BitaBIZ com o Azure Active Directory (Azure AD).

Integrar BitaBIZ com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao BitaBIZ.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para BitaBIZ (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com BitaBIZ, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um BitaBIZ-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar BitaBIZ a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-bitabiz-from-the-gallery"></a>Adicionar BitaBIZ a partir da Galeria
Para configurar a integração de BitaBIZ com o Azure AD, terá de adicionar BitaBIZ a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar BitaBIZ a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **BitaBIZ**, selecione **BitaBIZ** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![BitaBIZ na lista de resultados](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com BitaBIZ com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no BitaBIZ é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no BitaBIZ tem de ser estabelecida.

No BitaBIZ, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com BitaBIZ, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste BitaBIZ](#create-a-bitabiz-test-user)**  - para ter um homólogo de Britta Simon BitaBIZ que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação BitaBIZ.

**Para configurar o Azure AD-início de sessão único com BitaBIZ, execute os seguintes passos:**

1. No portal do Azure, no **BitaBIZ** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

3. No **BitaBIZ domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no modo de IDP iniciado:

    ![Domínio BitaBIZ e os URLs únicos de informações de início de sessão](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_url.png)

    No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > O valor no URL acima é para a demonstração apenas. Atualize o valor com o identificador real, que é explicado mais tarde no tutorial.

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Domínio BitaBIZ e os URLs únicos de informações de início de sessão](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_url1.png)

    No **URL de início de sessão** caixa de texto, escreva o URL:`https://www.bitabiz.com/dashboard`

5. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-bitabiz-tutorial/tutorial_general_400.png)
    
7. No **BitaBIZ configuração** secção, clique em **configurar BitaBIZ** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_configure.png) 

8. Numa janela do browser web diferente, início de sessão no seu inquilino BitaBIZ como administrador.

9. Clique em **configuração ADMIN**.

    ![Configuração de BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings1.png)

10. Clique em **integrações Microsoft** em **adicionar valor** secção.

    ![Configuração de BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings2.png)

11. Vá para a secção **Microsoft Azure AD (ativar início de sessão único)** e execute os seguintes passos:

    ![Configuração de BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/settings3.png)

    a. Copie o valor da **ID de entidade ("Identifier" no Azure AD)** caixa de texto e cole-o para o **identificador** caixa de texto no **BitaBIZ domínio e os URLs** secção no portal do Azure. 
    
    b. No **do Azure AD URL Single Sign-On serviço** caixa de texto, colar **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.
    
    c. No **ID de entidade do Azure AD SAML** caixa de texto, colar **ID de entidade de SAML**, que copiou do portal do Azure.

    d. Abra o transferido **Certificate(Base64)** ficheiro no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **do Azure AD certificado de assinatura (codificação Base64)** caixa de texto.

    e. Adicionar o seu domínio de correio eletrónico de negócio ou seja, nome mycompany.com no **nome de domínio** caixa de texto para atribuir o SSO para os utilizadores na sua empresa com este domínio de e-mail (não obrigatório).
    
    f. Marca **SSO ativado** a conta de BitaBIZ.
    
    g. Clique em **guardar a configuração do Azure AD** para guardar e ativar a configuração de SSO.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-bitabiz-test-user"></a>Criar um utilizador de teste BitaBIZ

Para permitir que os utilizadores do Azure AD iniciem sessão nos BitaBIZ, têm de ser aprovisionados para BitaBIZ.  
No caso de BitaBIZ, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa BitaBIZ como administrador.

2. Clique em **configuração ADMIN**.

    ![BitaBIZ adicionar utilizador](./media/active-directory-saas-bitabiz-tutorial/settings1.png)

3. Clique em **adicionar utilizadores** em **organização** secção.

    ![BitaBIZ adicionar utilizador](./media/active-directory-saas-bitabiz-tutorial/user1.png)

4. Clique em **empregado novo adicionar**.

    ![BitaBIZ adicionar utilizador](./media/active-directory-saas-bitabiz-tutorial/user2.png)

5. No **"Adicionar nova empregado"** diálogo página, execute os seguintes passos:

    ![BitaBIZ adicionar utilizador](./media/active-directory-saas-bitabiz-tutorial/user3.png)

    a. No **nome próprio** caixa de texto, tipo o primeiro nome de utilizador como Britta.

    b. No **Apelido** caixa de texto, escreva o apelido do utilizador como Simon.

    c. No **E-Mail** caixa de texto, como o tipo de endereço de correio eletrónico do utilizador Brittasimon@contoso.com.

    d. Selecione uma data no **data da sua admissão**.

    e. Existem outros atributos de utilizador não obrigatórias que podem ser configurados para o utilizador. Consulte o [documento de configuração de empregado](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) para obter mais detalhes.    
    
    f. Clique em **guardar empregado**.
    
    > [!NOTE]
    > Titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue-se de uma ligação para confirmar a respetiva conta para ficar ativa.
    
### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para BitaBIZ.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a BitaBIZ, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **BitaBIZ**.

    ![A ligação de BitaBIZ na lista de aplicações](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico BitaBIZ no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de BitaBIZ.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_203.png

