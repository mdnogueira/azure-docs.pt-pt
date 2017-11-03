---
title: "Tutorial: Integração do Azure Active Directory com Workday | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e do Workday."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 164d5c644f120fa86e2b690649241892764b64b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integração do Azure Active Directory com Workday

Neste tutorial, irá aprender a integração do Workday com o Azure Active Directory (Azure AD).

Integração do Workday com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Workday
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Workday (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Workday, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Workday início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Workday a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-workday-from-the-gallery"></a>Adicionar Workday a partir da Galeria
Para configurar a integração do Workday com o Azure AD, terá de adicionar Workday a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Workday a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Workday**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_search.png)

5. No painel de resultados, selecione **Workday**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Workday com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Workday é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Workday tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no Workday.

Para configurar e testar o Azure AD-início de sessão único com Workday, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Workday](#creating-a-workday-test-user)**  - para ter um homólogo de Britta Simon no Workday que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação do Workday.

**Para configurar o Azure AD-início de sessão único com Workday, execute os seguintes passos:**

1. No portal do Azure, no **Workday** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. No **URLs e de domínio do Workday** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. No **URL de início de sessão** caixa de texto, digite o valor como:`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE] 
    > Estes valores não estiverem a real. Atualize estes valores com o URL de início de sessão real e o URL de resposta. O URL de resposta tem de ter um subdomínio por exemplo: www, wd2, wd3, wd3 impl, wd5, wd5 impl). Utilizar algo semelhante a "*http://www.myworkday.com*" funciona, mas "*http://myworkday.com*" não. Contacte [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter estes valores. 
 

4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)

6. No **Workday configuração** secção, clique em **configurar Workday** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 
<CS>
7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Workday como administrador.

8. Aceda a **Menu \> Workbench**.
   
    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

9. Aceda a **conta administração**.
   
    ![Conta de administração](./media/active-directory-saas-workday-tutorial/IC782924.png "administração da conta")

10. Aceda a **Editar configuração de inquilino – segurança**.
   
    ![Editar segurança inquilino](./media/active-directory-saas-workday-tutorial/IC782925.png "Editar segurança do inquilino")

11. No **os URLs de redirecionamento** secção, execute os seguintes passos:
   
    ![Os URLs de redirecionamento](./media/active-directory-saas-workday-tutorial/IC7829581.png "os URLs de redirecionamento")
   
    a. Clique em **Adicionar linha**.
   
    b. No **URL de redireccionamento de início de sessão** caixa de texto e o **URL de redirecionamento Mobile** caixa de texto, tipo o **URL de início de sessão** que introduziu **URLsededomíniodoWorkday** secção do portal do Azure.
   
    c. No portal do Azure, no **configurar início de sessão** janela, copie o **Sign-Out URL**e, em seguida, cole-o para o **URL de redirecionamento de fim de sessão** caixa de texto.
   
    d.  No **ambiente** caixa de texto, escreva o nome do ambiente.  

    >[!NOTE]
    > O valor do atributo ambiente está associado ao valor do URL de inquilino:  
    >-Se o nome de domínio do URL de inquilino do Workday começa com impl por exemplo: *https://impl.workday.com/\<inquilino\>/login-saml2.htmld*), o **ambiente** atributo tem de ser definida para a implementação.  
    >-Se o nome de domínio é iniciado com outra coisa, terá de contactar [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter a correspondência **ambiente** valor.

12. No **configuração SAML** secção, execute os seguintes passos:
   
    ![A configuração SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "configuração SAML")
   
    a.  Selecione **ativar autenticação SAML**.
   
    b.  Clique em **Adicionar linha**.

13. Na secção de fornecedores de identidade de SAML, execute os seguintes passos:
   
    ![Fornecedores de identidade SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "fornecedores de identidade SAML")
   
    a. Na caixa de texto nome do fornecedor de identidade, escreva um nome de fornecedor (por exemplo: *SPInitiatedSSO*).
   
    b. No portal do Azure, no **configurar início de sessão** janela, copie o **ID de entidade de SAML** valor e, em seguida, cole-o para o **emissor** caixa de texto.
   
    c. Selecione **ativar a fim de sessão iniciada do Workday**.
   
    d. No portal do Azure, no **configurar início de sessão** janela, copie o **Sign-Out URL** valor e, em seguida, cole-o para o **URL de pedido do fim de sessão** caixa de texto.

    e. Clique em **certificado de chave pública fornecedor de identidade**e, em seguida, clique em **criar**. 

    ![Criar](./media/active-directory-saas-workday-tutorial/IC782928.png "criar")

    f. Clique em **criar x509 chave pública**. 

    ![Criar](./media/active-directory-saas-workday-tutorial/IC782929.png "criar")


14. No **chave pública de vista x509** secção, execute os seguintes passos: 
   
    ![Chave pública de vista x509](./media/active-directory-saas-workday-tutorial/IC782930.png "chave pública de vista x509") 
   
    a. No **nome** caixa de texto, escreva um nome para o certificado (por exemplo: *PPE\_SP*).
   
    b. No **válido de** caixa de texto, escreva a partir do valor de atributo do seu certificado válido.
   
    c.  No **válido para** caixa de texto, digite o valor do atributo do seu certificado válido.
   
    > [!NOTE]
    > Pode obter válido de data e válido para a data do certificado transferido, fazendo duplo clique-lo.  As datas estão listadas no **detalhes** separador.
    > 
    >
   
    d.  Abra o certificado codificado base-64 no bloco de notas e, em seguida, copie o conteúdo do mesmo.
   
    e.  No **certificado** caixa de texto, cole o conteúdo da sua área de transferência.
   
    f.  Clique em **OK**.

15. Execute os seguintes passos: 
   
    ![Configuração de SSO](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "configuração SSO")
   
    a.  Ativar o **x509 par de chaves privadas**.
   
    b.  No **ID do fornecedor de serviço** caixa de texto, tipo **http://www.workday.com**.
   
    c.  Selecione **ativar SP iniciada autenticação SAML**.
   
    d.  No portal do Azure, no **configurar início de sessão** janela, copie o **único início de sessão no URL do serviço SAML** valor e, em seguida, cole-o para o **URL do serviço do IdP SSO** caixa de texto.
   
    e. Selecione **não Deflate o pedido de autenticação iniciado por SP**.
   
    f. Como **pedido de assinatura de método de autenticação de**, selecione **SHA256**. 
   
    ![Método de assinatura do pedido de autenticação](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "método de assinatura do pedido de autenticação") 
   
    g. Clique em **OK**. 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")
<CE>

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
>

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-workday-test-user"></a>Criar um utilizador de teste do Workday

Para obter um utilizador de teste aprovisionado no Workday, terá de contactar o [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html).
O [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) cria o utilizador para si.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder acesso ao Workday.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon ao Workday, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Workday**.

    ![Configurar o início de sessão único](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Se pretender testar as definições de início de sessão único, abra o painel de acesso. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o aprovisionamento de utilizadores](active-directory-saas-workday-inbound-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png

