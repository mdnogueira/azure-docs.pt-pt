---
title: "Tutorial: Integração do Azure Active Directory com Mozy empresarial | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Mozy Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: ac73aadcb8205f24f9d2dbce5af76f53bbcb9753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: Integração do Azure Active Directory com Mozy empresarial

Neste tutorial, irá aprender a integração empresarial Mozy com o Azure Active Directory (Azure AD).

Integrar Mozy Enterprise com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Mozy Enterprise
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a empresa Mozy (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Mozy empresarial, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma empresa de Mozy-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Mozy Enterprise da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Adicionar Mozy Enterprise da galeria do
Para configurar a integração do Mozy Enterprise com o Azure AD, tem de adicionar Mozy Enterprise da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Mozy Enterprise da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Mozy Enterprise**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. No painel de resultados, selecione **Mozy Enterprise**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configura e teste do Azure AD-início de sessão único com Mozy empresarial com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo Mozy empresa for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado Mozy empresa tem de ser estabelecida.

Numa empresa Mozy, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Mozy empresarial, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Mozy Enterprise](#creating-a-mozy-enterprise-test-user)**  - para ter um homólogo de Britta Simon Mozy empresa que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Mozy Enterprise.

**Para configurar o Azure AD-início de sessão único com Mozy Enterprise, execute os seguintes passos:**

1. No portal do Azure, no **Mozy Enterprise** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. No **Mozy Enterprise domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente do Enterprise Mozy](http://support.mozy.com/) para obter este valor.

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_400.png)

6. No **Mozy Enterprise configuração** secção, clique em **configurar Enterprise de Mozy** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Mozy empresarial como um administrador.

8. No **configuração** secção, clique em **política de autenticação**.
   
   ![Política de autenticação](./media/active-directory-saas-mozy-enterprise-tutorial/ic777314.png "política de autenticação")

9. No **política de autenticação** secção, execute os seguintes passos:
   
   ![Política de autenticação](./media/active-directory-saas-mozy-enterprise-tutorial/ic777315.png "política de autenticação")
   
   a. Selecione **serviço de diretório** como **fornecedor**.
   
   b. Selecione **utilizar LDAP Push**.
   
   c. Clique em de **autenticação SAML** separador.
   
   d. Colar **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure para o **URL de autenticação** caixa de texto.
   
   e. Colar **ID de entidade de SAML**, que copiou do portal do Azure para o **ponto final de SAML** caixa de texto.
   
   f. Abra o certificado codificado de base-64 transferido no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole o certificado completo no **SAML certificado** caixa de texto.
   
   g. Selecione **ativar a SSO para os administradores iniciar sessão com as respetivas credenciais de rede**.
   
   h. Clique em **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Criar um utilizador de teste Mozy Enterprise

Para permitir que os utilizadores do Azure AD sessão Mozy Enterprise, têm de ser aprovisionados na empresa Mozy. No caso de Mozy Enterprise, o aprovisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras Mozy Enterprise utilizador conta criação ferramentas ou APIs fornecidas pelo Mozy Enterprise para aprovisionar contas de utilizador do AAD.

**Aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Mozy Enterprise** inquilino.

2. Clique em **utilizadores**e, em seguida, clique em **adicionar novo utilizador**.
   
   ![Os utilizadores](./media/active-directory-saas-mozy-enterprise-tutorial/ic777317.png "utilizadores")
   
   >[!NOTE]
   >O **adicionar novo utilizador** opção só é apresentada apenas se **Mozy** é selecionado como o fornecedor em **política de autenticação**. Se estiver configurada a autenticação SAML, em seguida, os utilizadores são adicionados automaticamente no respetivo primeiro início de sessão através de início de sessão único em.
    
3. Na caixa de diálogo novo do utilizador, execute os seguintes passos:
   
   ![Adicionar utilizadores](./media/active-directory-saas-mozy-enterprise-tutorial/ic777318.png "adicionar utilizadores")
   
   a. Do **escolha um grupo** lista, selecione um grupo.
   
   b. Do **que tipo de utilizador** lista, selecione um tipo.
   
   c. No **Username** caixa de texto, escreva o nome do utilizador do Azure AD.
   
   d. No **E-Mail** caixa de texto, escreva o endereço de e-mail do utilizador do Azure AD.
   
   e. Selecione **enviar correio eletrónico de instruções de utilizador**.
   
   f. Clique em **Adicionar es**.

     >[!NOTE]
     > Depois de criar o utilizador, será enviado um e-mail ao utilizador do Azure AD que inclui uma ligação para confirmar a conta para ficar ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para a empresa Mozy.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Mozy Enterprise, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Mozy Enterprise**.

    ![Configurar o início de sessão único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Mozy empresarial no painel de acesso, deve obter a página de início de sessão da aplicação Mozy empresarial.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_203.png

