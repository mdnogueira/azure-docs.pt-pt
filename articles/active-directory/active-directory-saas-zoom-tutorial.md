---
title: "Tutorial: Integração do Azure Active Directory com Zoom | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Zoom."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: b8d902b9170fee47afb038df739bbca6d2d025e0
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Integração do Azure Active Directory com o Zoom

Neste tutorial, irá aprender a integrar o Zoom no Azure Active Directory (Azure AD).

Integrar o Zoom com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Zoom.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para aplicar Zoom (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Zoom, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Zoom-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de Zoom da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-zoom-from-the-gallery"></a>A adição de Zoom da galeria do
Para configurar a integração de Zoom com o Azure AD, tem de adicionar Zoom da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Zoom da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Zoom**, selecione **Zoom** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Ampliar a lista de resultados](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Zoom com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Zoom é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zoom tem de ser estabelecida.

Zoom, o valor de atribuir o **nome de utilizador** no Azure AD como o valor do **nome de utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Zoom, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de Zoom](#create-a-zoom-test-user)**  - para ter um homólogo de Britta Simon Zoom que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de Zoom.

**Para configurar o Azure AD-início de sessão único com Zoom, execute os seguintes passos:**

1. No portal do Azure, no **Zoom** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_samlbase.png)

3. No **domínio Zoom e URLs** secção, execute os seguintes passos:

    ![Domínio de zoom e os URLs únicos de informações de início de sessão](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.zoom.us`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`<companyname>.zoom.us`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de Zoom](https://support.zoom.us/hc) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-zoom-tutorial/tutorial_general_400.png)

6. No **Zoom configuração** secção, clique em **configurar Zoom** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de zoom](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Zoom como administrador.

8. Clique em de **Single Sign-On** separador.
   
    ![Separador de início de sessão único](./media/active-directory-saas-zoom-tutorial/IC784700.png "de sessão único-")

9. Clique em de **controlo de segurança** separador e, em seguida, avance para o **Single Sign-On** definições.

10. Na secção de Single Sign-On, execute os seguintes passos:
   
    ![Único início de sessão na secção](./media/active-directory-saas-zoom-tutorial/IC784701.png "de sessão único-")
   
    a. No **URL de página de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
   
    b. No **URL da página de início de sessão** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure.
     
    c. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado do fornecedor de identidade** caixa de texto.

    d. No **emissor** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure. 

    e. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-zoom-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-zoom-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-zoom-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-zoom-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-zoom-test-user"></a>Criar um utilizador de teste de Zoom

Para permitir que os utilizadores do Azure AD iniciem sessão nos Zoom, têm de ser aprovisionados para Zoom. No caso do Zoom, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

1. Inicie sessão no seu **Zoom** site da empresa como administrador.
 
2. Clique em de **gestão de contas** separador e, em seguida, clique em **gestão de utilizadores**.

3. Na secção de gestão de utilizadores, clique em **adicionar utilizadores**.
   
    ![Gestão de utilizadores](./media/active-directory-saas-zoom-tutorial/IC784703.png "gestão de utilizadores")

4. No **adicionar utilizadores** página, execute os seguintes passos:
   
    ![Adicionar utilizadores](./media/active-directory-saas-zoom-tutorial/IC784704.png "adicionar utilizadores")
   
    a. Como **tipo de utilizador**, selecione **básico**.

    b. No **E-mails** caixa de texto, escreva o endereço de e-mail de um válido do Azure AD conta pretende aprovisionar.

    c. Clique em **Adicionar**.

> [!NOTE]
> Pode utilizar quaisquer outras Zoom utilizador conta criação ferramentas ou APIs fornecidas pelo Zoom para aprovisionar o Azure Active Directory, contas de utilizador.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder acesso ao Zoom.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon ao Zoom, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Zoom**.

    ![A ligação de Zoom na lista de aplicações](./media/active-directory-saas-zoom-tutorial/tutorial_zoom_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de Zoom no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Zoom.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zoom-tutorial/tutorial_general_203.png

