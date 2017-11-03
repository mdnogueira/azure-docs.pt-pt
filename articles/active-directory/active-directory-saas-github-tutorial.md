---
title: "Tutorial: Integração do Azure Active Directory com o GitHub | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e do GitHub."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 9dc12bc2e313bcb2000724d035156c5054d14e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Tutorial: Integração do Azure Active Directory com o GitHub

Neste tutorial, irá aprender a integrar o GitHub com o Azure Active Directory (Azure AD).

Integrar o GitHub com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao GitHub
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada no GitHub (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal de gestão do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o GitHub, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um GitHub início de sessão único subscrição ativado


> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar GitHub a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-


## <a name="adding-github-from-the-gallery"></a>Adicionar GitHub a partir da Galeria
Para configurar a integração do GitHub com o Azure AD, terá de adicionar GitHub a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar GitHub a partir da galeria, execute os seguintes passos:**

1. No  **[Azure Management Portal](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **GitHub.com**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. No painel de resultados, selecione **GitHub**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o GitHub com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no GitHub é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no GitHub tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no GitHub.

Para configurar e testar o Azure AD-início de sessão único com o GitHub, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do GitHub](#creating-a-GitHub-test-user)**  - para ter um homólogo de Britta Simon no GitHub que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal de gestão do Azure e configurar o início de sessão único na sua aplicação do GitHub.

**Para configurar o Azure AD-início de sessão único com o GitHub, execute os seguintes passos:**

1. No portal de gestão do Azure, no **GitHub** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, como **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. No **domínio GitHub e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. No **URL de início de sessão** caixa de texto, digite o valor como:`https://github.com/orgs/<entity-id>/sso`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > Tenha em atenção que estas não são os valores reais. Tem de atualizar estes valores com o URL de início de iniciar sessão no real e o identificador. Aqui sugerimos que utilize o valor de cadeia exclusivo no identificador de. Aceda à secção de administração do GitHub para obter estes valores. 

4. No **atributos de utilizador** secção, selecione **identificador de utilizador** como user.mail.

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. No **certificado de assinatura de SAML** secção, clique em **criar novo certificado**.

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. No **criar o novo certificado** caixa de diálogo, clique no ícone de calendário e selecione um **data de expiração**. Em seguida, clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. No **certificado de assinatura de SAML** secção, selecione **tornar o novo certificado ativa** e clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. No pop-up **o certificado de Rollover** janela, clique em **OK**.

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. No **configuração de GitHub** secção, clique em **configurar GitHub** para abrir **configurar início de sessão** janela.

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. Numa janela do browser web diferente, inicie sessão no seu site do GitHub organização como um administrador.

12. Navegue para **definições** e clique em **segurança**

    ![Definições](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. Verifique o **autenticação ativar SAML** caixa, revelando os campos de configuração-início de sessão único. Em seguida, utilize o início de sessão URL valor único para atualizar o URL Single sign-on na configuração do Azure AD.

    ![Definições](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. Configure os seguintes campos:

    a. **Inicie sessão no URL**: introduza **SAML-início de sessão único URL de serviço** do **configurar GitHub** secção sobre o Azure AD

    b. **Emissor**: introduza **ID de entidade de SAML** do **configurar GitHub** secção sobre o Azure AD

    c. **Certificado público**: Abra o certificado transferido do Azure AD num bloco de notas e copie o conteúdo, incluindo "Começar CERTIFICATE" e "END CERTIFICATE"

    ![Definições](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. Clique em **configuração SAML do teste** para confirmar que não existem falhas de validação ou erros durante a SSO.

    ![Definições](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. Clique em **guardar**

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal de gestão do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**. 


### <a name="creating-a-github-test-user"></a>Criar um utilizador de teste do GitHub

Para permitir que os utilizadores do Azure AD inicie sessão no GitHub, tem de ser aprovisionados no GitHub.  
No caso do GitHub, o aprovisionamento é uma tarefa manual.

**Aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa GitHub como administrador.

2. Clique em **pessoas**.

    ![As pessoas](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "pessoas")

3. Clique em **convite membro**.

    ![Convidar utilizadores](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "convidar utilizadores")

4. No **convite membro** diálogo página, execute os seguintes passos:

    a. No **E-Mail** caixa de texto, escreva o endereço de correio eletrónico da conta de Britta Simon.

    ![Convidar pessoas](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "convidar pessoas")
    
    b. Clique em **enviar o convite**.

    ![Convidar pessoas](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "convidar pessoas")

    > [!NOTE]
    > O marcador de posição de conta do Azure Active Directory irá receber um e-mail e siga uma ligação para confirmar a respetiva conta para ficar ativa.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao GitHub.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon ao GitHub, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **GitHub.com**.

    ![Configurar o início de sessão único](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do GitHub no painel de acesso, deve obter com sessão iniciada para a aplicação do GitHub. Será possível iniciar sessão como uma organização de conta, mas, em seguida, tem de iniciar sessão com a sua conta pessoal.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
