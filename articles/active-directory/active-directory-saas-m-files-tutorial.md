---
title: "Tutorial: Integração do Azure Active Directory com ficheiros M | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e M ficheiros."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 0f2682cf7cd3e11a5a7156938fbe9d4c7f541312
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Tutorial: Integração do Azure Active Directory com ficheiros M

Neste tutorial, irá aprender a integrar M ficheiros com o Azure Active Directory (Azure AD).

Integrar M ficheiros com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso aos ficheiros de M
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada a M-ficheiros (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ficheiros M, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um milhão ficheiros-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar ficheiros de M na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-m-files-from-the-gallery"></a>Adicionar ficheiros de M na galeria do
Para configurar a integração dos ficheiros M com o Azure AD, tem de adicionar ficheiros de M na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar ficheiros de M a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **M ficheiros**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_search.png)

5. No painel de resultados, selecione **M ficheiros**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com M-ficheiros com base num utilizador teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo nos ficheiros de M é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos ficheiros de M tem de ser estabelecida.

Nos ficheiros de M, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com ficheiros M, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de ficheiros M](#creating-a-m-files-test-user)**  - para ter um homólogo de Britta Simon nos ficheiros de M que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação M ficheiros.

**Para configurar o Azure AD-início de sessão único com ficheiros M, execute os seguintes passos:**

1. No portal do Azure, no **M ficheiros** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_samlbase.png)

3. No **M ficheiros domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de ficheiros de M](mailto:support@m-files.com) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-m-files-tutorial/tutorial_general_400.png)

6. Para obter SSO configurado para a sua aplicação, contacte [equipa de suporte de ficheiros M](mailto:support@m-files.com) e forneça os metadados transferido.
   
    >[!NOTE]
    >Siga os passos seguintes se quiser configurar o SSO para a aplicação de ambiente de trabalho de ficheiro M. Não existem passos adicionais são necessários se pretender apenas configurar o SSO para a versão do M ficheiros web.  

7. Siga os passos seguintes para configurar a aplicação de ambiente de trabalho de ficheiro M para ativar a SSO com o Azure AD. Para transferir ficheiros M, visite [transferir ficheiros M](https://www.m-files.com/en/download-latest-version) página.

8. Abra o **definições de ambiente de trabalho de ficheiros de M** janela. Em seguida, clique em **adicionar**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)

9. No **propriedades de ligação do documento cofre** janela, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)  

    Sob o servidor secção tipo, os valores da seguinte forma:  

    a. Para **nome**, tipo `<tenant-name>.cloudvault.m-files.com`. 
 
    b. Para **número da porta**, tipo **4466**. 

    c. Para **protocolo**, selecione **HTTPS**. 

    d. No **autenticação** campo, selecione **utilizador do Windows específicos**. Em seguida, serão apresentadas uma página de assinatura. Insira as credenciais do Azure AD. 

    e. Para o **cofre no servidor**, selecione o Cofre correspondente no servidor.
 
    f. Clique em **OK**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-m-files-test-user"></a>Criar um utilizador de teste de ficheiros M

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon nos ficheiros de M. Trabalhar com [equipa de suporte de ficheiros M](mailto:support@m-files.com) para adicionar os utilizadores nos ficheiros de M.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso aos ficheiros de M.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a M ficheiros, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **M ficheiros**.

    ![Configurar o início de sessão único](./media/active-directory-saas-m-files-tutorial/tutorial_m-files_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico de ficheiros M no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de ficheiros M.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png

