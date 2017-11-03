---
title: "Tutorial: Integração do Azure Active Directory nuvem criativos Adobe | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e nuvem criativos Adobe."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: ad2c3999ac8bfc8dfe0fc662ee37b41fff38444f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Tutorial: Integração do Azure Active Directory nuvem criativos Adobe

Neste tutorial, irá aprender a integrar nuvem criativos Adobe com o Azure Active Directory (Azure AD).

Nuvem criativos Adobe a integração com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à nuvem criativos Adobe.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para Adobe nuvem criativos (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com nuvem criativos Adobe, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma nuvem criativos Adobe-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de nuvem criativos Adobe de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>A adição de nuvem criativos Adobe de galeria
Para configurar a integração da nuvem criativos Adobe com o Azure AD, tem de adicionar nuvem criativos Adobe na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar nuvem criativos Adobe na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **nuvem criativos Adobe**, selecione **nuvem criativos Adobe** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Adobe nuvem criativos na lista de resultados](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com o Adobe nuvem criativos, com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na nuvem criativos Adobe é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na nuvem criativos Adobe tem de ser estabelecida.

No Adobe criativos na nuvem, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com nuvem criativos Adobe, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de nuvem criativos Adobe](#create-an-adobe-creative-cloud-test-user)**  - para ter um homólogo de Britta Simon na nuvem de criativos Adobe que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de nuvem criativos Adobe.

**Para configurar o Azure AD-início de sessão único com nuvem criativos Adobe, execute os seguintes passos:**

1. No portal do Azure, no **nuvem criativos Adobe** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. No **Adobe criativos nuvem domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no modo de IDP iniciado:

    ![Adobe criativos nuvem domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://www.okta.com/saml2/service-provider/<token>`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador e o URL de resposta real. Contacte [equipa de suporte de cliente de Cloud criativos Adobe](https://helpx.adobe.com/in/contact/support.html) para obter estes valores. 

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Adobe criativos nuvem domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    No **URL de início de sessão** caixa de texto, digite o valor como:`https://adobe.com`

5. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Aplicação de nuvem criativos Adobe espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos do **atributo de utilizador** separador da aplicação. A seguinte captura de ecrã mostra um exemplo para este.

    ![Configurar o início de sessão único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributos token SAML, conforme mostrado na imagem acima e execute os seguintes passos:

    | Nome do atributo | Valor do atributo |
    | ---------------| ----------------|
    | Nome próprio |User.givenName |
    | Apelido |User.Surname |
    | E-mail |User.Mail |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o início de sessão único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)
    
    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **OK**.
    
8. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)
    
9. No **configuração da nuvem criativos Adobe** secção, clique em **configurar Adobe criativos na nuvem** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida**.

    ![Configuração da nuvem criativos Adobe](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)
    
10. Numa janela do browser web diferente, início de sessão no seu inquilino de nuvem criativos Adobe como administrador.

11. Aceda a **identidade** no painel de navegação esquerdo e clique no seu domínio. Em seguida, execute os seguintes passos no **início de sessão único na configuração necessária** secção.

    ![Definições](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "definições")
    
    a. Clique em **procurar** para carregar o certificado transferido do Azure AD para **IDP certificado**.
    
    b. No **emissor IDP** caixa de texto, colocar o valor de **Id de entidade de SAML** que copiou do **configurar início de sessão** secção no portal do Azure.
    
    c. No **URL de início de sessão do IDP** caixa de texto, colocar o valor de **URL do serviço SAML SSO** que copiou do **configurar início de sessão** secção no portal do Azure.
    
    d. Selecione **HTTP - redirecionamento** como **IDP enlace**.
    
    e. Selecione **endereço de correio eletrónico** como **definição de início de sessão do utilizador**.
    
    f. Clique em **guardar** botão.

12. O dashboard agora apresentará o XML **"Transferir metadados"** ficheiro. Contém EntityDescriptor URL e o URL de AssertionConsumerService do Adobe. Volte a abrir o ficheiro e configurá-las na aplicação do Azure AD.

    ![Configurar o início de sessão único no lado de aplicação](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![Configurar o início de sessão único no lado de aplicação](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Utilize o valor de EntityDescriptor Adobe fornecido lhe **identificador** no **configurar definições de aplicação** caixa de diálogo.

    b. Utilize o valor de AssertionConsumerService Adobe fornecido lhe **URL de resposta** no **configurar definições de aplicação** caixa de diálogo.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Criar um utilizador de teste de nuvem criativos Adobe

Para permitir que os utilizadores do Azure AD inicie sessão no Cloud criativos Adobe, têm de ser aprovisionados numa nuvem criativos Adobe. No caso de Adobe criativos nuvem, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionar contas de utilizador, execute os seguintes passos:

1. Inicie sessão no site da sua empresa nuvem criativos Adobe como administrador.

2. Adicionar o utilizador da consola do Adobe como ID Federado e atribuí-las a um grupo para elegibilidade

    ![A ligação de nuvem criativos Adobe na lista de aplicações](./media/active-directory-saas-adobe-creative-cloud-tutorial/users.png)  

3. Neste momento, escreva o endereço de e-mail/upn no formulário de início de sessão do Adobe, prima separador, e deve ser federado para o Azure AD:
    * Web acesso: www.adobe.com > início de sessão
    * Dentro do utilitário de aplicação de ambiente de trabalho > início de sessão
    * Na aplicação > ajudar > início de sessão

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à nuvem criativos Adobe.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon nuvem criativos Adobe, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **nuvem criativos Adobe**.

    ![A ligação de nuvem criativos Adobe na lista de aplicações](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de nuvem criativos Adobe no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de nuvem criativos Adobe.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png

