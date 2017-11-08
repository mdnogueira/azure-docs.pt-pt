---
title: "Tutorial: Integração do Azure Active Directory Atlassian nuvem | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Atlassian nuvem."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jeedes
ms.openlocfilehash: 25a7694cefde344b34d3a46535bf09209b306593
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integração do Azure Active Directory Atlassian nuvem

Neste tutorial, irá aprender a integrar Atlassian Cloud com o Azure Active Directory (Azure AD).

Integrar Atlassian Cloud com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à nuvem Atlassian.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para nuvem Atlassian (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Atlassian nuvem, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Para ativar SAML-início de sessão único para produtos da nuvem Atlassian terá de definir a segurança do Identity Manager. Saiba mais sobre [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de nuvem Atlassian de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-atlassian-cloud-from-the-gallery"></a>A adição de nuvem Atlassian de galeria
Para configurar a integração da nuvem Atlassian com o Azure AD, tem de adicionar Atlassian Cloud na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Atlassian Cloud a partir da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Atlassian nuvem**, selecione **Atlassian nuvem** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Nuvem Atlassian na lista de resultados](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configura e teste do Azure AD-início de sessão único com Atlassian nuvem com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na nuvem Atlassian é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na nuvem Atlassian tem de ser estabelecida.

Na nuvem Atlassian, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Atlassian nuvem, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de nuvem Atlassian](#create-an-atlassian-cloud-test-user)**  - para ter um homólogo de Britta Simon na nuvem Atlassian que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de nuvem Atlassian.

**Para configurar o Azure AD-início de sessão único com Atlassian nuvem, execute os seguintes passos:**

1. No portal do Azure, no **Atlassian nuvem** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. No **Atlassian nuvem domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Domínio de nuvem Atlassian e URLs único informações de início de sessão](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url_new.png)
    
    a. No **identificador** caixa de texto, escreva o URL:`https://id.atlassian.com/login`
    
    b. No **URL de resposta** caixa de texto, escreva o URL:`https://id.atlassian.com/login/saml/acs`

    c. No **reencaminhamento estado** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<instancename>.atlassian.net`

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Domínio de nuvem Atlassian e URLs único informações de início de sessão](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    No **caixa de texto do URL de início de sessão**, escreva um URL a utilizar o padrão do seguinte:`https://<instancename>.atlassian.net`

    > [!NOTE] 
    > Estes valores não estiverem reais. Pode obter estes valores a partir do ecrã de configuração de SAML do Atlassian na nuvem, o que é explicada mais tarde no tutorial.

5. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

7. No **configuração da nuvem Atlassian** secção, clique em **configurar a nuvem de Atlassian** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração da nuvem Atlassian](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

8. Para obter SSO configurado para a sua aplicação, o início de sessão no portal de Atlassian utilizando os direitos de administrador.

9. Navegue para **administração de sites Atlassian** > **organizações e segurança**. Se ainda não o fez, crie e atribua o nome da organização. Em seguida, no painel de navegação esquerdo, clique em **domínios**.

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

10. Selecione a forma como os que pretende verificar o seu domínio - **DNS** ou **HTTPS**.

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

11. Verificação de DNS, selecione o **DNS** separador no **domínios** e execute os passos de follwing da página:

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Clique em **cópia** para copiar o valor para o registo TXT.

    b. Do seu DNS, localize a página de definições para adicionar um novo registo

    c. Selecione a opção para adicionar um novo registo e cole o valor que copiou do **domínios** página para o **valor** campo. O DNS também se refiram ao mesmo como **resposta** ou **Descrição**.

    d. O registo de DNS também pode incluir os seguintes campos:
    
    * **Tipo de registo**: introduza **TXT**
    * **Nome/anfitrião/Alias**: deixe a predefinição (@ ou em branco)
    * **Tempo até em direto (TTL)**: introduza **86400**
    
    e.  Guarde o registo.

12. Volte à **página domínios** na administração da organização e clique em de **verificar domínio** botão. Introduza o nome de domínio no pop-up e clique o **verificar domínio** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)
    
    > [!NOTE]
    > Pode demorar até 72 horas para as alterações de registo TXT entrem em vigor, não sabe imediato se a verificação de domínio foi concluída com êxito. Verifique o **domínios** página logo depois de executar estes passos para o estado de verificação. Consulte os seguintes ecrã com o estado atualizado como **VERIFIED**.

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

13. Verificação de HTTPS, selecione o **HTTPS** separador no **domínios** página e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a.  Clique em **transferir ficheiro** para transferir o ficheiro HTML.

    b.  Carregue o ficheiro HTML para o diretório de raiz do seu domínio.

14. Volte à **domínios** página na administração da organização e clique em de **verificar domínio** botão. Introduza o **nome de domínio** no pop-up e clique o **verificar domínio** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

15. Se o processo de verificação pode localizar o ficheiro que carregou no diretório de raiz, o estado do domínio atualizações **Verified**.

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Para obter mais informações, consulte [documentação de verificação de domínio do Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)

16. Na barra de navegação esquerdo, clique em **SAML-início de sessão único**. Se ainda não o fez, subscreve do Atlassian Identity Manager.

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

17. No **configuração SAML do adicionar** caixa de diálogo Adicionar as definições do fornecedor de identidade, da seguinte forma:

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. No **fornecedor de identidade ID de entidade** texto caixa, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.

    b. No **fornecedor de identidade SSO URL** texto caixa, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.

    c. Abra o certificado transferido num bloco de notas e copie os valores sem o Begin e linhas de fim e cole-a no **X509 pública certificado** caixa.
    
    d. Clique em **Guardar configuração** para guardar as definições.
     
18. Atualize as definições do Azure AD para se certificar de que configurou os URLs corretos.
  
    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Copiar o **SP identidade ID** do SAML ecrã e cole o valor no **identificador** caixa no portal do Azure, na nuvem Atlassian **URLs de domínio e** secção.
    
    b. Cópia o **URL do serviço de consumidor do SP asserção** do SAML ecrã e cole o valor no **URL de resposta** caixa no portal do Azure, na nuvem Atlassian **domínios e URLs** secção.
    
    c. URL de início de sessão é o URL de inquilino da sua nuvem Atlassian. 
    
19. No portal do Azure, clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Criar um utilizador de teste Atlassian nuvem

Para permitir que os utilizadores do Azure AD iniciem sessão nos Atlassian Cloud, têm de ser aprovisionados para Atlassian nuvem. Em caso de Atlassian nuvem, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Na secção de administração do Site do Portal de Atlassian, clique o **utilizadores** botão

    ![Criar utilizador de nuvem Atlassian](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Clique em de **convite utilizador** botão para criar um utilizador na nuvem Atlassian.

    ![Criar utilizador de nuvem Atlassian](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Introduza o utilizador **endereço de correio eletrónico** e atribua o acesso de aplicação. 

    ![Criar utilizador de nuvem Atlassian](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Clique em **convidar utilizadores** botão, irá enviar o convite de correio eletrónico ao utilizador e após aceitar o convite ao utilizador será ativo no sistema. 

>[!NOTE] 
>Também pode criar os utilizadores em massa clicando a **em massa criar** botão na seção de utilizadores.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à nuvem Atlassian.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon Atlassian nuvem, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Atlassian nuvem**.

    ![A ligação de nuvem Atlassian na lista de aplicações](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de nuvem Atlassian no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de nuvem Atlassian.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

