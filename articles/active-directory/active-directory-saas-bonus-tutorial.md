---
title: "Tutorial: Integração do Azure Active Directory com Bonusly | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Bonusly."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 29a88b2efdb9f0f33f7933bc654a5a0fdf589c5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integração do Azure Active Directory com Bonusly

Neste tutorial, irá aprender a integrar Bonusly com o Azure Active Directory (Azure AD).

Integrar Bonusly com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Bonusly
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Bonusly (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Bonusly, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Bonusly-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Bonusly a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-bonusly-from-the-gallery"></a>Adicionar Bonusly a partir da Galeria
Para configurar a integração de Bonusly com o Azure AD, terá de adicionar Bonusly a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Bonusly a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Bonusly**, selecione **Bonusly** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Bonusly na lista de resultados](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com Bonusly com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Bonusly é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Bonusly tem de ser estabelecida.

No Bonusly, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Bonusly, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Bonusly](#create-a-bonusly-test-user)**  - para ter um homólogo de Britta Simon Bonusly que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Bonusly.

**Para configurar o Azure AD-início de sessão único com Bonusly, execute os seguintes passos:**

1. No portal do Azure, no **Bonusly** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_samlbase.png)

3. No **domínio Bonusly e URLs** secção, execute os seguintes passos:

    ![Bonusly URLs de domínio e única informações de início de sessão](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_url.png)

    No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte Bonusly](https://Bonusly/contact) para obter o valor.
 
4. No **certificado de assinatura de SAML** secção, copie o **THUMBPRINT** valor do certificado.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-bonus-tutorial/tutorial_general_400.png)

6. No **configuração Bonusly** secção, clique em **configurar Bonusly** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_configure.png) 

7. Numa janela do browser diferente, inicie sessão no seu **Bonusly** inquilino.

8. Na barra de ferramentas na parte superior, clique em **definições**e, em seguida, selecione **integrações e aplicações**.
   
    ![Secção de redes Social bonusly](./media/active-directory-saas-bonus-tutorial/ic773686.png "Bonusly")
9. Em **Single Sign-On**, selecione **SAML**.

10. No **SAML** diálogo página, execute os seguintes passos:
   
    ![Página de caixa de diálogo de Saml bonusly](./media/active-directory-saas-bonus-tutorial/ic773687.png "Bonusly")
   
    a. No **URL de destino do IdP SSO** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.
   
    b. No **IdP emissor** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure. 

    c. No **URL de início de sessão do IdP** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.

    d. Colar o **Thumbprint** valor copiados a partir do portal do Azure para o **impressão digital do certificado** caixa de texto.
   
11. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/active-directory-saas-bonus-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-bonus-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![O botão de adição](./media/active-directory-saas-bonus-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-bonus-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-bonusly-test-user"></a>Criar um utilizador de teste Bonusly

Para permitir que os utilizadores do Azure AD iniciem sessão nos Bonusly, têm de ser aprovisionados para Bonusly. No caso de Bonusly, o aprovisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas do utilizador Bonusly conta criação ou APIs fornecidas pelo Bonusly para aprovisionar contas de utilizador do AAD.
>  

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Numa janela do browser web, inicie sessão no seu inquilino Bonusly.

2. Clique em **definições**.
 
    ![Definições](./media/active-directory-saas-bonus-tutorial/ic781041.png "definições")

3. Clique em de **utilizadores e bonuses** separador.
   
    ![Os utilizadores e bonuses](./media/active-directory-saas-bonus-tutorial/ic781042.png "utilizadores e bonuses")

4. Clique em **gerir utilizadores**.
   
    ![Gerir utilizadores](./media/active-directory-saas-bonus-tutorial/ic781043.png "gerir utilizadores")

5. Clique em **adicionar utilizador**.
   
    ![Adicionar utilizador](./media/active-directory-saas-bonus-tutorial/ic781044.png "adicionar utilizador")

6. No **adicionar utilizador** caixa de diálogo, execute os seguintes passos:
   
    ![Adicionar utilizador](./media/active-directory-saas-bonus-tutorial/ic781045.png "adicionar utilizador")  

    a. No **nome próprio** caixa de texto, introduza o nome de utilizador como **Britta**.

    b. No **Apelido** caixa de texto, introduza o apelido do utilizador, como **Simon**.
 
    c. No **E-Mail** caixa de texto, introduza o e-mail do utilizador, como  **brittasimon@contoso.com** .

    d. Clique em **Guardar**.
   
     >[!NOTE]
     >Titular da conta do Azure AD recebe uma mensagem de e-mail que inclui uma ligação para confirmar a conta para ficar ativa.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Bonusly.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Bonusly, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Bonusly**.

    ![O Bonusly ligação na lista de aplicações](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Bonusly no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação Bonusly.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_203.png

