---
title: "Tutorial: Integração do Azure Active Directory com o ServiceNow | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e ServiceNow e ServiceNow rápida."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a91fab90a94b655b93c8ae9064ea4836b80d7678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Tutorial: Integração do Azure Active Directory com o ServiceNow
Neste tutorial, irá aprender integrar o ServiceNow e ServiceNow Express com o Azure Active Directory (Azure AD).

Integrar o ServiceNow e ServiceNow Express com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao ServiceNow e ServiceNow rápida
* Pode permitir que os utilizadores automaticamente obter com sessão iniciada para ServiceNow e ServiceNow rápida (Single Sign-On) com as respetivas contas do Azure AD
* Pode gerir as contas numa localização central - portal clássico do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com ServiceNow e Express do ServiceNow, terá dos seguintes itens:

* Uma subscrição do Azure AD
* Para o ServiceNow, uma instância ou o inquilino do ServiceNow, versão Calgary ou superior
* Para expressas ServiceNow, uma instância do ServiceNow rápida, versão Helsinki ou superior
* O inquilino do ServiceNow tem de ter o [vários fornecedor único início de sessão no plug-in](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) ativada. Isto pode ser feito [submeter um pedido de serviço](https://hi.service-now.com). 

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.
> 
> 

Para testar os passos neste tutorial, deve seguir estas recomendações:

* Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
* Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar ServiceNow a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-ServiceNow ou ServiceNow rápida

## <a name="adding-servicenow-from-the-gallery"></a>Adicionar ServiceNow a partir da Galeria
Para configurar a integração do ServiceNow ou ServiceNow Express com o Azure AD, terá de adicionar ServiceNow a partir da Galeria à sua lista de aplicações SaaS geridas. 

**Para adicionar ServiceNow a partir da galeria, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**. 
   
    ![Active Directory][1]
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Aplicações][2]
4. Clique em **adicionar** na parte inferior da página.
   
    ![Aplicações][3]
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
    ![Aplicações][4]
6. Na caixa de pesquisa, escreva **ServiceNow**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. No painel de resultados, selecione **ServiceNow**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configura e teste do Azure AD-início de sessão único com ServiceNow ou ServiceNow rápida com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no ServiceNow é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ServiceNow tem de ser estabelecida.
Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no ServiceNow. Para configurar e testar o Azure AD-início de sessão único com ServiceNow, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On para ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o Azure AD Single Sign-On para ServiceNow rápida](#configuring-azure-ad-single-sign-on-for-servicenow-express)**  - para permitir aos utilizadores utilizar esta funcionalidade.
3. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
4. **[Criar um utilizador de teste do ServiceNow](#creating-a-servicenow-test-user)**  - para ter um homólogo de Britta Simon ServiceNow que está ligada a representação do Azure AD da forma.
5. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
6. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

> [!NOTE]
> Se pretender configurar ServiceNow omitir o passo 2. Da mesma forma, se pretender configurar ServiceNow rápida omitir o passo 1.
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configurar o Azure AD início de sessão único para o ServiceNow
1. No portal clássico do Azure AD, no **ServiceNow** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar início de sessão único** caixa de diálogo.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "configurar o início de sessão único")

2. No **como gostaria aos utilizadores iniciar sessão ServiceNow** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "configurar o início de sessão único")

3. No **configurar definições de aplicação** página, execute os seguintes passos:
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/IC769497.png "configurar o URL da aplicação")
   
    a. no **ServiceNow no URL sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para início de sessão ao seguir o padrão de aplicação ServiceNow: `https://<instance-name>.service-now.com`.
   
    b. No **identificador** caixa de texto, escreva o URL é utilizado pelos seus utilizadores para início de sessão ao seguir o padrão de aplicação ServiceNow: `https://<instance-name>.service-now.com`.
   
    c. Clique em **Seguinte**

4. Para ter o Azure AD configurar automaticamente o ServiceNow para autenticação baseada em SAML, introduza o nome da instância do ServiceNow, admin nome de utilizador e palavra-passe de administrador no **automática configurar o início de sessão único** formam e clique em  *Configurar*. Tenha em atenção que o nome de utilizador do administrador fornecido tem de ter o **security_admin** função atribuída em ServiceNow para isto funcionar. Caso contrário, para configurar manualmente o ServiceNow ao utilizar o Azure AD como um fornecedor de identidade, clique em **configurar manualmente a aplicação para o início de sessão único**, em seguida, clique em **seguinte** e conclua os passos seguintes .
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "configurar o URL da aplicação")

5. No **configurar o início de sessão único em ServiceNow** página, clique em **Transferir certificado**, guarde o ficheiro de certificado localmente no seu computador.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "configurar o início de sessão único")

6. Início de sessão para a aplicação de ServiceNow como administrador.

7. Ativar o *integração - vários único início de sessão instalador de fornecedor* Plug-in, seguindo os passos seguintes:
   
    a. No painel de navegação no lado esquerdo, aceda a **definição de sistema** secção e, em seguida, clique em **plug-ins**.
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "ativar Plug-in")
   
    b. Procurar *integração - vários único início de sessão instalador de fornecedor*.
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "ativar Plug-in")
   
    c. Selecione o plug-in. Rigth clique e selecione **ativar/atualização**.
   
    d. Clique em de **ativar** botão.

8. No painel de navegação no lado esquerdo, clique em **propriedades**.  
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "configurar o URL da aplicação")

9. No **várias propriedades do fornecedor de SSO** caixa de diálogo, execute os seguintes passos:
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "configurar o URL da aplicação")
   
    a. Como **ativar o fornecedor de várias SSO**, selecione **Sim**.
   
    b. Como **ativar registo de depuração recebeu o fornecedor de várias integração SSO**, selecione **Sim**.
   
    c. No **o campo no utilizador tabela que...**  caixa de texto, tipo **user_name**.
   
    d. Clique em **Guardar**.

10. No painel de navegação no lado esquerdo, clique em **certificados x509**.
    
     ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "configurar o início de sessão único")

11. No **certificados x. 509** caixa de diálogo, clique em **novo**.
    
     ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "configurar o início de sessão único")

12. No **certificados x. 509** caixa de diálogo, execute os seguintes passos:
    
     ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "configurar o início de sessão único")
    
     a. Clique em **Novo**.
    
     b. No **nome** caixa de texto, escreva um nome para a sua configuração (por ex.: **TestSAML2.0**).
    
     c. Selecione **Active Directory**.
    
     d. Como **formato**, selecione **PEM**.
    
     e. Como **tipo**, selecione **confiar o arquivo de certificados**.
    
     f. Abra o certificado codificado em Base64 no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **PEM certificado** caixa de texto.
    
     g. Clique em **atualização**.

13. No painel de navegação no lado esquerdo, clique em **fornecedores de identidade**.
    
     ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "configurar o início de sessão único")

14. No **fornecedores de identidade** caixa de diálogo, clique em **novo**:
    
     ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "configurar o início de sessão único")

15. No **fornecedores de identidade** caixa de diálogo, clique em **SAML2 atualização1?**:
    
     ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "configurar o início de sessão único")

16. Na caixa de diálogo Propriedades de atualização1 SAML2, execute os seguintes passos:
    
     ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "configurar o início de sessão único")

    a. no **nome** caixa de texto, escreva um nome para a sua configuração (por ex.: **SAML 2.0**).

    b. No **campo utilizador** caixa de texto, tipo **e-mail** ou **user_name**, consoante o campo que é utilizado para identificar exclusivamente os utilizadores na sua implementação do ServiceNow. 

    > [!NOTE] 
    > Pode configue do Azure AD para emitir o ID de utilizador do Azure AD (nome principal de utilizador) ou o endereço de e-mail como o identificador exclusivo no SAML token acedendo ao **ServiceNow > atributos > Single Sign-On** secção clássica do Azure Portal e o mapeamento do campo pretendido para o **nameidentifier** atributo. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal de utilizador) tem de corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name)

    c. No portal clássico do Azure AD, copie o **ID do fornecedor de identidade** valor e, em seguida, cole-o para o **URL do fornecedor de identidade** caixa de texto.

    d. No portal clássico do Azure AD, copie o **URL de pedido de autenticação** valor e, em seguida, cole-o para o **AuthnRequest do fornecedor de identidade** caixa de texto.

    e. No portal clássico do Azure AD, copie o **único URL de serviço Sign-Out** valor e, em seguida, cole-o para o **SingleLogoutRequest do fornecedor de identidade** caixa de texto.

    f. No **home page do ServiceNow** caixa de texto, escreva o URL da sua home page de instância de ServiceNow.

    > [!NOTE] 
    > Home page ServiceNow instância é uma concatenação do seu **ServieNow URL de inquilino** e **/navpage.do** (por ex.:`https://fabrikam.service-now.com/navpage.do`).

    g. No **ID de entidade / emissor** caixa de texto, escreva o URL do seu inquilino do ServiceNow.

    h. No **URL público-alvo** caixa de texto, escreva o URL do seu inquilino do ServiceNow. 

    posso. No **protocolo do enlace para o IDP SingleLogoutRequest** caixa de texto, tipo **urn: oasis: os nomes: tc: SAML:2.0:bindings:HTTP-redirecionar**.

    j. Na caixa de texto NameID política, escreva **urn: oasis: os nomes: tc: SAML:1.1:nameid-formato: não especificado**.

    k. Anular seleção de **criar um AuthnContextClass**.

    l. No **AuthnContextClassRef método**, tipo `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Só é necessária se estiverem organização apenas de nuvem. Se estiver a utilizar no local ADFS ou MFA para a autenticação, em seguida, não deve configurar este valor. 

    m. No **desfasamento de relógio** caixa de texto, tipo **60**.

    n. Como **início de sessão único no Script**, selecione **MultiSSO_SAML2_Update1**.

    Nã. Como **x509 certificado**, selecione o certificado que criou no passo anterior.

    p. Clique em **submeter**. 

1. No portal clássico do Azure AD, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "configurar o início de sessão único")

2. No **único início de sessão confirmação** página, clique em **concluída**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "configurar o início de sessão único")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configurar o Azure AD início de sessão único para ServiceNow rápida
1. No portal clássico do Azure AD, no **ServiceNow** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar início de sessão único** caixa de diálogo.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "configurar o início de sessão único")

2. No **como gostaria aos utilizadores iniciar sessão ServiceNow** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "configurar o início de sessão único")

3. No **configurar definições de aplicação** página, execute os seguintes passos:
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/IC769497.png "configurar o URL da aplicação")
   
    a. no **ServiceNow no URL sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para início de sessão ao seguir o padrão de aplicação ServiceNow: `https://<instance-name>.service-now.com`.
   
    b. No **URL do emissor** caixa de texto, escreva o URL é utilizado pelos seus utilizadores para início de sessão ao seguir o padrão de aplicação ServiceNow `https://<instance-name>.service-now.com`.
   
    c. Clique em **Seguinte**

4. Clique em **configurar manualmente a aplicação para o início de sessão único**, em seguida, clique em **seguinte** e conclua os passos seguintes.
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "configurar o URL da aplicação")

5. No **configurar o início de sessão único em ServiceNow** página, clique em **Transferir certificado**, guarde o ficheiro de certificado localmente no seu computador e, em seguida, clique em **seguinte**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "configurar o início de sessão único")

6. Início de sessão da aplicação rápida do ServiceNow como administrador.

7. No painel de navegação no lado esquerdo, clique em **Single Sign-On**.  
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "configurar o URL da aplicação")

8. No **Single Sign-On** caixa de diálogo, clique no ícone de configuração no canto superior direito e defina as seguintes propriedades:
   
    ![Configurar o URL da aplicação](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "configurar o URL da aplicação")
   
    a. Ativar/desativar **ativar o fornecedor de várias SSO** à direita.
   
    b. Ativar/desativar **depuração de ativar o registo para o fornecedor de várias integração SSO** à direita.
   
    c. No **o campo no utilizador tabela que...**  caixa de texto, tipo **user_name**.
9. No **Single Sign-On** caixa de diálogo, clique em **adicionar novo certificado**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "configurar o início de sessão único")
10. No **certificados x. 509** caixa de diálogo, execute os seguintes passos:
    
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "configurar o início de sessão único")
    
    a. No **nome** caixa de texto, escreva um nome para a sua configuração (por ex.: **TestSAML2.0**).
    
    b. Selecione **Active Directory**.
    
    c. Como **formato**, selecione **PEM**.
    
    d. Como **tipo**, selecione **confiar o arquivo de certificados**.
    
    e. Crie um ficheiro de codificado em Base64 a partir do seu certificado transferido.
    
    > [!NOTE]
    > Para obter mais detalhes, consulte [como converter um certificado de binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).
    > 
    > 
    
    f. Abra o certificado codificado em Base64 no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **PEM certificado** caixa de texto.
    
    g. Clique em **atualização**.
11. No **Single Sign-On** caixa de diálogo, clique em **Adicionar nova IdP**.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "configurar o início de sessão único")
12. No **adicionar novo fornecedor de identidade** caixa de diálogo, em **configurar o fornecedor de identidade**, execute os seguintes passos:
    
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "configurar o início de sessão único")

    a. no **nome** caixa de texto, escreva um nome para a sua configuração (por ex.: **SAML 2.0**).

    b. No portal clássico do Azure AD, copie o **ID do fornecedor de identidade** valor e, em seguida, cole-o para o **URL do fornecedor de identidade** caixa de texto.

    c. No portal clássico do Azure AD, copie o **URL de pedido de autenticação** valor e, em seguida, cole-o para o **AuthnRequest do fornecedor de identidade** caixa de texto.

    d. No portal clássico do Azure AD, copie o **único URL de serviço Sign-Out** valor e, em seguida, cole-o para o **SingleLogoutRequest do fornecedor de identidade** caixa de texto.

    e. Como **certificado do fornecedor de identidade**, selecione o certificado que criou no passo anterior.


1. Clique em **definições avançadas**e, em **propriedades do fornecedor de identidade adicionais**, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "configurar o início de sessão único")
   
    a. No **protocolo do enlace para o IDP SingleLogoutRequest** caixa de texto, tipo **urn: oasis: os nomes: tc: SAML:2.0:bindings:HTTP-redirecionar**.
   
    b. No **NameID política** caixa de texto, tipo **urn: oasis: os nomes: tc: SAML:1.1:nameid-formato: não especificado**.    
   
    c. No **AuthnContextClassRef método**, tipo **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
   
    d. Anular seleção de **criar um AuthnContextClass**.

2. Em **propriedades adicionais de fornecedor de serviço**, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "configurar o início de sessão único")
   
    a. No **home page do ServiceNow** caixa de texto, escreva o URL da sua home page de instância de ServiceNow.
   
    > [!NOTE]
    > Home page ServiceNow instância é uma concatenação do seu **ServieNow URL de inquilino** e **/navpage.do** (por ex.: `https://fabrikam.service-now.com/navpage.do`).
    > 
    > 
   
    b. No **ID de entidade / emissor** caixa de texto, escreva o URL do seu inquilino do ServiceNow.
   
    c. No **URI de audiência** caixa de texto, escreva o URL do seu inquilino do ServiceNow. 
   
    d. No **desfasamento de relógio** caixa de texto, tipo **60**.
   
    e. No **campo utilizador** caixa de texto, tipo **e-mail** ou **user_name**, consoante o campo que é utilizado para identificar exclusivamente os utilizadores na sua implementação do ServiceNow.
   
    > [!NOTE]
    > Pode configue do Azure AD para emitir o ID de utilizador do Azure AD (nome principal de utilizador) ou o endereço de e-mail como o identificador exclusivo no SAML token acedendo ao **ServiceNow > atributos > Single Sign-On** secção clássica do Azure Portal e o mapeamento do campo pretendido para o **nameidentifier** atributo. O valor armazenado para o atributo selecionado no Azure AD (por exemplo, nome principal de utilizador) tem de corresponder ao valor armazenado no ServiceNow para o campo introduzido (por exemplo, user_name)
    > 
    > 
   
    f. Clique em **Guardar**. 

3. No portal clássico do Azure AD, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "configurar o início de sessão único")

4. No **único início de sessão confirmação** página, clique em **concluída**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "configurar o início de sessão único")

## <a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizadores
O objetivo desta secção consiste em descrevem como ativar o aprovisionamento de utilizadores do Active Directory de contas de utilizador para ServiceNow.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:
1. No portal clássico do Azure Management, no **ServiceNow** página de integração de aplicações, clique em **configurar o aprovisionamento de utilizadores**. 
   
    ![Aprovisionamento de utilizadores](./media/active-directory-saas-servicenow-tutorial/IC769498.png "aprovisionamento de utilizadores")

2. No **introduza as suas credenciais do ServiceNow para ativar o aprovisionamento de utilizador automáticas** página, forneça as seguintes definições de configuração:
   
     a. No **nome da instância do ServiceNow** caixa de texto, escreva o nome de instância do ServiceNow.
   
     b. No **nome de utilizador de Admin do ServiceNow** caixa de texto, escreva o nome da conta de administrador do ServiceNow.
   
     c. No **palavra-passe de administrador de ServiceNow** caixa de texto, escreva a palavra-passe para esta conta.
   
     d. Clique em **validar** para verificar a configuração.
   
     e. Clique em de **seguinte** botão para abrir o **passos** página.
   
     f. Se pretende aprovisionar todos os utilizadores para esta aplicação, selecione "**aprovisionar automaticamente todas as contas de utilizador no diretório para esta aplicação**". 
   
    ![Próximos passos](./media/active-directory-saas-servicenow-tutorial/IC698804.png "próximos passos")
   
     g. No **passos** página, clique em **concluída** para guardar a configuração.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
Nesta secção, vai criar um utilizador de teste no portal clássico do chamado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu na parte superior, clique em **utilizadores**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Para abrir o **adicionar utilizador** caixa de diálogo, na barra de ferramentas na parte inferior, clique em **adicionar utilizador**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. No **diga-nos informações sobre este utilizador** diálogo página, execute os seguintes passos:
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a. Como tipo de utilizador, selecione o novo utilizador na sua organização.
   
    b. O nome de utilizador **textbox**, tipo **BrittaSimon**.
   
    c. Clique em **Seguinte**.

6. No **perfil de utilizador** diálogo página, execute os seguintes passos:
   
   ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a. No **nome próprio** caixa de texto, tipo **Britta**.  
   
   b. No **Apelido** caixa de texto, tipo, **Simon**.
   
   c. No **nome a apresentar** caixa de texto, tipo **Britta Simon**.
   
   d. No **função** lista, selecione **utilizador**.
   
   e. Clique em **Seguinte**.

7. No **Get palavra-passe temporária** página da caixa de diálogo, clique em **criar**.
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. No **Get palavra-passe temporária** diálogo página, execute os seguintes passos:
   
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **nova palavra-passe**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-servicenow-test-user"></a>Criar um utilizador de teste do ServiceNow
Nesta secção, vai criar um utilizador chamado Britta Simon ServiceNow. Nesta secção, vai criar um utilizador chamado Britta Simon ServiceNow. Se não souber como adicionar um utilizador na sua conta ServiceNow ou rápida do ServiceNow, contacte a equipa de suporte do ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD
Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao ServiceNow.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a ServiceNow, execute os seguintes passos:**

1. No portal clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **ServiceNow**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. No menu na parte superior, clique em **utilizadores**.
   
    ![Atribua o utilizador][203] 

4. Na lista de todos os utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
   
    ![Atribua o utilizador][205]

### <a name="testing-single-sign-on"></a>Teste o início de sessão único
O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de ServiceNow, no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação do ServiceNow.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
