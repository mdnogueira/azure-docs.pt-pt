---
title: "Tutorial: Integração do Azure Active Directory com Jobscience | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Jobscience."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 66bec35a8f17482433dbf02827b90620d1cff378
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Integração do Azure Active Directory com Jobscience

Neste tutorial, irá aprender a integrar Jobscience com o Azure Active Directory (Azure AD).

Integrar Jobscience com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Jobscience
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Jobscience (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Jobscience, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Jobscience-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Jobscience a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-jobscience-from-the-gallery"></a>Adicionar Jobscience a partir da Galeria
Para configurar a integração de Jobscience com o Azure AD, terá de adicionar Jobscience a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Jobscience a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Jobscience**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_search.png)

5. No painel de resultados, selecione **Jobscience**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Jobscience com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Jobscience é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Jobscience tem de ser estabelecida.

No Jobscience, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Jobscience, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Jobscience](#creating-a-jobscience-test-user)**  - para ter um homólogo de Britta Simon Jobscience que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Jobscience.

**Para configurar o Azure AD-início de sessão único com Jobscience, execute os seguintes passos:**

1. No portal do Azure, no **Jobscience** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_samlbase.png)

3. No **Jobscience domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Para obter este valor [equipa de suporte de cliente Jobscience](https://www.jobscience.com/support) ou do perfil de SSO, irá criar que é explicada mais tarde no tutorial. 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-jobscience-tutorial/tutorial_general_400.png)

6. No **Jobscience configuração** secção, clique em **configurar Jobscience** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_configure.png) 

7. Inicie sessão no site da sua empresa Jobscience como administrador.

8. Aceda a **configuração**.
   
   ![A configuração](./media/active-directory-saas-jobscience-tutorial/IC784358.png "programa de configuração")

9. No painel de navegação esquerdo, no **administrar** secção, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **Meu domínio** página. 
   
   ![O meu domínio](./media/active-directory-saas-jobscience-tutorial/ic767825.png "meu domínio")

10. Para verificar que o domínio tiver sido configurado corretamente, certifique-se de que está a ser "**passo 4 implementada para os utilizadores**" e rever o "**My definições de domínio**".

    ![Domínio implementado no utilizador](./media/active-directory-saas-jobscience-tutorial/ic784377.png "implementado no utilizador de domínio")

11. No site de empresa Jobscience, clique em **controlos de segurança**e, em seguida, clique em **as definições de início de sessão único**.
    
    ![Controlos de segurança](./media/active-directory-saas-jobscience-tutorial/ic784364.png "controlos de segurança")

12. No **as definições de início de sessão único** secção, execute os seguintes passos:
    
    ![Single Sign-On definições](./media/active-directory-saas-jobscience-tutorial/ic781026.png "único as definições de início de sessão")
    
    a. Selecione **SAML ativada**.

    b. Clique em **Novo**.

13. No **SAML único início de sessão no definição editar** caixa de diálogo, execute os seguintes passos:
    
    ![SAML único início de sessão definição](./media/active-directory-saas-jobscience-tutorial/ic784365.png "SAML único início de sessão na definição")
    
    a. No **nome** caixa de texto, escreva um nome para a sua configuração.

    b. No **emissor** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.

    c. No **Id de entidade** caixa de texto, tipo`https://salesforce-jobscience.com`

    d. Clique em **procurar** para carregar o certificado do Azure AD.

    e. Como **tipo de identidade de SAML**, selecione **asserção contém o ID de Federação do objeto User**.

    f. Como **SAML identidade localização**, selecione **é de identidade no elemento NameIdentfier da declaração de assunto**.

    g. No **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure.

    h. No **URL de fim de sessão do fornecedor de identidade** caixa de texto, cole o valor de **Sign-Out URL**, que copiou do portal do Azure.

    posso. Clique em **Guardar**.

14. No painel de navegação esquerdo, no **administrar** secção, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **Meu domínio** página. 
    
    ![O meu domínio](./media/active-directory-saas-jobscience-tutorial/ic767825.png "meu domínio")

15. No **meu domínio** na página de **imagem corporativa página de início de sessão** secção, clique em **editar**.
    
    ![Página de início de sessão de imagem corporativa](./media/active-directory-saas-jobscience-tutorial/ic767826.png "página de início de sessão de imagem corporativa")

16. No **imagem corporativa página de início de sessão** na página a **serviço de autenticação** secção, o nome do seu **SAML SSO definições** é apresentado. Selecionar e, em seguida, clique em **guardar**.
    
    ![Página de início de sessão de imagem corporativa](./media/active-directory-saas-jobscience-tutorial/ic784366.png "página de início de sessão de imagem corporativa")

17. Para obter o SP iniciada pelo início de sessão único no clique do URL de início de sessão no **as definições de início de sessão único** no **controlos de segurança** secção de menu.

    ![Controlos de segurança](./media/active-directory-saas-jobscience-tutorial/ic784368.png "controlos de segurança")
    
    Clique no perfil SSO que criou no passo acima. Esta página mostra o início de sessão único num URL para a sua empresa (por exemplo, [https://companyname.my.salesforce.com?so=companyid](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jobscience-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jobscience-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jobscience-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-jobscience-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-jobscience-test-user"></a>Criar um utilizador de teste Jobscience

Para permitir que os utilizadores do Azure AD iniciem sessão nos Jobscience, têm de ser aprovisionados para Jobscience. No caso de Jobscience, o aprovisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras Jobscience utilizador conta criação ferramentas ou APIs fornecidas pelo Jobscience para aprovisionar o Azure Active Directory contas de utilizador.
>  
        
**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Jobscience** site da empresa como administrador.

2. Vá para a configuração.
   
   ![A configuração](./media/active-directory-saas-jobscience-tutorial/ic784358.png "programa de configuração")
3. Aceda a **gerir utilizadores \> utilizadores**.
   
   ![Os utilizadores](./media/active-directory-saas-jobscience-tutorial/ic784369.png "utilizadores")
4. Clique em **novo utilizador**.
   
   ![Todos os utilizadores](./media/active-directory-saas-jobscience-tutorial/ic784370.png "todos os utilizadores")
5. No **Editar utilizador** caixa de diálogo, execute os seguintes passos:
   
   ![Edição do utilizador](./media/active-directory-saas-jobscience-tutorial/ic784371.png "edição do utilizador")
   
   a. No **nome próprio** caixa de texto, escreva um nome próprio do utilizador como Britta.
   
   b. No **Apelido** caixa de texto, digite o apelido do utilizador como Simon.
   
   c. No **Alias** caixa de texto, escreva um nome de alias do utilizador como brittas.

   d. No **E-Mail** caixa de texto, como o tipo de endereço de correio eletrónico do utilizador Brittasimon@contoso.com.

   e. No **nome de utilizador** caixa de texto, escreva um nome de utilizador utilizador gostar Brittasimon@contoso.com.

   f. No **Nick nome** caixa de texto, escreva um nome de nick do utilizador como Simon.

   g. Clique em **Guardar**.

    
> [!NOTE]
> Titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue-se de uma ligação para confirmar a respetiva conta para ficar ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Jobscience.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Jobscience, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Jobscience**.

    ![Configurar o início de sessão único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Jobscience no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Jobscience.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_203.png

