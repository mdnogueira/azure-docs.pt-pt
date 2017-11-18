---
title: "Tutorial: Integração do Azure Active Directory com o ServiceNow | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e ServiceNow."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 7b25e2184296182687d49b798e5e5a9a2d623c6e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Tutorial: Integração do Azure Active Directory com o ServiceNow

Neste tutorial, irá aprender a integrar o ServiceNow ao Azure Active Directory (Azure AD).

Integrar o ServiceNow com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ServiceNow.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para ServiceNow (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ServiceNow, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Para o ServiceNow, uma instância ou o inquilino do ServiceNow, versão Calgary ou superior
- Para expressas ServiceNow, uma instância do ServiceNow rápida, versão Helsinki ou superior
- O inquilino do ServiceNow tem de ter o [vários fornecedor único início de sessão no plug-in](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) ativada. Isto pode ser feito [submeter um pedido de serviço](https://hi.service-now.com).

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar ServiceNow a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-servicenow-from-the-gallery"></a>Adicionar ServiceNow a partir da Galeria
Para configurar a integração do ServiceNow com o Azure AD, terá de adicionar ServiceNow a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar ServiceNow a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **ServiceNow**, selecione **ServiceNow** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![ServiceNow na lista de resultados](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com ServiceNow com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no ServiceNow é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ServiceNow tem de ser estabelecida.

No ServiceNow, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com ServiceNow, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On para ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o Azure AD Single Sign-On para ServiceNow rápida](#configure-azure-ad-single-sign-on-for-servicenow-express)**  - para permitir aos utilizadores utilizar esta funcionalidade.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
4. **[Criar um utilizador de teste do ServiceNow](#create-a-servicenow-test-user)**  - para ter um homólogo de Britta Simon ServiceNow que está ligada a representação do Azure AD do utilizador.
5. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
6. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Configurar o Azure AD início de sessão único para o ServiceNow

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação do ServiceNow.

**Para configurar o Azure AD-início de sessão único com ServiceNow, execute os seguintes passos:**

1. No portal do Azure, no **ServiceNow** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. No **ServiceNow domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio de ServiceNow e URLs único informações de início de sessão](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<instance-name>.service-now.com/navpage.do`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<instance-name>.service-now.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Terá de atualizar estes valores do URL de início de sessão real e o identificador que é explicada mais tarde no tutorial.

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Para gerar o **metadados** url, execute os seguintes passos:

    a. Clique em **registos de aplicação**.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/appregistrations.png)

    b. Clique em **pontos finais** para abrir **pontos finais** caixa de diálogo.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/endpointicon.png)
    
    c. Clique no botão Copiar para copiar **documento de METADADOS de Federação** url e cole-o bloco de notas.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/endpoint.png)

    d. Agora, aceda a **ServiceNow** propriedades e copie o **ID da aplicação** utilizando **cópia** botão e cole-o bloco de notas.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/appid.png)

    e. Gerar o **URL de metadados** através do padrão de seguinte: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.  Copie o valor gerado no bloco de notas como estes metadados que URL será utilizado mais tarde no tutorial.

7. Um clique configurar o serviço é fornecida para ServiceNow ou seja, para que o Azure AD configurar automaticamente ServiceNow para autenticação baseada em SAML. Para ativar este serviço, aceda ao **ServiceNow configuração** secção, clique em **configurar ServiceNow** para abrir a janela de início de sessão de configurar.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

8. Introduza o seu nome de instância do ServiceNow, o nome de utilizador de administrador e a palavra-passe de administrador no **configurar início de sessão** formam e clique em **configurar agora**. Tenha em atenção que o nome de utilizador do administrador fornecido tem de ter o **security_admin** função atribuída em ServiceNow para isto funcionar. Caso contrário, para configurar manualmente o ServiceNow ao utilizar o Azure AD como um fornecedor de identidade, clique em **configurar manualmente o início de sessão único** e copie o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** da secção de referência rápida.

    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/configure.png "configurar o URL da aplicação")

9. Inicie sessão aplicação ServiceNow como administrador.

10. Ativar o **integração - vários único início de sessão instalador de fornecedor** Plug-in, seguindo os passos seguintes:

    a. No painel de navegação no lado esquerdo, procurar **definição de sistema** secção na barra de pesquisa e, em seguida, clique em **plug-ins**.

    ![Ativar o plug-in](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "ativar Plug-in")

     b. Procurar **integração - vários único início de sessão instalador de fornecedor**.

     ![Ativar o plug-in](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "ativar Plug-in")

    c. Selecione o plug-in. Clique com o botão direito e selecione **ativar/atualização**.

    d. Clique em de **ativar** botão.

11. No painel de navegação no lado esquerdo, procurar **SSO do fornecedor de Multi** secção na barra de pesquisa e, em seguida, clique em **propriedades**.

    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "configurar o URL da aplicação")

12. No **várias propriedades do fornecedor de SSO** caixa de diálogo, execute os seguintes passos:

    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/ic7694981.png "configurar o URL da aplicação")

    a. Como **ativar o fornecedor de várias SSO**, selecione **Sim**.

    b. Como **ativar automaticamente a importação de utilizadores de todos os fornecedores de identidade na tabela de utilizador**, selecione **Sim**.

    c. Como **depuração de ativar o registo para o fornecedor de várias integração SSO**, selecione **Sim**.

    d. No **o campo no utilizador tabela que...**  caixa de texto, tipo **user_name**.

    e. Clique em **Guardar**.

13. No painel de navegação no lado esquerdo, procurar **SSO do fornecedor de Multi** secção na barra de pesquisa e, em seguida, clique em **certificados x509**.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "configurar o início de sessão único")

14. No **certificados x. 509** caixa de diálogo, clique em **novo**.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694974.png "configurar o início de sessão único")

15. No **certificados x. 509** caixa de diálogo, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "configurar o início de sessão único")

    a. No **nome** caixa de texto, escreva um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Active Directory**.

    c. Como **formato**, selecione **PEM**.

    d. Como **tipo**, selecione **confiar o arquivo de certificados**.

    e. Abra o certificado codificado em Base64 transferido a partir do Azure no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **PEM certificado** caixa de texto.

     f. Clique em **submeter**.

16. No painel de navegação no lado esquerdo, clique em **fornecedores de identidade**.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "configurar o início de sessão único")

17. No **fornecedores de identidade** caixa de diálogo, clique em **novo**.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694977.png "configurar o início de sessão único")

18. No **fornecedores de identidade** caixa de diálogo, clique em **SAML2 atualização1?**.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694978.png "configurar o início de sessão único")

19. Na caixa de diálogo Propriedades de atualização1 SAML2, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/idp.png "configurar o início de sessão único")

    a. Selecione **URL** opção **metadados de fornecedor de identidade de importação** caixa de diálogo.

    b. Introduza o **URL de metadados** gerado a partir do portal do Azure.

    c. Clique em **importar**.

20. Lê o URL de metadados do IdP e preenche a todas as informações de campos.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694982.png "configurar o início de sessão único")

    a. No **nome** caixa de texto, escreva um nome para a sua configuração (por exemplo, **SAML 2.0**).

    b. No **campo utilizador** caixa de texto, tipo **e-mail** ou **user_name**, consoante o campo que é utilizado para identificar exclusivamente os utilizadores na sua implementação do ServiceNow.

    > [!NOTE]
    > Pode configurar o Azure AD para emitir o ID de utilizador do Azure AD (nome principal de utilizador) ou o endereço de e-mail como o identificador exclusivo no SAML token acedendo ao **ServiceNow > atributos > Single Sign-On** secção do portal do Azure e o mapeamento do campo pretendido para o **nameidentifier** atributo. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal de utilizador) tem de corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name)

    c. Cópia **home page do ServiceNow** valor, cole-a no **URL de início de sessão** textbox em **ServiceNow domínio e os URLs** secção no portal do Azure.

    > [!NOTE]
    > Home page ServiceNow instância é uma concatenação do seu **URL de inquilino ServieNow** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).

    d. Cópia **ID de entidade / emissor** valor, cole-a no **identificador** textbox em **ServiceNow domínio e os URLs** secção no portal do Azure.

     e. Em **x509 certificado**, apresenta o certificado que criou no passo anterior.

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Configurar o Azure AD início de sessão único para ServiceNow rápida

1. No portal do Azure, no **ServiceNow** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_samlbase.png)

3. No **ServiceNow domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_url.png)

    a. No **URL de início de sessão** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://<instance-name>.service-now.com/navpage.do`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte [equipa de suporte de cliente do ServiceNow](https://www.servicenow.com/support/contact-support.html) para obter estes valores.

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_certificate.png)

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_general_400.png)

6. Um clique configurar o serviço é fornecida para ServiceNow ou seja, para que o Azure AD configurar automaticamente ServiceNow para autenticação baseada em SAML. Para ativar este serviço, aceda ao **ServiceNow configuração** secção, clique em **configurar ServiceNow** para abrir a janela de início de sessão de configurar.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_configure.png)

7. Introduza o seu nome de instância do ServiceNow, o nome de utilizador de administrador e a palavra-passe de administrador no **configurar início de sessão** formam e clique em **configurar agora**. Tenha em atenção que o nome de utilizador do administrador fornecido tem de ter o **security_admin** função atribuída em ServiceNow para isto funcionar. Caso contrário, para configurar manualmente o ServiceNow ao utilizar o Azure AD como um fornecedor de identidade, clique em **configurar manualmente o início de sessão único** e copie o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** da secção de referência rápida.

    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/configure.png "configurar o URL da aplicação")

8. Inicie sessão aplicação ServiceNow Express como administrador.

9. No painel de navegação no lado esquerdo, clique em **Single Sign-On**.

    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "configurar o URL da aplicação")

10. No **Single Sign-On** caixa de diálogo, clique no ícone de configuração no canto superior direito e defina as seguintes propriedades:

    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "configurar o URL da aplicação")

    a. Ativar/desativar **ativar o fornecedor de várias SSO** à direita.
    
    b. Ativar/desativar **depuração de ativar o registo para o fornecedor de várias integração SSO** à direita.
    
    c. No **o campo no utilizador tabela que...**  caixa de texto, tipo **user_name**.

11. No **Single Sign-On** caixa de diálogo, clique em **adicionar novo certificado**.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "configurar o início de sessão único")

12. No **certificados x. 509** caixa de diálogo, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694975.png "configurar o início de sessão único")

    a. No **nome** caixa de texto, escreva um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **Active Directory**.

    c. Como **formato**, selecione **PEM**.

    d. Como **tipo**, selecione **confiar o arquivo de certificados**.

    e. Abra o certificado codificado em Base64 transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **PEM certificado** caixa de texto.

    f. Clique em **atualização**

13. No **Single Sign-On** caixa de diálogo, clique em **Adicionar nova IdP**.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "configurar o início de sessão único")

14. No **adicionar novo fornecedor de identidade** caixa de diálogo, em **configurar o fornecedor de identidade**, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "configurar o início de sessão único")

    a. No **nome** caixa de texto, escreva um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. No **URL do fornecedor de identidade** campo, cole o valor de **ID do fornecedor de identidade** que copiou do portal do Azure.
    
    c. No **AuthnRequest do fornecedor de identidade** campo, cole o valor de **URL de pedido de autenticação** que copiou do portal do Azure.

    d. No **SingleLogoutRequest do fornecedor de identidade** campo, cole o valor de **único URL de serviço Sign-Out** que copiou do portal do Azure

    e. Como **certificado do fornecedor de identidade**, selecione o certificado que criou no passo anterior.

15. Clique em **definições avançadas**e, em **propriedades do fornecedor de identidade adicionais**, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "configurar o início de sessão único")

    a. No **protocolo do enlace para o IDP SingleLogoutRequest** caixa de texto, tipo **urn: oasis: os nomes: tc: SAML:2.0:bindings:HTTP-redirecionar**.

    b. No **NameID política** caixa de texto, tipo **urn: oasis: os nomes: tc: SAML:1.1:nameid-formato: não especificado**.

    c. No **AuthnContextClassRef método**, tipo `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Anular seleção de **criar um AuthnContextClass**.

16. Em **propriedades adicionais de fornecedor de serviço**, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "configurar o início de sessão único")

    a. No **home page do ServiceNow** caixa de texto, escreva o URL da sua home page de instância de ServiceNow.

    > [!NOTE]
    > Home page ServiceNow instância é uma concatenação do seu **URL de inquilino ServieNow** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. No **ID de entidade / emissor** caixa de texto, escreva o URL do seu inquilino do ServiceNow.

    c. No **URI de audiência** caixa de texto, escreva o URL do seu inquilino do ServiceNow.

    d. No **desfasamento de relógio** caixa de texto, tipo **60**.

    e. No **campo utilizador** caixa de texto, tipo **e-mail** ou **user_name**, consoante o campo que é utilizado para identificar exclusivamente os utilizadores na sua implementação do ServiceNow.

    > [!NOTE]
    > Pode configurar o Azure AD para emitir o ID de utilizador do Azure AD (nome principal de utilizador) ou o endereço de e-mail como o identificador exclusivo no SAML token acedendo ao **ServiceNow > atributos > Single Sign-On** secção do portal do Azure e o mapeamento do campo pretendido para o **nameidentifier** atributo. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal de utilizador) tem de corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name)

    f. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-servicenow-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-servicenow-test-user"></a>Criar um utilizador de teste do ServiceNow

Nesta secção, vai criar um utilizador chamado Britta Simon ServiceNow. Se não souber como adicionar um utilizador na sua conta ServiceNow ou rápida do ServiceNow, contacte [equipa de suporte de cliente do ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para ServiceNow.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a ServiceNow, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **ServiceNow**.

    ![A ligação do ServiceNow na lista de aplicações](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de ServiceNow, no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação do ServiceNow.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png

