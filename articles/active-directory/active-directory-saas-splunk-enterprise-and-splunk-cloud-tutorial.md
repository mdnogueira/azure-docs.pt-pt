---
title: "Tutorial: Integração do Azure Active Directory Splunk Enterprise e nuvem Splunk | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Splunk Enterprise e Splunk nuvem."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/09/2017
ms.author: jeedes
ms.openlocfilehash: b78e9b7161207a74880e912241d5e965b353d1c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Tutorial: Integração do Azure Active Directory Splunk Enterprise e Splunk nuvem

Neste tutorial, irá aprender integrar Splunk Enterprise e Splunk Cloud com o Azure Active Directory (Azure AD).

Integrar Splunk Enterprise e Splunk Cloud com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Splunk Enterprise e Splunk nuvem
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Splunk Enterprise e nuvem Splunk-início de sessão único (SSO) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássico do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Splunk Enterprise e Splunk nuvem, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Splunk empresarial ou na subscrição Splunk nuvem SSO ativado


>[!NOTE]
>Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.
>

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Splunk Enterprise e nuvem Splunk na galeria do
2. Configurar e testar o SSO do Azure AD


## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Adicionar Splunk Enterprise e nuvem Splunk na galeria do
Para configurar a integração do Splunk Enterprise e Splunk Cloud com o Azure AD, tem de adicionar Splunk Enterprise e nuvem Splunk na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Splunk Enterprise e nuvem Splunk na galeria do, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.

    ![Active Directory][1]

2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **adicionar** na parte inferior da página.

    ![Aplicações][3]

5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Splunk empresarial ou uma nuvem Splunk**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_01.png)

7. No painel de resultados, selecione **Splunk Enterprise e nuvem Splunk**e, em seguida, clique em **concluída** para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_02.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com Splunk empresarial e Splunk nuvem com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo Splunk Enterprise e Splunk nuvem é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Splunk Enterprise e Splunk nuvem tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor do **Username** Splunk Enterprise e Splunk nuvem.

Para configurar e testar o Azure AD-início de sessão único com Splunk Enterprise e Splunk nuvem, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD-início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Splunk Enterprise e nuvem Splunk](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)**  - para ter um homólogo de Britta Simon na empresa Splunk e nuvem Splunk que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar SSO do Azure AD no portal clássico e configurar SSO na sua aplicação Splunk Enterprise e Splunk nuvem.


**Para configurar o Azure AD-início de sessão único com Splunk Enterprise e nuvem Splunk, execute os seguintes passos:**

1. No portal clássico, no **Splunk Enterprise e nuvem Splunk** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar Single Sign-On**caixa de diálogo.
     
    ![Configurar o início de sessão único][6] 

2. No **como pretende que os utilizadores iniciem sessão Splunk Enterprise e da nuvem de Splunk** página, selecione **do Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o início de sessão único](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_03.png) 

3. No **configurar definições de aplicação** diálogo página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_04.png) 
  1. No **URL de início de sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para início de sessão à sua aplicação Splunk Enterprise e Splunk nuvem utilizando o padrão do seguinte:`https://<splunkserverUrl>/en-US/app/launcher/home`
  2. No **identificador** caixa de texto, escreva o URL do seu servidor Splunk.
  3. No **URL de resposta** caixa de texto, escreva o URL com o padrão do seguinte:`https://<splunkserver>/saml/acs`
  4. Clique em **Seguinte**.
 
4. No **configurar o início de sessão único em Splunk Enterprise e nuvem Splunk** página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_05.png)
  1. Clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador.
  2. Clique em **Seguinte**.

5. Para obter SSO configurado para a sua aplicação, contacte Splunk empresarial e a equipa de suporte de nuvem Splunk e forneça-los com o seguinte:

    * O transferido **federaton metadados**
6. No portal clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD início de sessão único][10]

7. No **único início de sessão confirmação** página, clique em **concluída**.  
 
    ![Azure AD início de sessão único][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
Nesta secção, vai criar um utilizador de teste no portal clássico do chamado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_09.png) 

2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu na parte superior, clique em **utilizadores**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_03.png) 

4. Para abrir o **adicionar utilizador** caixa de diálogo, na barra de ferramentas na parte inferior, clique em **adicionar utilizador**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_04.png) 

5. No **diga-nos informações sobre este utilizador** diálogo página, execute os seguintes passos:

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_05.png) 
  1. Como tipo de utilizador, selecione o novo utilizador na sua organização.
  2. O nome de utilizador **textbox**, tipo **BrittaSimon**.
  3. Clique em **Seguinte**.

6.  No **perfil de utilizador** diálogo página, execute os seguintes passos:
  
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_06.png) 
  1. No **nome próprio** caixa de texto, tipo **Britta**.  
  2. No **Apelido** caixa de texto, tipo, **Simon**.
  3. No **nome a apresentar** caixa de texto, tipo **Britta Simon**.
  4. No **função** lista, selecione **utilizador**.
  5. Clique em **Seguinte**.

7. No **Get palavra-passe temporária** página da caixa de diálogo, clique em **criar**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_07.png) 

8. No **Get palavra-passe temporária** diálogo página, execute os seguintes passos:

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **nova palavra-passe**.
  2. Clique em **Concluído**.   

### <a name="create-a-splunk-enterprise-and-splunk-cloud-test-user"></a>Criar um Splunk Enterprise e o utilizador de teste de nuvem Splunk

Nesta secção, vai criar um utilizador chamado Britta Simon na empresa Splunk e Splunk nuvem. . Funciona com Splunk Enterprise e equipa de suporte de nuvem Splunk para adicionar os utilizadores na plataforma Splunk Enterprise e Splunk nuvem.


### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar SSOy Azure conceder o acesso ao Splunk Enterprise e Splunk nuvem.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon para a empresa Splunk e nuvem Splunk, execute os seguintes passos:**

1. No portal clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Splunk Enterprise e nuvem Splunk**.

    ![Configurar o início de sessão único](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_50.png) 

3. No menu na parte superior, clique em **utilizadores**.

    ![Atribua o utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribua o utilizador][205]

### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, teste o SSOonfiguration do AD do Azure através do painel de acesso.

Quando clicar em empresa Splunk e nuvem Splunk na peça de mosaico do painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação Splunk Enterprise e Splunk nuvem.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_205.png
