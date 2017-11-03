---
title: "Tutorial: Integração do Azure Active Directory com Halosys | Microsoft Docs"
description: "Saiba como utilizar Halosys com o Azure Active Directory para ativar o início de sessão único, aprovisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 42a0eb7c-5cb7-44a9-b00b-b0e7df4b63e8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 18c5cd8eb4ca211f8ae2b8dd994c0e8c48625a2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-halosys"></a>Tutorial: Integração do Azure Active Directory com Halosys

Neste tutorial, irá aprender a integrar Halosys com o Azure Active Directory (Azure AD).

Integrar Halosys com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Halosys
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Halosys (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássico do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Halosys, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Halosys início de sessão único subscrição ativado


> [!NOTE] 
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Halosys a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-


## <a name="adding-halosys-from-the-gallery"></a>Adicionar Halosys a partir da Galeria
Para configurar a integração de Halosys com o Azure AD, terá de adicionar Halosys a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Halosys a partir da galeria, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.

    ![Active Directory][1]
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **adicionar** na parte inferior da página.

    ![Aplicações][3]

5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Halosys**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_01.png)
    
7. No painel de resultados, selecione **Halosys**e, em seguida, clique em **concluída** para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_011.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Halosys com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Halosys é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Halosys tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Halosys.

Para configurar e testar o Azure AD-início de sessão único com Halosys, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Halosys](#creating-a-halosys-test-user)**  - para ter um homólogo de Britta Simon Halosys que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal clássico e configurar o início de sessão único na sua aplicação Halosys.


**Para configurar o Azure AD-início de sessão único com Halosys, execute os seguintes passos:**

1. No portal clássico, no **Halosys** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar Single Sign-On** caixa de diálogo.
     
    ![Configurar o início de sessão único][6] 

2. No **como gostaria aos utilizadores iniciar sessão Halosys** página, selecione **do Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o início de sessão único](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_03.png) 

3. No **configurar definições de aplicação** diálogo página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_04.png) 

    a. No **URL de início de sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para início de sessão para a aplicação de Halosys através do padrão de seguinte: `https://<company-name>.Halosys.com/client-api/api`.

    b.In o **URL de identificador** caixa de texto, escreva o URL no seguinte padrão: `https://<company-name>.Halosys.com`.   
         
4. No **configurar o início de sessão único em Halosys** página, clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador:

    ![Configurar o início de sessão único](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_05.png)
   
5. Para obter SSO configurado para a sua aplicação, contacte a equipa de suporte de Halosys e forneça-los com o seguinte:

    • O transferido **ficheiro de metadados**
    
    • O **URL SAML SSO**
    

6. No portal clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD início de sessão único][10]

7. No **único início de sessão confirmação** página, clique em **concluída**.  
 
    ![Azure AD início de sessão único][11]


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
Nesta secção, vai criar um utilizador de teste no portal clássico do chamado Britta Simon.


![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_09.png) 

2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu na parte superior, clique em **utilizadores**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_03.png) 

4. Para abrir o **adicionar utilizador** caixa de diálogo, na barra de ferramentas na parte inferior, clique em **adicionar utilizador**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_04.png) 

5. No **diga-nos informações sobre este utilizador** diálogo página, execute os seguintes passos: ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_05.png) 

    a. Como tipo de utilizador, selecione o novo utilizador na sua organização.

    b. O nome de utilizador **textbox**, tipo **BrittaSimon**.

    c. Clique em **Seguinte**.

6.  No **perfil de utilizador** diálogo página, execute os seguintes passos: ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_06.png) 

    a. No **nome próprio** caixa de texto, tipo **Britta**.  

    b. No **Apelido** caixa de texto, tipo, **Simon**.

    c. No **nome a apresentar** caixa de texto, tipo **Britta Simon**.

    d. No **função** lista, selecione **utilizador**.

    e. Clique em **Seguinte**.

7. No **Get palavra-passe temporária** página da caixa de diálogo, clique em **criar**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_07.png) 

8. No **Get palavra-passe temporária** diálogo página, execute os seguintes passos:

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-Halosys-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **nova palavra-passe**.

    b. Clique em **Concluído**.   



### <a name="creating-a-halosys-test-user"></a>Criar um utilizador de teste Halosys

Nesta secção, vai criar um utilizador chamado Britta Simon Halosys. . Funciona com a equipa de suporte de Halosys para adicionar os utilizadores na plataforma Halosys.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao Halosys.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Halosys, execute os seguintes passos:**

1. No portal clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Halosys**.

    ![Configurar o início de sessão único](./media/active-directory-saas-Halosys-tutorial/tutorial_Halosys_50.png) 

3. No menu na parte superior, clique em **utilizadores**.

    ![Atribua o utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribua o utilizador][205]


### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Halosys no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Halosys.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-Halosys-tutorial/tutorial_general_205.png
