---
title: "Tutorial: Integração do Azure Active Directory com Qlik sentido empresarial | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Qlik sentido Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 4964634cd5aaf0dbb98c766f5e12700c4d118750
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Tutorial: Integração do Azure Active Directory com Qlik sentido empresarial

Neste tutorial, irá aprender a integrar Qlik sentido Enterprise com o Azure Active Directory (Azure AD).

Integrar Qlik sentido Enterprise com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Qlik sentido Enterprise.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para a empresa de sentido Qlik (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Qlik sentido empresarial, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma empresa de sentido Qlik-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Qlik sentido Enterprise da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adicionar Qlik sentido Enterprise da galeria do
Para configurar a integração de Qlik sentido empresa com o Azure AD, tem de adicionar Qlik sentido Enterprise da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Qlik sentido Enterprise da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Qlik sentido Enterprise**, selecione **Qlik sentido Enterprise** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Qlik sentido Enterprise na lista de resultados](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Qlik sentido Enterprise com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo Qlik sentido empresa for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado Qlik sentido empresa tem de ser estabelecida.

Numa empresa de sentido Qlik, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Qlik sentido empresarial, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Qlik sentido Enterprise](#create-a-qlik-sense-enterprise-test-user)**  - para ter um homólogo de Britta Simon Qlik sentido empresa que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Qlik sentido Enterprise.

**Para configurar o Azure AD-início de sessão único com Qlik sentido Enterprise, execute os seguintes passos:**

1. No portal do Azure, no **Qlik sentido Enterprise** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. No **Qlik sentido Enterprise domínio e os URLs** secção, execute os seguintes passos:

    ![URLs e de domínio de empresa Qlik sentido único informações de início de sessão](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`
    
    > [!NOTE]
    > Tenha em atenção a barra no final deste URI. É necessário.
    
    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualizar estes valores com o URL de início de sessão real e o identificador, que são explicado posteriormente neste tutorial ou contacte [equipa de suporte de cliente do Enterprise Qlik sentido](https://www.qlik.com/us/services/support) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Prepare o ficheiro XML de metadados de Federação, para que pode carregar que servidor Qlik sentido.
   
    > [!NOTE]
    > Antes de carregar os metadados do IdP para o servidor de Qlik sentido, o ficheiro tem de ser editadas para remover as informações para assegurar um funcionamento correto entre o Azure AD e o servidor de Qlik sentido.
    
    ![QlikSense][qs24]
   
    a. Abra o ficheiro de FederationMetaData.xml que transferiu a partir do portal do Azure num editor de texto.
   
    b. Pesquisa para o valor **RoleDescriptor**.  Existem quatro entradas (dois pares de abertura e fecho de tags de elemento).
   
    c. Elimine entre as etiquetas de RoleDescriptor e todas as informações do ficheiro.
   
    d. Guarde o ficheiro e de as manter utilizaria para utilização posterior neste documento.

7. Navegue para o Qlik sentido Qlik gestão consola (QMC) como um utilizador pode criar as configurações de virtual proxy.

8. No QMC, clique em de **Virtual Proxies** item de menu.
   
    ![QlikSense][qs6] 

9. Na parte inferior do ecrã, clique em de **criar nova** botão.
   
    ![QlikSense][qs7]

10. É apresentado o ecrã de edição de proxy Virtual.  No lado direito do ecrã é um menu para tornar as opções de configuração visível.
   
    ![QlikSense][qs9]

11. Com a opção de menu de identificação opção estiver marcada, introduza as informações de identificação para a configuração de proxy virtual do Azure.
    
    ![QlikSense][qs8]  
    
    a. O **Descrição** campo é um nome amigável para a configuração de virtual proxy.  Introduza um valor para uma descrição.
    
    b. O **prefixo** campo identifica o ponto final de virtual proxy para ligar ao Qlik sentido com o Azure AD Single Sign-On.  Introduza um nome de prefixo exclusivo para este proxy virtual.
    
    c. **Tempo limite de inatividade de sessão (minutos)** é o tempo limite para ligações através deste proxy virtual.
    
    d. O **nome de cabeçalho do cookie de sessão** é o nome do cookie armazenar o identificador de sessão para a sessão de Qlik sentido um utilizador recebe após a autenticação com êxito.  Este nome tem de ser exclusivo.

12. Clique na opção de menu de autenticação para torná-lo visível.  É apresentado o ecrã de autenticação.
    
    ![QlikSense][qs10]
    
    a. O **modo de acesso anónimo** pendente determina se os utilizadores anónimos podem aceder Qlik sentido através do proxy virtual.  A opção predefinida não é nenhum utilizador anónimo.
    
    b. O **método de autenticação** pendente determina o esquema de autenticação do proxy virtual irá utilizar.  Selecione SAML na lista pendente.  Mais opções aparecem como resultado.
    
    c. No **campo URI do anfitrião SAML**, os utilizadores de nome de anfitrião introduzem para aceder à Qlik sentido através do proxy de virtual este SAML de entrada.  O nome do anfitrião é o uri do servidor Qlik sentido.
    
    d. No **ID de entidade SAML**, introduza o mesmo valor introduzido para o campo URI do anfitrião SAML.
    
    e. O **SAML IdP metadados** é o ficheiro editado anteriormente no **editar os metadados de Federação da configuração do Azure AD** secção.  **Antes de carregar os metadados do IdP, o ficheiro tem de ser editada** remover as informações para assegurar um funcionamento correto entre o Azure AD e o servidor de Qlik sentido.  **Consulte as instruções anteriores se o ficheiro ainda tem de ser editado.**  Se o ficheiro foi editado clique no botão Procurar e selecione o ficheiro de metadados editado carregá-lo para a configuração de virtual proxy.
    
    f. Introduza a referência do atributo de nome ou o esquema para o atributo SAML que representa o **UserID** do Azure AD envia para o servidor de Qlik sentido.  Informações de referência de esquema estão disponíveis na configuração de post de ecrãs de aplicações do Azure.  Para utilizar o atributo de nome, introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    g. Introduza o valor para o **diretório utilizador** que será anexado aos utilizadores quando se autenticar para o servidor Qlik sentido através do Azure AD.  Codificado valores tem de ser rodeados por **Parênteses Retos []**.  Para utilizar um atributo enviado na asserção SAML do Azure AD, introduza o nome do atributo nesta caixa de texto **sem** Parênteses Retos.
    
    h. O **algoritmo de assinatura de SAML** define o certificado de fornecedor (por este servidor Qlik sentido maiúsculas) do serviço de assinatura para a configuração de virtual proxy.  Se o servidor de Qlik sentido utiliza um certificado fidedigno gerado utilizando o Microsoft avançada RSA e fornecedor de criptografia AES, altere o algoritmo de assinatura de SAML para **SHA-256**.
    
    posso. A secção de mapeamento de atributos SAML permite atributos adicionais, como grupos sejam enviados para Qlik sentido para utilização em regras de segurança.

13. Clique em de **BALANCEAMENTO de carga** opção de menu para torná-lo visível.  É apresentado o ecrã de balanceamento de carga.
    
    ![QlikSense][qs11]

14. Clique em de **adicionar novo nó de servidor** botão, o nó de motor selecione ou nós Qlik sentido enviará sessões para fins de balanceamento de carga e, clique em de **adicionar** botão.
    
    ![QlikSense][qs12]

15. Clique na opção de menu Avançadas para torná-lo visível. É apresentado o ecrã avançado.
    
    ![QlikSense][qs13]
    
    A lista de permissão de anfitrião identifica os nomes de anfitrião são aceites quando ligar ao servidor de Qlik sentido.  **Introduza o nome de anfitrião os utilizadores irão especificar quando ligar ao servidor Qlik sentido.** O nome do anfitrião é o mesmo valor que o uri de anfitrião SAML sem o https://.

16. Clique em de **aplicar** botão.
    
    ![QlikSense][qs14]

17. Clique em OK para aceitar a mensagem de aviso que indica a proxies ligados ao proxy virtual serão reiniciados.
    
    ![QlikSense][qs15]

18. No lado direito do ecrã, é apresentado o menu de itens associados.  Clique em de **Proxies** opção do menu.
    
    ![QlikSense][qs16]

19. É apresentado o ecrã de proxy.  Clique em de **ligação** na parte inferior para associar um proxy para o proxy virtual.
    
    ![QlikSense][qs17]

20. Selecione o nó de proxy que suporta esta ligação de virtual proxy e clique em de **ligação** botão.  Após a ligação, o proxy será listado sob proxies associados.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Depois de cerca de cinco a dez segundos, será apresentada a mensagem de QMC atualizar.  Clique em de **atualizar QMC** botão.
    
    ![QlikSense][qs20]

22. Quando atualiza o QMC, clique em de **Virtual proxies** item de menu. A nova entrada de virtual proxy SAML está listada na tabela no ecrã.  Único clique na entrada virtual proxy.
    
    ![QlikSense][qs51]

23. Na parte inferior do ecrã, o botão de metadados de transferir SP serão ativados.  Clique em de **SP transferir metadados** botão para guardar os metadados para um ficheiro.
    
    ![QlikSense][qs52]

24. Abra o ficheiro de metadados de sp.  Observar o **entityID** entrada e de **AssertionConsumerService** entrada.  Estes valores são equivalentes da **identificador** e o **iniciar sessão no URL** na configuração de aplicação do Azure AD. Colar estes valores no **Qlik sentido Enterprise domínio e os URLs** secção na configuração de aplicação do Azure AD Se estes não correspondentes, em seguida, deve substituí-los no Assistente de configuração de aplicação do Azure AD.
    
    ![QlikSense][qs53]

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

   ![O botão do Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

   !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

   ![O botão de adição](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

   ![A caixa de diálogo de utilizador](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. No **nome** caixa, escreva **BrittaSimon**.

   b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

   c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

   d. Clique em **Criar**.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Criar um utilizador de teste Qlik sentido Enterprise

Nesta secção, vai criar um utilizador chamado Britta Simon Qlik sentido empresa. Trabalhar com [equipa de suporte de cliente do Enterprise Qlik sentido](https://www.qlik.com/us/services/support) para adicionar os utilizadores na plataforma Qlik sentido Enterprise. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso a Qlik sentido empresarial.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Qlik sentido Enterprise, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Qlik sentido Enterprise**.

    ![A ligação de Qlik sentido Enterprise na lista de aplicações](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Qlik sentido empresarial no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Qlik sentido Enterprise. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

