---
title: "Tutorial: Integração do Azure Active Directory com @Task| Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e @Task."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: ebb19ca6cbaf04106fbce937d95651e709854cfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>Tutorial: Integração do Azure Active Directory com o@Task
O objetivo deste tutorial é para lhe mostrar como integrar @Task com o Azure Active Directory (Azure AD).  
Integrar @Task com o Azure AD fornece as seguintes vantagens: 

* Pode controlar no Azure AD que tenha acesso@Task
* Pode permitir aos utilizadores obter automaticamente com sessão iniciada para @Task (Single Sign-On) com as respetivas contas do Azure AD
* Pode gerir as contas numa localização central - Portal clássico do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com @Task, terá dos seguintes itens:

* Uma subscrição do Azure AD
* Um @Task início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.
> 
> 

Para testar os passos neste tutorial, deve seguir estas recomendações:

* Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
* Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir-lhe testar do Azure AD-início de sessão único num ambiente de teste.  
O cenário descrito neste tutorial consiste em três blocos modulares principais:

1. Adicionar @Task na galeria do 
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-task-from-the-gallery"></a>Adicionar @Task na galeria do
Para configurar a integração de @Task com o Azure AD, tem de adicionar @Task na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar @Task da galeria, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**. 
   
    ![Active Directory][1] 
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Aplicações][2] 
4. Clique em **adicionar** na parte inferior da página.
   
    ![Aplicações][3] 
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
    ![Aplicações][4] 
6. Na caixa de pesquisa, escreva  **@Task** .
   
    ![Aplicações][5] 
7. No painel de resultados, selecione  **@Task** e, em seguida, clique em **concluída** para adicionar a aplicação.
   
    ![Aplicações][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
O objetivo desta secção consiste em Mostrar-lhe como configurar e testar o Azure AD-início de sessão único com @Task com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber que o utilizador homólogo no @Task para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no @Task tem de ser estabelecida.   
Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no @Task.

Para configurar e testar o Azure AD-início de sessão único com @Task, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um @Tasktest utilizador](#creating-a-halogen-software-test-user)**  - para ter um homólogo de Britta Simon no @Taskthat está ligado a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único
O objetivo desta secção é para ativar o Azure AD início de sessão no portal clássico do Azure e configurar o início de sessão na sua @Task aplicação.

**Para configurar o Azure AD-início de sessão único com @Task, execute os seguintes passos:**

1. No portal clássico do Azure, no  **@Task**  página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar Single Sign-On** caixa de diálogo.
   
    ![Configurar o início de sessão único][6] 
2. No **como gostaria aos utilizadores iniciar sessão no @Task**  página, selecione **do Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
    ![Azure AD início de sessão único][7] 
3. No **configurar definições de aplicação** diálogo página, execute os seguintes passos:
   
    ![Configurar definições da aplicação][8] 
   
     a. No **URL de início de sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para início de sessão no seu @Task aplicação (por ex.:*https://<Tenant name>.attask ondemand.com*).
   
     b. Clique em **Seguinte**.
4. No **configurar início de sessão único em @Task**  página, clique em **transferir metadados**, guarde o ficheiro de metadados localmente no seu computador e, em seguida, clique em **seguinte**.
   
    ![O que é o Azure AD Connect][9] 
5. Início de sessão no seu @Task site da empresa como administrador.
6. Aceda a **na configuração de início de sessão único**.
7. No **Single Sign-On** caixa de diálogo, execute os passos seguintes
   
    ![Configurar o início de sessão único][23]
   
    a. Como **tipo**, selecione **SAML 2.0**.
   
    b. Selecione **ID do fornecedor de serviço**.
   
    c. No portal clássico do Azure, copie o **URL de início de sessão remoto**e, em seguida, cole-o para o **URL do Portal de início de sessão** caixa de texto.
   
    d. No portal clássico do Azure, copie o **único URL de serviço Sign-Out**e, em seguida, cole-o para o **Sign-Out URL** caixa de texto.
   
    e. No portal clássico do Azure, copie o **alterar palavra-passe URL**e, em seguida, cole-o para o **alterar palavra-passe URL** caixa de texto.
   
    f. Clique em **Guardar**.
8. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 
   
    ![O que é o Azure AD Connect][10]
9. No **único início de sessão confirmação** página, clique em **concluída**.  
   
    ![O que é o Azure AD Connect][11]

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal clássico do Azure chamado Britta Simon.  

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para apresentar a lista de utilizadores, no menu na parte superior, clique em **utilizadores**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. Para abrir o **adicionar utilizador** caixa de diálogo, na barra de ferramentas na parte inferior, clique em **adicionar utilizador**. 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. No **diga-nos informações sobre este utilizador** diálogo página, execute os seguintes passos: 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    a. Como tipo de utilizador, selecione o novo utilizador na sua organização.
   
    b. O nome de utilizador **textbox**, tipo **BrittaSimon**.
   
    c. Clique em **Seguinte**.
6. No **perfil de utilizador** diálogo página, execute os seguintes passos: 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
    a. No **nome próprio** caixa de texto, tipo **Britta**.  
   
    b. No **Apelido** caixa de texto, tipo, **Simon**.
   
    c. No **nome a apresentar** caixa de texto, tipo **Britta Simon**.
   
    d. No **função** lista, selecione **utilizador**.

    e. Clique em **Seguinte**.

7. No **Get palavra-passe temporária** página da caixa de diálogo, clique em **criar**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. No **Get palavra-passe temporária** diálogo página, execute os seguintes passos:
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **nova palavra-passe**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-task-test-user"></a>Criar um @Task utilizador de teste
O objetivo desta secção consiste em criar um utilizador chamado Britta Simon no @Task.

**Para criar um utilizador chamado Britta Simon @Task, execute os seguintes passos:**

1. Inicie sessão no seu @Task site da empresa como administrador.
2. No menu na parte superior, clique em **pessoas**.
3. Clique em **nova pessoa**. 
4. Na caixa de diálogo nova pessoa, execute os seguintes passos:
   
    ![Criar um @Task utilizador de teste][21] 
   
    a. No **nome próprio** caixa de texto, escreva "Britta".
   
    b. No **Apelido** caixa de texto, escreva "Simon".
   
    c. No **endereço de correio eletrónico** caixa de texto, escreva o endereço de correio eletrónico de Britta Simon no Azure Active Directory.
   
    d. Clique em **Adicionar pessoa**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD
O objetivo desta secção consiste em Ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao @Task.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon para @Task, execute os seguintes passos:**

1. No portal clássico do Azure, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Atribua o utilizador][201] 
2. Na lista de aplicações, selecione  **@Task** .
   
    ![Atribua o utilizador][202] 
3. No menu na parte superior, clique em **utilizadores**.
   
    ![Atribua o utilizador][203] 
4. Na lista de utilizadores, selecione **Britta Simon**.
5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
   
    ![Atribua o utilizador][205]

### <a name="testing-single-sign-on"></a>Teste o início de sessão único
O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.  
Ao clicar no @Task mosaico no painel de acesso, deve obter automaticamente iniciada no seu @Task aplicação.

## <a name="additional-resources"></a>Recursos Adicionais
* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






