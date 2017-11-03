---
title: "Tutorial: Integração do Azure Active Directory com SD elementos | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e os elementos de SD."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 624eff0a0da8f548877e4a4346b21df89cd37b67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Tutorial: Integração do Azure Active Directory com SD elementos

Neste tutorial, irá aprender a integrar SD elementos com o Azure Active Directory (Azure AD).

Integrar SD elementos com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso aos elementos de SD
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada em elementos de SD (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com elementos SD, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um SD elementos-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de elementos de SD na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-sd-elements-from-the-gallery"></a>A adição de elementos de SD na galeria do
Para configurar a integração de SD elementos com o Azure AD, tem de adicionar elementos SD na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar SD elementos a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **SD elementos**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_search.png)

5. No painel de resultados, selecione **SD elementos**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com elementos SD com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo em elementos de SD é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em elementos de SD tem de ser estabelecida.

Em elementos de SD, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com elementos SD, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de elementos de SD](#creating-a-sd-elements-test-user)**  - ter um homólogo de Britta Simon em elementos de SD que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de elementos de SD.

**Para configurar o Azure AD-início de sessão único com elementos SD, execute os seguintes passos:**

1. No portal do Azure, no **SD elementos** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_samlbase.png)

3. No **SD elementos domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador e o URL de resposta real. Contacte [equipa de suporte de elementos de SD](mailto:support@sdelements.com) para obter estes valores.

4. Aplicação de elementos de SD espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos do **"Atributo de utilizador"** separador da aplicação. A seguinte captura de ecrã mostra um exemplo para este.

    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_attribute.png)

5. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributo token SAML, conforme mostrado na imagem e efetuar os seguintes passos: 

    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | Correio eletrónico |User.Mail |
    | nome próprio |User.givenName |
    | Apelido |User.Surname |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_officespace_04.png)

    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_officespace_05.png)

    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.

    d. Clique em **OK**.
 
6. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_certificate.png) 

7. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_general_400.png)

8. No **SD elementos configuração** secção, clique em **Configurar elementos de SD** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_configure.png)

9. Para obter início de sessão único ativada, contacte o seu [equipa de suporte de elementos de SD](mailto:support@sdelements.com) e forneça-los com o ficheiro de certificado transferido. 

10. Numa janela do browser diferente, início de sessão no seu inquilino de elementos de SD como administrador.

11. No menu na parte superior, clique em **sistema**e, em seguida, **Single Sign-on**. 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) 

12. No **as definições de início de sessão único** caixa de diálogo, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. Como **SSO tipo**, selecione **SAML**.
   
    b. No **ID de entidade do fornecedor de identidade** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure. 
   
    c. No **fornecedor único início de sessão no serviço de identidade** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure. 
   
    d. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sd-elements-test-user"></a>Criar um utilizador de teste de SD elementos

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon em elementos de SD. No caso de elementos de SD, criar utilizadores de elementos de SD é uma tarefa manual.

**Para criar Britta Simon em elementos de SD, execute os seguintes passos:**

1. Numa janela do browser web, início de sessão no site da sua empresa elementos SD como administrador.

2. No menu na parte superior, clique em **gestão de utilizadores**e, em seguida, **utilizadores**.
   
    ![Criar um utilizador de teste de SD elementos](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) 

3. Clique em **adicionar novo utilizador**.
   
    ![Criar um utilizador de teste de SD elementos](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png)
 
4. No **adicionar novo utilizador** caixa de diálogo, execute os seguintes passos:
   
    ![Criar um utilizador de teste de SD elementos](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
    a. No **correio electrónico** caixa de texto, introduza o e-mail do utilizador, como  **brittasimon@contoso.com** .
   
    b. No **nome próprio** caixa de texto, introduza o nome de utilizador como **Britta**.
   
    c. No **Apelido** caixa de texto, introduza o apelido do utilizador, como **Simon**.
   
    d. Como **função**, selecione **utilizador**. 
   
    e. Clique em **criar utilizador**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso aos elementos de SD.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon em elementos de SD, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **SD elementos**.

    ![Configurar o início de sessão único](./media/active-directory-saas-sd-elements-tutorial/tutorial_sdelements_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.
  
Quando clica no mosaico de elementos de SD no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de elementos de SD.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png

