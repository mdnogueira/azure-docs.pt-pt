---
title: "Tutorial: Integração do Azure Active Directory com Datahug | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Datahug."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: ec431dd5ccfa53e4b975e46da247704dd1e15c2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Tutorial: Integração do Azure Active Directory com Datahug

Neste tutorial, irá aprender a integrar Datahug com o Azure Active Directory (Azure AD).

Integrar Datahug com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Datahug
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Datahug (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo. [O que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Datahug, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Datahug início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Datahug a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-datahug-from-the-gallery"></a>Adicionar Datahug a partir da Galeria
Para configurar a integração de Datahug com o Azure AD, terá de adicionar Datahug a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Datahug a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Datahug**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_search.png)

5. No painel de resultados, selecione **Datahug**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Datahug com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Datahug é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Datahug tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Datahug.

Para configurar e testar o Azure AD-início de sessão único com Datahug, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Datahug](#creating-a-datahug-test-user)**  - para ter um homólogo de Britta Simon Datahug que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Datahug.

**Para configurar o Azure AD-início de sessão único com Datahug, execute os seguintes passos:**

1. No portal do Azure, no **Datahug** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_samlbase.png)

3. No **Datahug domínio e os URLs** secção, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_ur1.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://apps.datahug.com/identity/<uniqueID>`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://apps.datahug.com/identity/<uniqueID>/acs`

4. Verifique **Mostrar avançadas definições de URL**. Se pretender configurar a aplicação no **SP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_url2.png)

    No **URL de início de sessão** caixa de texto, escreva um URL como:`https://apps.datahug.com/`
     
    > [!NOTE] 
    > Estes valores não estiverem a real. Atualize estes valores com o identificador e o URL de resposta real. Aqui sugerimos que utilize o valor de cadeia com o identificador e o URL de resposta exclusivo. Contacte [equipa de suporte de cliente Datahug](http://datahug.com/about/contact-us/) para obter estes valores. 

5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_certificate.png) 

6.  Verifique **"Mostrar avançadas definições de assinatura de certificado"** e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_cert.png)

    a. No **assinatura opção**, selecione **asserção SAML de início de sessão**.
    
    b. No **algoritmo de assinatura**, selecione **SHA1**.
 
7. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-datahug-tutorial/tutorial_general_400.png)
    
8. No **Datahug configuração** secção, clique em **configurar Datahug** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML** e **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_configure.png) 

9. Para configurar o início de sessão único em **Datahug** lado, terá de enviar o transferido **XML de metadados**, **ID de entidade de SAML** e **único início de sessão no URL do serviço SAML** para [Datahug suporte](http://datahug.com/about/contact-us/). Se configuraram esta aplicação para que a ligação de SAML SSO corretamente em ambos os lados.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-datahug-test-user"></a>Criar um utilizador de teste Datahug

Para permitir que os utilizadores do Azure AD iniciem sessão nos Datahug, têm de ser aprovisionados para Datahug.  
Quando Datahug, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Datahug como administrador.

2. Coloque o cursor sobre o **roda dentada** no canto superior direito e clique **definições**
   
   ![Adicionar empregado](./media/active-directory-saas-datahug-tutorial/1.png)

3. Escolha **pessoas** e clique em de **adicionar utilizadores** separador

    ![Adicionar empregado](./media/active-directory-saas-datahug-tutorial/2.png)

4. Escreva a mensagem de e-mail da pessoa que gostaria de criar uma conta para e clique em **adicionar**.

    ![Adicionar empregado](./media/active-directory-saas-datahug-tutorial/3.png)

    > [!NOTE] 
    > Pode enviar correio de registo para o utilizador selecionando **enviar correio eletrónico de boas-vindas** caixa de verificação.  
    > Se estiver a criar uma conta do Salesforce não enviar o e-mail de boas-vindas.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Datahug.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Datahug, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Datahug**.

    ![Configurar o início de sessão único](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.
Quando clica no mosaico Datahug no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Datahug. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_203.png

