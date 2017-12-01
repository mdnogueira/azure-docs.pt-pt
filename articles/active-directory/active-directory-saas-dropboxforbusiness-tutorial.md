---
title: "Tutorial: Integração do Azure Active Directory com o Dropbox para empresas | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Dropbox para empresas."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: 2a71892296c82e3655a75ca3e863539cc19ea998
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Tutorial: Integração do Azure Active Directory com o Dropbox para empresas

Neste tutorial, irá aprender a integrar o Dropbox para empresas com o Azure Active Directory (Azure AD).

Integrar o Dropbox para empresas com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Dropbox para empresas.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Dropbox para empresas (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Dropbox para empresas, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Dropbox para empresas-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Dropbox para empresas a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Adicionar Dropbox para empresas a partir da Galeria
Para configurar a integração da Dropbox para empresas com o Azure AD, terá de adicionar Dropbox para empresas a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Dropbox para empresas a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Dropbox para empresas**, selecione **Dropbox para empresas** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Dropbox para empresas na lista de resultados](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Dropbox para empresas com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Dropbox para empresas é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Dropbox para empresas tem de ser estabelecida.

No Dropbox para empresas, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Dropbox para empresas, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um Dropbox para o utilizador de teste de negócio](#create-a-dropbox-for-business-test-user)**  - para ter um homólogo de Britta Simon no Dropbox para empresas que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único no seu Dropbox para aplicação de negócio.

**Para configurar o Azure AD-início de sessão único com Dropbox para empresas, execute os seguintes passos:**

1. No portal do Azure, no **Dropbox para empresas** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. No **Dropbox para o domínio de negócio e URLs** secção, execute os seguintes passos:

    ![Dropbox para domínio de negócio e URLs único início de sessão informações](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://www.dropbox.com/sso/<id>`

    b. No **identificador** caixa de texto, escreva um valor:`Dropbox`

    > [!NOTE] 
    > O valor de URL de início de sessão anterior não é o valor real. Atualizar o valor com o real início de sessão no URL, que é explicado mais tarde no tutorial. Contacte [Dropbox para a equipa de suporte de cliente de negócio](https://www.dropbox.com/business/contact) para obter o valor. 
 

4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_400.png)

6. No **Dropbox para configuração de negócio** secção, clique em **configurar Dropbox para empresas** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Dropbox para configuração de negócio](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Para configurar o início de sessão único em **Dropbox para empresas** lado, visite no seu Dropbox para o inquilino de negócio.

    a. Inicie sessão seu Dropbox para o inquilino de negócio. 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769509.png "configurar o início de sessão único")
   
    b. No painel de navegação no lado esquerdo, clique em **consola de administração**. 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769510.png "configurar o início de sessão único")
   
    c. No **consola de administração**, clique em **autenticação** no painel de navegação esquerdo. 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769511.png "configurar o início de sessão único")
   
    d. No **de sessão único-** secção, selecione **ativar o início de sessão único**e, em seguida, clique em **mais** para expandir esta secção.  
   
    ![Configurar o início de sessão único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769512.png "configurar o início de sessão único")
   
    e. Copie o URL junto a **os utilizadores podem iniciar sessão introduzindo o respetivo endereço de e-mail ou podem aceder diretamente à** e cole-o para o **URL de início de sessão** caixa de texto de **Dropbox para o domínio de negócio e URLs** secção no portal do Azure. 
    
    ![Configurar o início de sessão único](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769513.png)
    
8. No **de sessão único-** secção o **autenticação** página, execute os seguintes passos: 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "configurar o início de sessão único")
   
    a. Clique em **necessário**.
   
    b. No o **URL de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.

    c. Clique em **Escolher certificado**e, em seguida, navegue até à sua **ficheiro de certificado codificado Base64**.

    d. Clique em **guardar alterações** para concluir a configuração no seu DropBox para o inquilino de negócio.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Criar um Dropbox para o utilizador de teste de negócio

Nesta secção, é criado um utilizador chamado Britta Simon Dropbox para empresas. Dropbox para empresas suporta o aprovisionamento de just-in-time, que está ativada por predefinição.

Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe no Dropbox para empresas, uma nova é criada quando tentar aceder ao Dropbox para empresas.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [Dropbox para a equipa de suporte de cliente de negócio](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder acesso ao Dropbox para empresas.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon ao Dropbox para empresas, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Dropbox para empresas**.

    ![A Dropbox para ligação da empresa na lista de aplicações](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Ao clicar em Dropbox do mosaico de negócio no painel de acesso, pode ser obtido a página de início de sessão do seu Dropbox para aplicação de negócio.
 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_203.png

