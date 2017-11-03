---
title: "Tutorial: Integração do Azure Active Directory com FirmPlay - empregado Advocacy para Recruiting | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e FirmPlay - empregado Advocacy para Recruiting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: 3cddd5b9508159089bf344dbb3882d462799747c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Tutorial: Integração do Azure Active Directory com FirmPlay - empregado Advocacy para Recruiting

Neste tutorial, irá aprender a integrar FirmPlay - empregado Advocacy para Recruiting com o Azure Active Directory (Azure AD).

Integrar FirmPlay - empregado Advocacy para Recruiting com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao FirmPlay - empregado Advocacy para Recruiting
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para FirmPlay - empregado Advocacy para Recruiting (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal de gestão do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com FirmPlay - empregado Advocacy para Recruiting, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um FirmPlay - empregado Advocacy para Recruiting início de sessão único subscrição ativado


> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar FirmPlay - empregado Advocacy para Recruiting a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Adicionar FirmPlay - empregado Advocacy para Recruiting a partir da Galeria
Para configurar a integração de FirmPlay - empregado Advocacy para Recruiting com o Azure AD, terá de adicionar FirmPlay - empregado Advocacy para Recruiting na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar FirmPlay - empregado Advocacy para Recruiting da galeria, execute os seguintes passos:**

1. No  **[Azure Management Portal](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **FirmPlay - empregado Advocacy para Recruiting**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_001.png)

5. No painel de resultados, selecione **FirmPlay - empregado Advocacy para Recruiting**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com FirmPlay - empregado Advocacy para Recruiting com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber que o utilizador de homólogo FirmPlay - empregado Advocacy para Recruiting for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no FirmPlay - empregado Advocacy para Recruiting tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no FirmPlay - empregado Advocacy para Recruiting.

Para configurar e testar o Azure AD-início de sessão único com FirmPlay - empregado Advocacy para Recruiting, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um FirmPlay - empregado Advocacy para o utilizador de teste de Recruiting](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**  - para ter um homólogo de Britta Simon FirmPlay: empregado Advocacy para Recruiting que está ligado a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal de gestão do Azure e configurar o início de sessão único na sua FirmPlay - empregado Advocacy Recruiting aplicação.

**Para configurar o Azure AD-início de sessão único com FirmPlay - empregado Advocacy para Recruiting, execute os seguintes passos:**

1. No portal de gestão do Azure, no **FirmPlay - empregado Advocacy para Recruiting** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, como **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_01.png)

3. No **FirmPlay - empregado Advocacy para o domínio de Recruiting e URLs** na secção de **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<your-subdomain>.firmplay.com/`

    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Tenha em atenção que isto não é o valor real. Tem de atualizar este valor com o início de sessão real no URL. Contacte [FirmPlay - empregado Advocacy para a equipa de suporte Recruiting](mailto:engineering@firmplay.com) para obter este valor. 

4. No **certificado de assinatura de SAML** secção, clique em **criar novo certificado**.

    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_03.png)   

5. No **criar o novo certificado** caixa de diálogo, clique no ícone de calendário e selecione um **data de expiração**. Em seguida, clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_general_300.png)

6. No **certificado de assinatura de SAML** secção, selecione **tornar o novo certificado ativa** e clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_04.png)

7. No pop-up **o certificado de Rollover** janela, clique em **OK**.

    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_general_400.png)

8. No **certificado de assinatura de SAML** secção, clique em **certificado (base64)** e, em seguida, guarde o ficheiro de certificado no seu computador. 

    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_05.png) 

9. No **FirmPlay - empregado Advocacy para configuração Recruiting** secção, clique em **configurar FirmPlay - empregado Advocacy para Recruiting** para abrir **configurar início de sessão** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_07.png)

10. Para obter SSO configurado para a sua aplicação, contacte [FirmPlay - empregado Advocacy para a equipa de suporte Recruiting](mailto:engineering@firmplay.com) e forneça-los com o seguinte: 

    • O transferido **ficheiro de certificado**

    • O **URL do serviço de início de sessão único SAML**

    • O **ID de entidade SAML**

    • O **URL de início de sessão**
  

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal de gestão do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-firmplay-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Criar um FirmPlay - empregado Advocacy para Recruiting utilizador de teste

Nesta secção, vai criar um utilizador chamado Britta Simon FirmPlay - empregado Advocacy para Recruiting. Consulte [FirmPlay - empregado Advocacy para a equipa de suporte Recruiting](mailto:engineering@firmplay.com) para adicionar os utilizadores no FirmPlay - empregado Advocacy Recruiting plataforma.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao FirmPlay - empregado Advocacy para Recruiting.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a FirmPlay - empregado Advocacy para Recruiting, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **FirmPlay - empregado Advocacy para Recruiting**.

    ![Configurar o início de sessão único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_50.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Ao clicar em FirmPlay - empregado Advocacy para Recruiting mosaico no painel de acesso, deve obter automaticamente com sessão iniciada para sua FirmPlay - empregado Advocacy Recruiting aplicação.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_203.png