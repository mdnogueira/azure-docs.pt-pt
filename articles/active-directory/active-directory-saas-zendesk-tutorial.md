---
title: "Tutorial: Integração do Azure Active Directory com Zendesk | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e do Zendesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 51c06d838c5ed6286dfb99ea25faaaf33bad5f3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração do Azure Active Directory com Zendesk

Neste tutorial, irá aprender a integração do Zendesk no Azure Active Directory (Azure AD).

Integração do Zendesk com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Zendesk
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Zendesk (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Zendesk, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Zendesk-início de sessão único ativada subscrição


> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Zendesk a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-


## <a name="adding-zendesk-from-the-gallery"></a>Adicionar Zendesk a partir da Galeria
Para configurar a integração do Zendesk com o Azure AD, terá de adicionar Zendesk a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Zendesk a partir da galeria, execute os seguintes passos:**

1. No  **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Zendesk**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_search.png)

5. No painel de resultados, selecione **Zendesk**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Zendesk com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Zendesk é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zendesk tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Zendesk.

Para configurar e testar o Azure AD-início de sessão único com Zendesk, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Zendesk](#creating-a-zendesk-test-user)**  - para ter um homólogo de Britta Simon Zendesk que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação do Zendesk.

**Para configurar o Azure AD-início de sessão único com Zendesk, execute os seguintes passos:**

1. No portal do Azure, no **Zendesk** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. No **URLs de domínio do Zendesk e** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_url.png)

    a. No **URL de início de sessão** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://<subdomain>.zendesk.com`

    b. No **identificador** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://<subdomain>.zendesk.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão real e o URL de identificador. Contacte [equipa de suporte do Zendesk](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter estes valores. 

4. Zendesk espera as asserções de SAML num formato específico. Existem não existem atributos SAML obrigatórios, mas, opcionalmente, pode adicionar um atributo de **atributos de utilizador** secção seguindo os passos abaixo: 

     ![Configurar o início de sessão único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Clique em de **ver e editar todos os outros atributos** caixa de verificação.
     
    ![Configurar o início de sessão único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes2.png)
   
    b. Clique em de **adicionar atributo** para abrir **adicionar atributo** caixa de diálogo.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_05.png)

    c. No **nome** caixa de texto, escreva o nome de atributo (por exemplo **emailaddress**).
    
    d. Do **valor** lista, selecione o valor do atributo (como **user.mail**).
    
    e. Clique em **Ok**
 
    > [!NOTE] 
    > Utilize os atributos de extensão para adicionar atributos que não estão no Azure AD por predefinição. Clique em [atributos de utilizador que podem ser definidos no SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de SAML atributos que **Zendesk** aceita. 

5. No **certificado de assinatura de SAML** secção, copie o **THUMBPRINT** valor do certificado.

    ![Configurar o início de sessão único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_certificate.png) 

6. No **Zendesk configuração** secção, clique em **configurar Zendesk** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Zendesk como administrador.

8. Clique em **Admin**.

9. No painel de navegação esquerdo, clique em **definições**e, em seguida, clique em **segurança**.

10. No **segurança** página, execute os seguintes passos: 
   
     ![Segurança](./media/active-directory-saas-zendesk-tutorial/ic773089.png "segurança")

    ![De sessão único-](./media/active-directory-saas-zendesk-tutorial/ic773090.png "de sessão único-")

     a. Clique em de **Admin & agentes** separador.

     b. Selecione **único início de sessão (SSO) e SAML**e, em seguida, selecione **SAML**.

     c. No **SAML SSO URL** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure. 

     d. No **URL de fim de sessão remoto** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure.
        
     e. No **impressão digital do certificado** caixa de texto, cole o **Thumbprint** valor do certificado que copiou do portal do Azure.
     
     f. Clique em **Guardar**.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zendesk-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zendesk-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zendesk-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zendesk-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**. 

### <a name="creating-a-zendesk-test-user"></a>Criar um utilizador de teste do Zendesk

Para permitir que os utilizadores do Azure AD sessão **Zendesk**, têm de ser aprovisionados para **Zendesk**.  
Consoante a função atribuída a aplicações, é o comportamento esperado:

 1. **Utilizador final** contas são aprovisionadas automaticamente quando iniciar sessão.
 2. **Agente** e **Admin** as contas têm de ser aprovisionada manualmente no **Zendesk** antes de iniciar sessão.
 
**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Zendesk** inquilino.

2. Selecione o **cliente lista** separador.

3. Selecione o **utilizador** separador e clique em **adicionar**.
   
    ![Adicionar utilizador](./media/active-directory-saas-zendesk-tutorial/ic773632.png "adicionar utilizador")
4. Escreva o endereço de correio eletrónico da conta do Azure AD existente que pretende aprovisionar e, em seguida, clique em **guardar**.
   
    ![Novo utilizador](./media/active-directory-saas-zendesk-tutorial/ic773633.png "novo utilizador")

> [!NOTE]
> Pode utilizar quaisquer outras Zendesk utilizador conta criação ferramentas ou APIs fornecidas pelo Zendesk para aprovisionar contas de utilizador do AAD.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Zendesk.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Zendesk, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Zendesk**.

    ![Configurar o início de sessão único](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do Zendesk no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação do Zendesk.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_203.png
