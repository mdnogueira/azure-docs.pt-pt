---
title: "Tutorial: Integração do Azure Active Directory com o Salesforce Sandbox | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e a Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 90e08b9cf2feb93de4877bec9734352949896dca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Azure Active Directory com o Salesforce Sandbox

Neste tutorial, irá aprender a integrar o Salesforce Sandbox no Azure Active Directory (Azure AD).

Integrar o Salesforce Sandbox com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Salesforce Sandbox
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Salesforce Sandbox (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Sandbox do Salesforce, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Salesforce Sandbox início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de Salesforce Sandbox de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>A adição de Salesforce Sandbox de galeria
Para configurar a integração da Salesforce Sandbox com o Azure AD, tem de adicionar Salesforce Sandbox na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Salesforce Sandbox na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Salesforce Sandbox**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. No painel de resultados, selecione **Salesforce Sandbox**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Salesforce Sandbox com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Salesforce Sandbox é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Salesforce Sandbox tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Salesforce Sandbox.

Para configurar e testar o Azure AD-início de sessão único com o Salesforce Sandbox, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Salesforce Sandbox](#creating-a-salesforce-sandbox-test-user)**  - para ter um homólogo de Britta Simon no Salesforce Sandbox que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Salesforce Sandbox.

**Para configurar o Azure AD-início de sessão único com o Salesforce Sandbox, execute os seguintes passos:**

1. No portal do Azure, no **Salesforce Sandbox** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. No **Salesforce Sandbox domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     No **URL de início de sessão** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://<subdomain>.my.salesforce.com`

    > [!NOTE] 
    > Este valor não é o real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte do Salesforce Sandbox cliente](https://help.salesforce.com/support) para obter este valor.


4. Se já tiver configurado início de sessão único para outra instância do Salesforce Sandbox no seu diretório, em seguida, também tem de configurar o **identificador** ter o mesmo valor que o **iniciar sessão no URL**. 
    
    >[!Note]
    >O **identificador** campo pode ser encontrado ao verificar o **mostrar as definições avançadas** caixa de verificação no **URL da aplicação configurar** página da caixa de diálogo 


5. No **certificado de assinatura de SAML** secção, clique em **certificado** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. No **Salesforce Sandbox configuração** secção, clique em **configurar Sandbox do Salesforce** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. Abra um novo separador no seu browser e inicie sessão na sua conta de administrador do Salesforce Sandbox.

9. No menu na parte superior, clique em **configuração**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. No painel de navegação à esquerda, clique em **controlos de segurança**e, em seguida, clique em **as definições de início de sessão único**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. A secção de definições de início de sessão único, execute os seguintes passos: ![configurar Single Sign-On](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)
     
     a.  Selecione **SAML ativada**. 

     b.  Clique em **Novo**.

12. A secção de SAML único início de sessão definições, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    a.In a caixa de texto do nome, escreva o nome da configuração (por ex.: *SPSSOWAAD\_teste*). 

    b. Colar **ID da entidade SMAL** valor para o **emissor** caixa de texto.

    c. No **Id de entidade** caixa de texto, tipo **https://test.salesforce.com** se for a primeira instância do Salesforce Sandbox que estiver a adicionar ao seu diretório. Se já adicionou uma instância do Salesforce Sandbox, em seguida, para o **ID de entidade** escreva a **URL de início de sessão**, que deve ter este formato:`http://company.my.salesforce.com`  
 
    d. Clique em **procurar** para carregar o certificado transferido.  

    e. Como **tipo de identidade de SAML**, selecione **asserção contém o ID de Federação do objeto User**.
 
    f. Como **SAML identidade localização**, selecione **é de identidade no elemento NameIdentifier da declaração de assunto**.

    g. Colar **URL Single Sign-On serviço** para o **URL de início de sessão do fornecedor de identidade** caixa de texto. 

    h. SFDC não suporta a fim de sessão SAML.  Como solução, cole 'https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0' para o **URL de fim de sessão do fornecedor de identidade** caixa de texto.

    posso. Como **fornecedor iniciada pedido vínculo de serviço**, selecione **HTTP POST**. 

    j. Clique em **Guardar**.

### <a name="enable-your-domain"></a>Ativar o seu domínio
Esta secção assume que já criou um domínio.  Para obter mais informações, consulte [definir o nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Para ativar o seu domínio, execute os seguintes passos:**

1. No painel de navegação esquerdo, clique em **gestão de domínios**e, em seguida, clique em **meu domínio.**
   
     ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >Certifique-se que o seu domínio foi configurado corretamente. 

2. No **definições de páginas de início de sessão** secção, clique em **editar**, em seguida, como **serviço de autenticação**, selecione o nome de SAML único início de sessão na definição da secção anterior e, finalmente, clique em **guardar**.
   
   ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

Assim que tiver um domínio configurado, os utilizadores devem utilizar o URL do domínio para início de sessão para a sandbox do Salesforce.  

Para obter o valor do URL, clique no perfil SSO que criou na secção anterior.    

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>Criar um utilizador de teste de Salesforce Sandbox

Nesta secção, um utilizador chamado Britta Simon é criado no Sandbox do Salesforce. Salesforce Sandbox suportam o aprovisionamento de just-in-time, que está ativada por predefinição.
Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe no Salesforce Sandbox, uma nova é criada quando tenta aceder a Salesforce Sandbox.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder acesso ao Sandbox do Salesforce.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Salesforce Sandbox, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Salesforce Sandbox**.

    ![Configurar o início de sessão único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Se pretender testar as definições de SSO, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o aprovisionamento de utilizadores](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png

