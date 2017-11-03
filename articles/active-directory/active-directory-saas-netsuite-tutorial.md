---
title: "Tutorial: Integração do Azure Active Directory com Netsuite | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Netsuite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.openlocfilehash: f2184f426c0e60ea2b6ce029b80d3d63821794d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Tutorial: Integração do Azure Active Directory com Netsuite

Neste tutorial, irá aprender a integrar Netsuite com o Azure Active Directory (Azure AD).

Integrar Netsuite com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Netsuite
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Netsuite (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Netsuite, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Netsuite-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Netsuite a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-netsuite-from-the-gallery"></a>Adicionar Netsuite a partir da Galeria
Para configurar a integração de Netsuite com o Azure AD, terá de adicionar Netsuite a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Netsuite a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Netsuite**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. No painel de resultados, selecione **Netsuite**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Netsuite com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Netsuite é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Netsuite tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Netsuite.

Para configurar e testar o Azure AD-início de sessão único com Netsuite, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Netsuite](#creating-a-netsuite-test-user)**  - para ter um homólogo de Britta Simon Netsuite que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Netsuite.

**Para configurar o Azure AD-início de sessão único com Netsuite, execute os seguintes passos:**

1. No portal do Azure, no **Netsuite** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. No **Netsuite domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão seguinte: `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs``https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > Estes não são valores reais. Atualize estes valores com o URL de resposta real. Contacte [equipa de suporte de Netsuite](http://www.netsuite.com/portal/services/support.shtml) para obter estes valores.
 
4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. No **Netsuite configuração** secção, clique em **configurar Netsuite** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. Abra um novo separador no seu browser e inicie sessão no site da sua empresa Netsuite como administrador.

8. Na barra de ferramentas na parte superior da página, clique em **configuração**, em seguida, clique em **Gestor de configuração**.

    ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. Do **tarefas de configuração** lista, selecione **integração**.

    ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. No **Gerir autenticação** secção, clique em **SAML-início de sessão único**.

    ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. No **configuração SAML** página, execute os seguintes passos:
   
    a. Copiar o **único início de sessão no URL do serviço SAML** valor a partir da **referência rápida** secção **configurar início de sessão** e cole-o para o **página de início de sessão do fornecedor de identidade** campo no Netsuite.

    ![Configurar o início de sessão único](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b. No Netsuite, selecione **método de autenticação principal**.

    c. Para o campo de etiqueta **os metadados do fornecedor de identidade SAMLV2**, selecione **carregar ficheiro de metadados do IDP**. Em seguida, clique em **procurar** para carregar o ficheiro de metadados que transferiu a partir do portal do Azure.

    ![Configurar o início de sessão único](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Clique em **submeter**.

12. No Azure AD, clique em **ver e editar todos os outros atributos de utilizador** caixa de verificação e adicione o atributo.

    ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. Para o **nome de atributo** campo, digite na `account`. Para o **valor do atributo** campo, escreva o ID de conta Netsuite. Este valor é constante e altere com a conta. As instruções sobre como localizar o ID da conta estão incluídas abaixo:

      ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. No Netsuite, clique em **configuração** no menu de navegação superior.

    b. Em seguida, clique em de **tarefas de configuração** secção do menu de navegação esquerdo, selecione o **integração** secção e clique em **preferências de serviços Web**.

    c. Copie o seu ID de conta Netsuite e cole-o para o **valor do atributo** campo no Azure AD.

    ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Antes dos utilizadores podem efetuar o início de sessão único para Netsuite, estes tem primeiro de atribuir as permissões adequadas no Netsuite. Siga as instruções abaixo para atribuir estas permissões.

    a. No menu de navegação superior, clique em **configuração**, em seguida, clique em **Gestor de configuração**.
      
      ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b. No menu de navegação esquerdo, selecione **utilizadores/funções**, em seguida, clique em **gerir funções**.
      
      ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Clique em **nova função**.

    d. Escreva um **nome** para a sua nova função de segurança e selecione o **único início de sessão só** caixa de verificação.
      
      ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. Clique em **Guardar**.

    f. No menu na parte superior, clique em **permissões**. Em seguida, clique em **configuração**.
      
       ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. Selecione **definir segurança SAM Single Sign-on**e, em seguida, clique em **adicionar**.

    h. Clique em **Guardar**.

    posso. No menu de navegação superior, clique em **configuração**, em seguida, clique em **Gestor de configuração**.
      
       ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. No menu de navegação esquerdo, selecione **utilizadores/funções**, em seguida, clique em **gerir utilizadores**.
      
       ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Selecione um utilizador de teste. Em seguida, clique em **editar**.
      
       ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. Na caixa de diálogo Funções, selecione a função que criou e clique em **adicionar**.
      
       ![Configurar o início de sessão único](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Clique em **Guardar**.
    
> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**. 

### <a name="creating-a-netsuite-test-user"></a>Criar um utilizador de teste Netsuite

Nesta secção, é criado um utilizador chamado Britta Simon Netsuite. Netsuite suporta o aprovisionamento de just-in-time, que está ativada por predefinição.
Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe na Netsuite, uma nova é criada quando tentar aceder Netsuite.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Netsuite.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Netsuite, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Netsuite**.

    ![Configurar o início de sessão único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Para testar as definições de início de sessão único, abra o painel de acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), iniciar sessão na conta de teste e, em **Netsuite**.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o aprovisionamento de utilizadores](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

