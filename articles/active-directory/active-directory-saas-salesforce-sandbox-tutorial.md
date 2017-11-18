---
title: "Tutorial: Integração do Azure Active Directory com o Salesforce Sandbox | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e a Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 128d04fdf191b60441b695efef2bf602920d80e6
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Azure Active Directory com o Salesforce Sandbox

Neste tutorial, irá aprender a integrar o Salesforce Sandbox no Azure Active Directory (Azure AD).

Integrar o Salesforce Sandbox com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Sandbox do Salesforce.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para Salesforce Sandbox (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Sandbox do Salesforce, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Salesforce Sandbox-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de Salesforce Sandbox de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>A adição de Salesforce Sandbox de galeria
Para configurar a integração da Salesforce Sandbox com o Azure AD, tem de adicionar Salesforce Sandbox na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Salesforce Sandbox na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Salesforce Sandbox**, selecione **Salesforce Sandbox** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Salesforce Sandbox na lista de resultados](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com o Salesforce Sandbox baseado na chamado "Britta Simon" um utilizador de teste.

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Salesforce Sandbox é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Salesforce Sandbox tem de ser estabelecida.

No Salesforce Sandbox, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o Salesforce Sandbox, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)**  - para ter um homólogo de Britta Simon no Salesforce Sandbox que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Salesforce Sandbox.

**Para configurar o Azure AD-início de sessão único com o Salesforce Sandbox, execute os seguintes passos:**

1. No portal do Azure, no **Salesforce Sandbox** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. No **Salesforce Sandbox domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio de Sandbox Salesforce e URLs único informações de início de sessão](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. No **URL de início de sessão** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    b. No **identificador** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`
    
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte [equipa de suporte de cliente do Salesforce](https://help.salesforce.com/support) para obter estes valores.

4. No **certificado de assinatura de SAML** secção, clique em **certificado** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_400.png)

6. No **Salesforce Sandbox configuração** secção, clique em **configurar Sandbox do Salesforce** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

7. Abra um novo separador no seu browser e inicie sessão na sua conta de administrador do Salesforce Sandbox.

8. Clique em de **configuração** em **ícone definições** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/configure1.png)

9. Desloque para baixo até o **definições** no painel de navegação, clique em **identidade** para expandir a secção relacionada. Em seguida, clique em **as definições de início de sessão único**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Selecione **SAML ativada**e, em seguida, clique em **guardar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-enable-saml.png)

11. Para configurar as SAML único início de sessão definições, clique em **novo**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso-new.png)

12. A secção de SAML único início de sessão definições, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-saml-config.png)

    a. No **nome** caixa de texto, escreva o nome da configuração (por exemplo: *SPSSOWAAD_Test*). 

    b. No **emissor** campo, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure

    c. No **Id de entidade** caixa de texto, tipo `https://<instancename>--Sandbox.<entityid>.my.salesforce.com` se for a primeira instância do Salesforce Sandbox que estiver a adicionar ao seu diretório. Se já adicionou uma instância do Salesforce Sandbox, em seguida, para o **ID de entidade** escreva a **URL de início de sessão**, que deve ter este formato:`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`  
 
    d. Para carregar o **certificado do fornecedor de identidade**, clique em **Escolher ficheiro** para procurar e selecionar o ficheiro de certificado, o que transferiu a partir do portal do Azure.  

    e. Como **tipo de identidade de SAML**, escolha uma das seguintes opções:
    
      * Selecione **asserção contém o nome de utilizador do utilizador Salesforce**, se está a ser transferida Salesforce Username do utilizador na asserção SAML

      * Selecione **asserção contém o ID de Federação do objeto User**, se o ID de Federação do objeto de utilizador está a ser transmitido asserção SAML

      * Selecione **asserção contém o ID de utilização do objeto User**, se o ID de utilizador do objeto de utilizador está a ser transmitido na asserção SAML
 
    f. Como **SAML identidade localização**, selecione **é de identidade no elemento NameIdentifier da declaração de assunto**.

    g. Como **fornecedor iniciada pedido vínculo de serviço**, selecione **HTTP POST**. 

    h. No **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL Single Sign-On serviço**, que copiou do portal do Azure. 

    posso. SFDC não suporta a fim de sessão SAML.  Como solução, cole `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` para o **URL de fim de sessão do fornecedor de identidade** caixa de texto.

    j. Clique em **Guardar**.

### <a name="enable-your-domain"></a>Ativar o seu domínio
Esta secção assume que já criou um domínio.  Para obter mais informações, consulte [definir o nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Para ativar o seu domínio, execute os seguintes passos:**

1. No painel de navegação esquerdo do Salesforce, clique em **definições da empresa** para expandir a secção relacionada e, em seguida, clique em **meu domínio**.
   
     ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-my-domain.png)
   
   >[!NOTE]
   >Certifique-se que o seu domínio foi configurado corretamente. 

2. No **configuração da autenticação** secção, clique em **editar**, em seguida, como **serviço de autenticação**, selecione o nome de SAML único início de sessão na definição do anterior secção e, finalmente, clique em **guardar**.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-edit-auth-config.png)

Assim que tiver um domínio configurado, os utilizadores devem utilizar o URL do domínio para início de sessão para a sandbox do Salesforce.  

Para obter o valor do URL, clique no perfil SSO que criou na secção anterior.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-salesforce-sandbox-test-user"></a>Criar um utilizador de teste de Salesforce Sandbox

Nesta secção, um utilizador chamado Britta Simon é criado no Sandbox do Salesforce. Salesforce Sandbox suportam o aprovisionamento de just-in-time, que está ativada por predefinição.
Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe no Salesforce Sandbox, uma nova é criada quando tenta aceder a Salesforce Sandbox.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder acesso ao Sandbox do Salesforce.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Salesforce Sandbox, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Salesforce Sandbox**.

    ![A ligação de Salesforce Sandbox na lista de aplicações](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de Sandbox do Salesforce no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação Salesforce Sandbox.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_203.png

