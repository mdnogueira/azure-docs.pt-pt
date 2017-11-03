---
title: "Tutorial: Integração do Azure Active Directory com SilkRoad vida Suite | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e SilkRoad vida Suite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
ms.openlocfilehash: ecf4e31ecea00d003fc47ea4cebb781ca58957f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Integração do Azure Active Directory com SilkRoad vida Suite
O objetivo deste tutorial é para lhe mostrar como integrar SilkRoad vida Suite com o Azure Active Directory (Azure AD). 

Integrar SilkRoad vida Suite com o Azure AD fornece as seguintes vantagens: 

* Pode controlar no Azure AD que tenha acesso ao SilkRoad vida Suite 
* Pode permitir que os utilizadores automaticamente obter com sessão iniciada para SilkRoad vida Suite-início de sessão único (SSO) com as respetivas contas do Azure AD

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com SilkRoad vida Suite, terá dos seguintes itens:

* Uma subscrição do Azure AD
* Uma subscrição de SilkRoad vida Suite SSO ativado

>[!NOTE]
>Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção. 
> 

Para testar os passos neste tutorial, deve seguir estas recomendações:

* Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
* Se não tiver um ambiente de avaliação do Azure AD, pode obter um [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir-lhe testar o SSO do Azure AD num ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar SilkRoad vida Suite na galeria do 
2. Configurar e testar o SSO do Azure AD

## <a name="add-silkroad-life-suite-from-the-gallery"></a>Adicionar SilkRoad vida Suite a partir da Galeria
Para configurar a integração do SilkRoad vida Suite com o Azure AD, tem de adicionar SilkRoad vida Suite na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar SilkRoad vida Suite a partir da galeria, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**. 
   
    ![Active Directory][1]

2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Aplicações][2]

4. Clique em **adicionar** na parte inferior da página.
   
    ![Aplicações][3]

5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **SilkRoad vida Suite**.
   
    ![Aplicações][5]

7. No painel de resultados, selecione **SilkRoad vida Suite**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
    ![Aplicações][50]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
O objetivo desta secção consiste em Mostrar-lhe como configurar e testar o SSO do Azure AD com SilkRoad vida Suite com base num utilizador de teste chamado "Britta Simon".

Para SSO funcionar, do Azure AD tem de saber o que é o utilizador homólogo SilkRoad vida Suite para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SilkRoad vida Suite tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no SilkRoad vida Suite.

Para configurar e testar o Azure AD-início de sessão único com SilkRoad vida Suite, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD-início de sessão único](#configuring-azure-ad-single-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste SilkRoad vida Suite](#creating-a-silkroad-life-suite-test-user)**  - para ter um homólogo de Britta Simon SilkRoad vida Suite que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único
O objetivo desta secção é para ativar a SSO do Azure AD no portal clássico do Azure e configurar o SSO na sua aplicação SilkRoad vida Suite.

**Para configurar o Azure AD-início de sessão único com SilkRoad vida Suite, execute os seguintes passos:**

1. Início de sessão no site da sua empresa SilkRoad como administrador. 

  >[!NOTE] 
  > Para obter acesso à aplicação SilkRoad vida Suite autenticação para configurar a Federação com o Microsoft Azure AD, contacte o suporte de SilkRoad ou o seu representante SilkRoad serviços.
  > 

2. Aceda a **fornecedor de serviços**e, em seguida, clique em **detalhes de Federação**. 
   
    ![Azure AD início de sessão único][10] 

3. Clique em **transferir metadados de Federação**e, em seguida, guarde o ficheiro de metadados no seu computador.
   
    ![Azure AD início de sessão único][11] 

4. No portal clássico do Azure, no **SilkRoad vida Suite** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar Single Sign-On** caixa de diálogo.
   
    ![Configurar o início de sessão único][6] 

5. No **como gostaria aos utilizadores iniciar sessão conjunto de vida de SilkRoad** página, selecione **do Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
    ![Azure AD início de sessão único][7] 

6. No **configurar definições de aplicação** diálogo página, execute os seguintes passos:
   
    ![Azure AD início de sessão único][8]   
 1. No **URL de início de sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para início de sessão para o seu site SilkRoad Suite de vida (por ex.: *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).  
 2. Abra o transferido **Silkroad** ficheiro de metadados. 
 3. Localize o **AssertionConsumerService** etiqueta e, em seguida, copie o **localização** atributo.         
   
    ![Azure AD início de sessão único][21] 
 4. Colar o valor para o **URL de resposta** caixa de texto.  
 5. Clique em **Seguinte**.

6. No **configurar o início de sessão único em SilkRoad vida Suite** página, execute os seguintes passos:
   
    ![Azure AD início de sessão único][9]  
 1. Clique em Transferir certificado e, em seguida, guarde o ficheiro no seu computador.  
 2. Clique em **Seguinte**.

7. No seu **SilkRoad** aplicação, clique em **autenticação origens**.
   
    ![Azure AD início de sessão único][12] 

8. Clique em **Adicionar origem de autenticação**. 
   
    ![Azure AD início de sessão único][13] 

9. No **Adicionar origem de autenticação** secção, execute os seguintes passos: 
   
    ![Azure AD início de sessão único][14]  
 1. Em **opção 2 - ficheiro de metadados**, clique em **procurar** para carregar o ficheiro de metadados transferido.  
 2. Clique em **criar fornecedor de identidade com dados de ficheiro**.

10. No **autenticação origens** secção, clique em **editar**. 
    
     ![Azure AD início de sessão único][15] 

11. No **Editar origem de autenticação** caixa de diálogo, execute os seguintes passos: 
    
     ![Azure AD início de sessão único][16] 
 1. Como **ativado**, selecione **Sim**.   
 2. No **IdP Descrição** caixa de texto, digite uma descrição para a sua configuração (por ex.: *SSO do Azure AD*).  
 3. No **IdP nome** caixa de texto, escreva um nome que é específica para a configuração (por ex.: *Azure SP*).  
 4. Clique em **Guardar**.

12. Desative todas as outras origens de autenticação. 
    
     ![Azure AD início de sessão único][17]

13. No portal clássico do Azure, no **único início de sessão confirmação** página, clique em **seguinte**.  
    
     ![Azure AD início de sessão único][18]

14. No **único início de sessão confirmação** página, clique em **concluída**.
    
     ![Azure AD início de sessão único][19]

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal clássico do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)  

2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu na parte superior, clique em **utilizadores**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 

4. Para abrir o **adicionar utilizador** caixa de diálogo, na barra de ferramentas na parte inferior, clique em **adicionar utilizador**. 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. No **diga-nos informações sobre este utilizador** diálogo página, execute os seguintes passos: 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)  
 1. Como tipo de utilizador, selecione o novo utilizador na sua organização.  
 2. O nome de utilizador **textbox**, tipo **BrittaSimon**. 
 3. Clique em **Seguinte**.

6. No **perfil de utilizador** diálogo página, execute os seguintes passos: 
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)  
 1. No **nome próprio** caixa de texto, tipo **Britta**.    
 2. No **Apelido** caixa de texto, tipo, **Simon**. 
 3. No **nome a apresentar** caixa de texto, tipo **Britta Simon**. 
 4. No **função** lista, selecione **utilizador**.
 5. Clique em **Seguinte**.

7. No **Get palavra-passe temporária** página da caixa de diálogo, clique em **criar**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 

8. No **Get palavra-passe temporária** diálogo página, execute os seguintes passos:
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)  
 1. Anote o valor da **nova palavra-passe**. 
 2. Clique em **Concluído**.   

### <a name="create-a-silkroad-life-suite-test-user"></a>Criar um utilizador de teste SilkRoad vida Suite
O objetivo desta secção consiste em criar um utilizador chamado Britta Simon no SilkRoad vida Suite. Britta tem de ter um ID de SSO (por vezes referido como um *AuthParam*) que corresponde à de Britta **emailaddress** no Azure AD.

**Para criar um utilizador chamado Britta Simon no SilkRoad vida Suite, execute os seguintes passos:**

- Peça a sua equipa de suporte de SilkRoad vida Suite para criar um utilizador que tenha como **SSO ID** atributo o mesmo valor que o **emailaddress** de Britta Simon no Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD
O objetivo desta secção consiste em Ativar Britta Simon utilizar o SSO do Azure ao conceder o acesso ao SilkRoad vida Suite.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a SilkRoad vida Suite, execute os seguintes passos:**

1. No portal clássico do Azure, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **SilkRoad vida Suite**.
   
    ![Atribua o utilizador][202] 

3. No menu na parte superior, clique em **utilizadores**.
   
    ![Atribua o utilizador][203] 

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
   
    ![Atribua o utilizador][205]

### <a name="test-single-sign-on"></a>Teste o início de sessão único
O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.  

Quando clica no mosaico SilkRoad vida Suite no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de SilkRoad vida Suite.

## <a name="additional-resources"></a>Recursos Adicionais
* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png





