---
title: "Tutorial: Integração do Azure Active Directory com Learning posto de trabalho LMS | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e LMS de postos de trabalho de aprendizagem."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bb056fcf-4135-478e-85b1-5015d1f07b85
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: jeedes
ms.openlocfilehash: 877e0288fdd1f590acf064c204aff0741539b112
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-learning-seat-lms"></a>Tutorial: Integração do Azure Active Directory com LMS de postos de trabalho de aprendizagem

Neste tutorial, irá aprender integrar LMS de postos de trabalho de aprendizagem do Azure Active Directory (Azure AD).

Integrar Learning LMS de postos de trabalho com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao LMS de postos de trabalho de aprendizagem
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Learning posto de trabalho LMS (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo. [O que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LMS de postos de trabalho do Learning, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Learning posto de trabalho LMS início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de aprendizagem posto de trabalho LMS na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-learning-seat-lms-from-the-gallery"></a>A adição de aprendizagem posto de trabalho LMS na galeria do
Para configurar a integração do Learning LMS de postos de trabalho com o Azure AD, tem de adicionar LMS de postos de trabalho de aprendizagem da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar LMS de postos de trabalho de aprendizagem da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Learning posto de trabalho LMS**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_search.png)

5. No painel de resultados, selecione **Learning posto de trabalho LMS**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com LMS de postos de trabalho do Learning com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Learning LMS de postos de trabalho é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Learning LMS de postos de trabalho tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor do **Username** no Learning LMS de postos de trabalho.

Para configurar e testar o Azure AD-início de sessão único com LMS de postos de trabalho do Learning, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de aprendizagem posto de trabalho LMS](#creating-a-learnconnect-test-user)**  - para ter um homólogo de Britta Simon no Learning LMS de postos de trabalho que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação LMS de postos de trabalho de aprendizagem.

**Para configurar do Azure AD-início de sessão único com LMS de postos de trabalho de aprendizagem, efetue os seguintes passos:**

1. No portal do Azure, no **Learning posto de trabalho LMS** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_samlbase.png)

3. No **Learning posto de trabalho LMS domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.learningseatlms.com`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.learningseatlms.com/Account/AssertionConsumerService`

4. Verifique **Mostrar avançadas definições de URL**, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_url2.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.learningseatlms.com`
     
    > [!NOTE] 
    > Estes valores não são os valores reais. Atualize estes valores com o identificador real, URL de resposta e URL de início de sessão. Contacte [equipa de suporte de postos de trabalho do Learning](http://help.learningseatlms.com/help) para obter estes valores. 

5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-learnconnect-tutorial/tutorial_general_400.png)

7. Para configurar o início de sessão único em **Learning posto de trabalho LMS** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte de postos de trabalho do Learning](http://help.learningseatlms.com/help).

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnconnect-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnconnect-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnconnect-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnconnect-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-learning-seat-lms-test-user"></a>Criar um utilizador de teste LMS de postos de trabalho de aprendizagem

Nesta secção, vai criar um utilizador chamado Britta Simon no Learning LMS de postos de trabalho. Contacte [equipa de suporte de postos de trabalho do Learning](http://help.learningseatlms.com/help) com todas as informações de utilizador para adicionar os utilizadores na aplicação LMS de postos de trabalho de aprendizagem.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para LMS de postos de trabalho de aprendizagem.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a LMS de postos de trabalho de aprendizagem, efetue os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Learning posto de trabalho LMS**.

    ![Configurar o início de sessão único](./media/active-directory-saas-learnconnect-tutorial/tutorial_learnconnect_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso. 

Clique no mosaico de aprendizagem posto de trabalho LMS no painel de acesso, irão ser automaticamente com sessão iniciada para a aplicação de aprendizagem LMS de postos de trabalho. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnconnect-tutorial/tutorial_general_203.png

