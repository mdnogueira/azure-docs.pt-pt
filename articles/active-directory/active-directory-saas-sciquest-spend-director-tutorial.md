---
title: "Tutorial: Integração do Azure Active Directory com SciQuest gastam Director | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e SciQuest gastam Director."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
ms.openlocfilehash: 84b707668dc45e92e6151f422f1c919f638533b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Tutorial: Integração do Azure Active Directory com SciQuest gastam Director
O objetivo deste tutorial é para lhe mostrar como integrar SciQuest gastam Director com o Azure Active Directory (Azure AD).  
Integrar SciQuest gastam Director com o Azure AD fornece as seguintes vantagens: 

* Pode controlar no Azure AD que tenha acesso ao SciQuest gastam Director 
* Pode permitir que os utilizadores automaticamente obter com sessão iniciada para SciQuest gastam Director (Single Sign-On) com as respetivas contas do Azure AD
* Pode gerir as contas numa localização central - portal clássico do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com SciQuest gastam Director, terá dos seguintes itens:

* Uma subscrição do Azure AD
* Um SciQuest gastam Director início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.
> 
> 

Para testar os passos neste tutorial, deve seguir estas recomendações:

* Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
* Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir-lhe testar do Azure AD-início de sessão único num ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar SciQuest Director gastam a partir da Galeria 
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Adicionar SciQuest Director gastam a partir da Galeria
Para configurar a integração de SciQuest gastam Director com o Azure AD, tem de adicionar SciQuest gastam Director na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar SciQuest gastam Director na galeria do, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**. 
   
    ![Active Directory][1]

2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Aplicações][2]

4. Clique em **adicionar** na parte inferior da página.
   
    ![Aplicações][3]

5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **sciQuest gastam director**.
   
    ![Aplicações][5]

7. No painel de resultados, selecione **SciQuest gastam Director**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
    ![Aplicações][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
O objetivo desta secção consiste em Mostrar-lhe como configurar e testar o Azure AD-início de sessão único com SciQuest gastam Director com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que é o utilizador homólogo SciQuest gastam Director para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SciQuest gastam Director tem de ser estabelecida.  
Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor do **Username** no SciQuest gastam Director.

Para configurar e testar o Azure AD-início de sessão único com SciQuest gastam Director, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD único Single Sign-On](#configuring-azure-ad-single-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste SciQuest gastam Director](#creating-a-halogen-software-test-user)**  - para ter um homólogo de Britta Simon SciQuest gastam Director que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurar o Azure AD único início de sessão único
O objetivo desta secção é para ativar o Azure AD início de sessão no portal clássico do Azure e configurar o início de sessão único na sua aplicação SciQuest gastam Director.

**Para configurar o Azure AD-início de sessão único com SciQuest gastam Director, execute os seguintes passos:**

1. No portal clássico do Azure, no **SciQuest gastam Director** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar Single Sign-On**  caixa de diálogo.
   
    ![Configurar o início de sessão único][8]

2. No **como gostaria aos utilizadores iniciar sessão SciQuest gastam Director** página, selecione **do Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
    ![Azure AD início de sessão único][9]

3. No **configurar definições de aplicação** diálogo página, execute os seguintes passos: 
   
    ![Configurar definições da aplicação][10]
   
     a. No **URL de início de sessão** caixa de texto, escreva o URL é utilizado pelos seus utilizadores para iniciar sessão aplicação SciQuest gastam Director através do padrão de seguinte: *https://.* sciquest.com/.**
   
     b. No **URL de resposta** caixa de texto, escreva o mesmo valor que foi escrito para o **URL de início de sessão** caixa de texto. 
   
     c. Clique em **Seguinte**.

4. No **configurar o início de sessão único em SciQuest gastam Director** página, clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.
   
    ![O que é o Azure AD Connect][11]

5. SciQuest contacte o suporte para ativar este método de autenticação com os metadados de transferido acima.

6. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída** para fechar o **configurar início de sessão único** caixa de diálogo. 
   
    ![O que é o Azure AD Connect][15]

7. No **único início de sessão confirmação** página, clique em **concluída**.  

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal clássico do Azure chamado Britta Simon.

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![O que é o Azure AD Connect][100] 

2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu na parte superior, clique em **utilizadores**.
   
    ![O que é o Azure AD Connect][101] 

4. Para abrir o **adicionar utilizador** caixa de diálogo, na barra de ferramentas na parte inferior, clique em **adicionar utilizador**. 
   
    ![O que é o Azure AD Connect][102] 

5. No **diga-nos informações sobre este utilizador** diálogo página, execute os seguintes passos:
   
    ![O que é o Azure AD Connect][103] 
   
    a. Como **tipo de utilizador**, selecione **novo utilizador na sua organização**.
   
    b. O nome de utilizador **textbox**, tipo **BrittaSimon**.
   
    c. Clique em **Seguinte**.

6. No **perfil de utilizador** diálogo página, execute os seguintes passos: 
   
    ![O que é o Azure AD Connect][104] 
   
    a. No **nome próprio** caixa de texto, tipo **Britta**.  
   
    b. No **Apelido** txtbox, tipo, **Simon**.
   
    c. No **nome a apresentar** caixa de texto, tipo **Britta Simon**.
   
    d. No **função** lista, selecione **utilizador**.
   
    e. Clique em **Seguinte**.

7. No **Get palavra-passe temporária** página da caixa de diálogo, clique em **criar**.
   
    ![O que é o Azure AD Connect][105]  

8. No **Get palavra-passe temporária** diálogo página, execute os seguintes passos:
   
    ![O que é o Azure AD Connect][106]   
   
    a. Anote o valor da **nova palavra-passe**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-sciquest-spend-director-test-user"></a>Criar um utilizador de teste SciQuest gastam Director
O objetivo desta secção consiste em criar um utilizador chamado Britta Simon SciQuest gastam Director.

Terá de contactar a equipa de suporte SciQuest gastam Director e forneça-los com os detalhes sobre a sua conta de teste para obtê-lo a criar.

Em alternativa, pode também tirar partido just-in-time aprovisionamento, uma único início de sessão funcionalidade que é suportada pelo SciQuest gastam Director.  
Se just-in-time o aprovisionamento estiver ativado, os utilizadores são automaticamente criados por SciQuest gastam Director durante a tentativa de início de sessão único caso não existam. Esta funcionalidade elimina a necessidade de criar manualmente utilizadores homólogo de início de sessão único.

Para obter just-in-time aprovisionamento ativada, terá de contactar a sua equipa de suporte SciQuest gastam Director.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD
O objetivo desta secção consiste em Ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao SciQuest gastam Director.

![O que é o Azure AD Connect][200]

**Para atribuir Britta Simon a SciQuest gastam Director, execute os seguintes passos:**

1. No portal clássico do Azure, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![O que é o Azure AD Connect][201]

2. Na lista de aplicações, selecione **SciQuest gastam Director**.
   
    ![O que é o Azure AD Connect][202]

3. No menu na parte superior, clique em **utilizadores**.
   
    ![O que é o Azure AD Connect][203]

4. Na lista de utilizadores, selecione **Britta Simon**.
   
    ![O que é o Azure AD Connect][204]

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
   
    ![O que é o Azure AD Connect][205]

### <a name="testing-single-sign-on"></a>Teste o início de sessão único
O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.  
Quando clica no mosaico SciQuest gastam Director no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de SciQuest gastam Director.

## <a name="additional-resources"></a>Recursos Adicionais
* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_20.png

