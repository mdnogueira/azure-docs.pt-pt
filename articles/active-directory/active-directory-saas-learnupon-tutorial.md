---
title: "Tutorial: Integração do Azure Active Directory com LearnUpon | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e LearnUpon."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: b6ac8acc244e9029be01ede5e0865c280171217d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Tutorial: Integração do Azure Active Directory com LearnUpon

Neste tutorial, irá aprender a integrar LearnUpon com o Azure Active Directory (Azure AD).

Integrar LearnUpon com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao LearnUpon
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para LearnUpon (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LearnUpon, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um LearnUpon-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar LearnUpon a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-learnupon-from-the-gallery"></a>Adicionar LearnUpon a partir da Galeria
Para configurar a integração de LearnUpon com o Azure AD, terá de adicionar LearnUpon a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar LearnUpon a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **LearnUpon**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_search.png)

5. No painel de resultados, selecione **LearnUpon**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com LearnUpon com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no LearnUpon é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LearnUpon tem de ser estabelecida.

No LearnUpon, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com LearnUpon, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste LearnUpon](#creating-a-learnupon-test-user)**  - para ter um homólogo de Britta Simon LearnUpon que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação LearnUpon.

**Para configurar o Azure AD-início de sessão único com LearnUpon, execute os seguintes passos:**

1. No portal do Azure, no **LearnUpon** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. No **LearnUpon domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_url.png)

    No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Tenha em atenção que isto não é o valor real. tem de atualizar este valor com o URL de resposta real. Para obter este valor contacte [equipa de suporte de LearnUpon](https://www.learnupon.com/features/support/).



4. No **certificado de assinatura de SAML** secção, clique em **certificado (bruto)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_general_400.png)

6. No **LearnUpon configuração** secção, clique em **configurar LearnUpon** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_configure.png) 

7. Abra outra instância do browser e inicie sessão em LearnUpon com uma conta de administrador. 

8. Clique em de **definições** separador.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png)

9. Clique em **início de sessão único - SAML**e, em seguida, clique em **definições gerais** para configurar as definições de SAML.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 

10. No **definições gerais** secção, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selecione **ativada**.

    b. Selecione **versão** como **2.0**.

    c. Selecione **ignorar condições** como **não**.

    d. No **SAML Token Post nome de param** caixa de texto, tipo indicado o nome do parâmetro de mensagem de pedido para o URL de consumidor SAML acima, que contém a asserção SAML para ser verificado e autenticados – por exemplo **SAMLResponse** .

    e. No **formato de nome de identificador** caixa de texto, tipo de valor que indica onde no seu asserção SAML o identificador de utilizadores (endereço de correio eletrónico) reside - por exemplo **urn: oasis: os nomes: tc: SAML:1.1:nameid-formato: endereço de correio eletrónico**.
  
    f. No **identificar a localização do fornecedor** caixa de texto, digite o valor que indica onde os utilizadores são enviados para se clicar no seu ícone carregado a partir do seu ecrã de início de sessão portal do Azure.
  
    g. No **termine URL** caixa de texto, cole o **Sign-Out URL** que copiou do portal do Azure.
    
    h. Clique em **gerir as impressões mas**e, em seguida, carregue o imprimir mas do seu certificado transferido.

11. Clique em **as definições de utilizador**e, em seguida, execute os seguintes passos:
   
     ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. No **formato do nome próprio identificador** caixa de texto, tipo de valor que indica-nos onde no seu firstname a utilizadores de asserção SAML reside - por exemplo: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. No **último formato de nome de identificador** caixa de texto, tipo de valor que indica-nos onde no seu lastname a utilizadores de asserção SAML reside - por exemplo: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** .

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-learnupon-test-user"></a>Criar um utilizador de teste LearnUpon

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon LearnUpon. LearnUpon suporta o aprovisionamento de just-in-time, que está por predefinição, ativada.

Não há nenhum item de ação para si nesta secção. Durante uma tentativa de aceder LearnUpon se não existir ainda será criado um novo utilizador. [Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar [equipa de suporte de LearnUpon](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para LearnUpon.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a LearnUpon, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **LearnUpon**.

    ![Configurar o início de sessão único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico LearnUpon no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de LearnUpon.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png

