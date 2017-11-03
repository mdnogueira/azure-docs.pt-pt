---
title: "Tutorial: Integração do Azure Active Directory com BetterWorks | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e BetterWorks."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5bb9505a-be02-46ae-9979-5308715d2b47
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: d6a5b167c0befbd0fe2c65bdd16abc35ed0a659c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Tutorial: Integração do Azure Active Directory com BetterWorks

Neste tutorial, irá aprender a integrar BetterWorks com o Azure Active Directory (Azure AD).

Integrar BetterWorks com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao BetterWorks
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para BetterWorks (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com BetterWorks, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um BetterWorks início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar BetterWorks a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-betterworks-from-the-gallery"></a>Adicionar BetterWorks a partir da Galeria
Para configurar a integração de BetterWorks com o Azure AD, terá de adicionar BetterWorks a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar BetterWorks a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **BetterWorks**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_search.png)

5. No painel de resultados, selecione **BetterWorks**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com BetterWorks com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no BetterWorks é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no BetterWorks tem de ser estabelecida.

No BetterWorks, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com BetterWorks, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste BetterWorks](#creating-a-betterworks-test-user)**  - para ter um homólogo de Britta Simon BetterWorks que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação BetterWorks.

**Para configurar o Azure AD-início de sessão único com BetterWorks, execute os seguintes passos:**

1. No portal do Azure, no **BetterWorks** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_samlbase.png)

3. No **BetterWorks domínio e os URLs** secção, se pretender configurar a aplicação no **IDP iniciada modo**:

    ![Configurar o início de sessão único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://app.betterworks.com/saml2/metadata/`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://app.betterworks.com/saml2/acs/`

4. No **BetterWorks domínio e os URLs** secção, se pretender configurar a aplicação no **SP iniciada modo**, execute os seguintes passos:
    
    ![Configurar o início de sessão único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_url1.png)

    a. Clique em de **Mostrar avançadas definições de URL**.

    b. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://app.betterworks.com`

    > [!NOTE] 
    > Estes não são valores reais. Atualize estes valores com o URL de resposta, o identificador e o início de sessão real no URL. Contacte [BetterWorks suporta equipa](mailto:support@betterworks.com) para obter estes valores.
 
4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_certificate.png)  

5. Aplicação de BetterWorks espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos a partir de "**atributo**" separador da aplicação. A seguinte captura de ecrã mostra um exemplo para este. 

    ![Configurar o início de sessão único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_attribute.png)

6. No **atributos token SAML** caixa de diálogo, para cada linha mostrada na tabela abaixo, execute os seguintes passos:
 
   | Nome do atributo | Valor do atributo |
   | -------------- |  ------------ |
   | saml_token     | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

   a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-betterworks-tutorial/tutorial_officespace_04.png)

    ![Configurar o início de sessão único](./media/active-directory-saas-betterworks-tutorial/tutorial_officespace_05.png)

   b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha. 

   c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
   d. Clique em **OK**.

7. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-betterworks-tutorial/tutorial_general_400.png)

8. Para configurar o início de sessão único em **BetterWorks** lado, terá de enviar o transferido **XML de metadados** para [BetterWorks suporta equipa](mailto:support@betterworks.com).


> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-betterworks-test-user"></a>Criar um utilizador de teste BetterWorks

Nesta secção, vai criar um utilizador chamado Britta Simon BetterWorks. Trabalhar com [BetterWorks suporta equipa](mailto:support@betterworks.com) para adicionar os utilizadores na plataforma BetterWorks.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para BetterWorks.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a BetterWorks, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **BetterWorks**.

    ![Configurar o início de sessão único](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico BetterWorks no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de BetterWorks.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png

