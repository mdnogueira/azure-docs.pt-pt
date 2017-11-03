---
title: "Tutorial: Integração do Azure Active Directory com o reencaminhamento de imagem | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e do reencaminhamento de imagem."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 0bfbbaee7a74df6508584b7c8846fd07c2dc15c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Tutorial: Integração do Azure Active Directory com o reencaminhamento de imagem

Neste tutorial, irá aprender a integrar o reencaminhamento de imagem com o Azure Active Directory (Azure AD).

Integrar o reencaminhamento de imagem com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à imagem de reencaminhamento
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para reencaminhamento de imagem (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com reencaminhamento de imagem, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um reencaminhamento de imagem-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de reencaminhamento de imagem de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-image-relay-from-the-gallery"></a>A adição de reencaminhamento de imagem de galeria
Para configurar a integração do reencaminhamento de imagem com o Azure AD, tem de adicionar imagem de reencaminhamento da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar imagem de reencaminhamento da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **imagem reencaminhamento**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_search.png)

5. No painel de resultados, selecione **imagem reencaminhamento**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o reencaminhamento de imagem com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no reencaminhamento de imagem é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no reencaminhamento de imagem tem de ser estabelecida.

Na imagem de reencaminhamento, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o reencaminhamento de imagem, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de reencaminhamento de imagem](#creating-an-image-relay-test-user)**  - para ter um homólogo de Britta Simon no reencaminhamento de imagem que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de reencaminhamento de imagem.

**Para configurar o Azure AD-início de sessão único com o reencaminhamento de imagem, execute os seguintes passos:**

1. No portal do Azure, no **imagem reencaminhamento** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. No **domínio de reencaminhamento de imagem e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.imagerelay.com/`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de reencaminhamento de imagem](http://support.imagerelay.com/) para obter estes valores. 
 


4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_general_400.png)

6. No **configuração de reencaminhamento de imagem** secção, clique em **configurar o reencaminhamento de imagem** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL do serviço e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. Na outra janela do browser, inicie sessão no site da sua empresa reencaminhamento de imagem como um administrador.

8. Na barra de ferramentas na parte superior, clique em de **utilizadores e permissões** carga de trabalho.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. Clique em **criar nova permissão**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

10. No **início de sessão único em definições** carga de trabalho, selecione o **este grupo pode apenas início de sessão através de início de sessão único** caixa de verificação e, em seguida, clique em **guardar**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. Aceda a **definições da conta**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. Vá para o **início de sessão único em definições** carga de trabalho.
    
     ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

13. No **SAML definições** caixa de diálogo, execute os seguintes passos:
    
    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. No **URL de início de sessão** caixa de texto, cole o valor de **URL Single Sign-On serviço** que copiou do portal do Azure.

    b. No **URL de fim de sessão** caixa de texto, cole o valor de **único URL de serviço Sign-Out** que copiou do portal do Azure.

    c. Como **formato de Id de nome**, selecione **urn: oasis: os nomes: tc: SAML:1.1:nameid-formato: endereço de correio eletrónico**.

    d. Como **opções de enlace de mensagens em fila para pedidos do fornecedor de serviço (imagem reencaminhamento)**, selecione **POST enlace**.

    e. Em **x. 509 certificado**, clique em **atualização certificado**.

    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Abra o certificado transferido no bloco de notas, copie o conteúdo e, em seguida, cole-o a caixa de texto do certificado x. 509.

    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. No **aprovisionamento de utilizadores just** secção, selecione o **ativar o aprovisionamento de utilizadores just**.

    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selecione o grupo de permissão (por exemplo, **SSO básico**) que está autorizado a iniciar sessão apenas através de um início de sessão único.

    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    posso. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-image-relay-test-user"></a>Criar um utilizador de teste de reencaminhamento de imagem

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon na imagem de reencaminhamento.

**Para criar um utilizador chamado Britta Simon na imagem de reencaminhamento, execute os seguintes passos:**

1. Início de sessão no site da sua empresa imagem reencaminhamento como administrador.

2. Aceda a **utilizadores e permissões** e selecione **criar utilizador SSO**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Introduza o **E-Mail**, **nome próprio**, **Apelido**, e **empresa** do utilizador que pretende aprovisionar e selecione o grupo de permissões (para exemplo básico SSO) que é o grupo que pode iniciar sessão apenas através de um início de sessão único.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. Clique em **Criar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à imagem de reencaminhamento.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a imagem de reencaminhamento, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **imagem reencaminhamento**.

    ![Configurar o início de sessão único](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.    

Quando clica no mosaico de reencaminhamento de imagem no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de reencaminhamento de imagem.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png

