---
title: "Tutorial: Integração do Azure Active Directory com o Procore SSO | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Procore SSO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 042a41eaa6bb21670b4c12068f1b017222f64b99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Integração do Azure Active Directory com o Procore SSO

Neste tutorial, irá aprender a integrar Procore SSO ao Azure Active Directory (Azure AD).

Integrar o Procore SSO com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Procore SSO
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Procore SSO (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal de gestão do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Procore SSO, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Procore SSO.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Procore SSO, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Procore SSO início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de Procore SSO de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-procore-sso-from-the-gallery"></a>A adição de Procore SSO de galeria
Para configurar a integração do Procore SSO com o Azure AD, tem de adicionar Procore SSO na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Procore SSO a partir da galeria, execute os seguintes passos:**

1. No  **[Azure Management Portal](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Procore SSO**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. No painel de resultados, selecione **Procore SSO**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o SSO Procore com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo em Procore SSO é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em Procore SSO tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** em Procore SSO.

Para configurar e testar o Azure AD-início de sessão único com o Procore SSO, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de Procore SSO](#creating-a-procore-sso-test-user)**  - para ter um homólogo de Britta Simon Procore SSO que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal de gestão do Azure e configurar o início de sessão único na sua aplicação Procore SSO.

**Para configurar o Azure AD-início de sessão único com o Procore SSO, execute os seguintes passos:**

1. No portal de gestão do Azure, no **Procore SSO** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. No **Procore de SSO de domínio e os URLs** secção, o utilizador não tem de efetuar quaisquer passos, tal como a aplicação já está pré-integrada com o Azure.

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. No **Procore SSO configuração** secção, clique em **configurar Procore SSO** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Para configurar o início de sessão único em **Procore SSO** lado, inicie sessão no site da sua empresa procore como administrador.

8. Na lista de caixa de ferramentas pendente, clique em **Admin** para abrir a página de definições do SSO.

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. Cole os valores nas caixas, conforme descrito abaixo-

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. No **único início de sessão no URL do emissor** caixa, cole o ID de entidade de SAML copiado a partir do portal do Azure.

    b. No **sessão SAML no URL de destino** caixa, cole o único início de sessão no URL do serviço SAML copiado a partir do portal do Azure.

    c. Agora abra o **XML de metadados** transferido acima, a partir do portal do Azure e copie o certficate na etiqueta com o nome **certificado X509**. Colar o valor copiado para o **certificado de início de sessão único x509** caixa.

10. Clique em **guardar alterações**.

11. Depois destas definições, tem de enviar o **nome de domínio** (por exemplo **contoso.com**) através do qual está a iniciar sessão em Procore para o [equipa de suporte Procore](https://support.procore.com/) e irá ativar a SSO federado desse domínio.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal de gestão do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-procore-sso-test-user"></a>Criar um utilizador de teste de Procore SSO

Siga os passos abaixo para criar um utilizador de teste Procore no seu lado.

1. Início de sessão para o site da sua empresa procore como administrador.  

2. Na lista de caixa de ferramentas pendente, clique em **diretório** para abrir a página do diretório da empresa.

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. Clique em **adicionar uma pessoa** opção para abrir o formulário e introduza efetuar os seguintes opções -

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. No **nome próprio** caixa de texto, nome do próprio do utilizador do tipo como **Britta**.

    b. No **Apelido** caixa de texto, apelido do utilizador do tipo como **Simon**.

    c. No **endereço de correio eletrónico** como endereço de correio eletrónico do utilizador do tipo de caixa de texto,  **BrittaSimon@contoso.com** .

    d. Selecione **permissão modelo** como **aplicar o modelo de permissão mais tarde**.

    e. Clique em **Criar**.

4. Verifique e atualize os detalhes para o contacto adicionado recentemente.

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. Clique em **guardar e enviar Invitiation** (se for necessário um convite através de correio) ou **guardar** (guardar diretamente) para concluir o registo de utilizador.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao Procore SSO.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon para Procore SSO, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Procore SSO**.

    ![Configurar o início de sessão único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Se pretender testar as definições de início de sessão único, abra o painel de acesso. Para obter mais detalhes sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586). Quando clica no mosaico Procore SSO no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Procore SSO.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png

