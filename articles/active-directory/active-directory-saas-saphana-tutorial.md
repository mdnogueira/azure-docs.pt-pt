---
title: "Tutorial: Integração do Azure Active Directory com SAP HANA | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e SAP HANA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: a7e73f6ee763d1005ad85935cf2d8f6b24ecf116
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Integração do Azure Active Directory com SAP HANA

Neste tutorial, irá aprender a integração de SAP HANA com o Azure Active Directory (Azure AD).

A integração de SAP HANA com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao SAP HANA
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para SAP HANA (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SAP HANA, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um SAP HANA-início de sessão único ativada subscrição
- Instância de um HANA em execução em qualquer IaaS público, no local, as VMs do Azure ou instâncias de grande SAP no Azure
- A Interface de Web de administração de XSA, bem como Studio HANA instalada na instância HANA.

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção de SAP HANA. Testar a integração pela primeira vez no desenvolvimento ou de ambiente de teste da aplicação e, em seguida, utilizar o ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de SAP HANA de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-sap-hana-from-the-gallery"></a>A adição de SAP HANA de galeria
Para configurar a integração de SAP HANA com o Azure AD, tem de adicionar SAP HANA na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar SAP HANA a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **SAP HANA**, selecione **SAP HANA** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação. 

    ![A nova aplicação](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com SAP HANA com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no SAP HANA é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SAP HANA tem de ser estabelecida.

No SAP HANA, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com SAP HANA, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de SAP HANA](#creating-a-sap-hana-test-user)**  - para ter um homólogo de Britta Simon SAP HANA que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de SAP HANA.

**Para configurar o Azure AD-início de sessão único com SAP HANA, execute os seguintes passos:**

1. No portal do Azure, no **SAP HANA** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. No **SAP HANA domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. No **identificador** caixa de texto, tipo como:`HA100` 

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador e o URL de resposta real. Contacte [equipa de suporte de SAP HANA cliente](https://cloudplatform.sap.com/contact.html) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Se o certificado não está ativo, em seguida, torne-a ativa clicando na caixa de verificação "Tornar novo certificado ativa" no Azure AD. 

5. Aplicação de SAP HANA espera as asserções de SAML num formato específico. A seguinte captura de ecrã mostra um exemplo para este. Aqui é ter mapeado os **identificador de utilizador** com **ExtractMailPrefix()** função do **user.mail**. Isto permite que o valor de prefixo de e-mail do utilizador que é o ID de utilizador exclusivo. Esta é enviada para a aplicação de SAP HANA em cada resposta com êxito.

    ![Configurar o início de sessão único](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. No **atributos de utilizador** secção no **de sessão único-** diálogo:

    a. No **identificador de utilizador** na lista pendente, selecione **ExtractMailPrefix**.
    
    b. No **correio** na lista pendente, selecione **user.mail**.

7. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Para configurar o início de sessão único em **SAP HANA** lado, inicie sessão no seu **HANA XSA Web consola** ao navegar para o respetivo-ponto final de HTTPS.

    > [!Note]
    > Na configuração predefinida, o URL redireciona o pedido para um ecrã de início de sessão, o que requer que as credenciais de um utilizador de base de dados SAP HANA autenticado para concluir o processo de início de sessão. O utilizador que inicie sessão tem de ter os privilégios necessários para efetuar tarefas de administração de SAML.

9. Na Interface de Web XSA, navegue para **fornecedor de identidade** e a partir daí, clique em de **"+"** -botão na parte inferior do ecrã, para apresentar o painel adicionar informações do fornecedor de identidade e execute os seguintes passos:

    ![Adicione o fornecedor de identidade](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. No **adicionar informações do fornecedor de identidade** painel, cole o conteúdo do XML de metadados, que transferiu a partir do portal do Azure para o **metadados** caixa de texto.

    ![Adicionar as definições do fornecedor de identidade](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Se o conteúdo do documento XML válido, o processo de análise extrai as informações necessárias para inserir o **requerente, o ID de entidade e o emissor** campos de dados gerais de ecrã área e os campos de URL, na área de ecrã de destino, por exemplo,  **Base URL e o URL de SingleSignOn (*)**.

    ![Adicionar as definições do fornecedor de identidade](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. Na caixa de nome da área do ecrã de dados gerais, introduza um nome para o novo fornecedor de identidade de SAML SSO.

    > [!Note]
    > O nome do SAML IDP é obrigatório e tem de ser exclusivo; é apresentado na lista de IDPs SAML disponível que é apresentado se seleciona SAML como método de autenticação para aplicações de SAP HANA XS a utilizar, por exemplo, na área de ecrã de autenticação da ferramenta de administração de artefacto XS.

10. Guarde os detalhes do fornecedor de identidade SAML novo. Escolha **guardar** para guardar os detalhes do fornecedor de identidade SAML e adicionar novos IDP SAML à lista de conhecidos IDPs de SAML.

    ![botão Guardar](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. No Studio HANA dentro as propriedades do sistema do **configuração** separador, apenas filtrar definições por **saml** e ajuste o **assertion_timeout** de **10 seg** para **seg 120**.

    ![definição de assertion_timeout](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![O botão de adição](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sap-hana-test-user"></a>Criar um utilizador de teste de SAP HANA

Para ativar a utilizadores do Azure AD para início de sessão para SAP HANA, têm de ser aprovisionados para SAP HANA.
SAP HANA suporta o aprovisionamento de just-in-time, que está por predefinição, ativada.

Se precisar de criar manualmente um utilizador, execute os seguintes passos:

>[!Note]
>Pode alterar a autenticação externa utilizada pelo utilizador.
Os utilizadores externos são autenticados utilizando um sistema externo, por exemplo um sistema de Kerberos. Para obter informações detalhadas sobre as identidades externas, contacte o seu [administrador de domínio](https://cloudplatform.sap.com/contact.html).

1. Abra o [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador e ativar o utilizador de base de dados para SAML SSO.

    ![Criar utilizador](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. A caixa de verificação invisível para a esquerda de escala **SAML** e siga a ligação de configurar.

3. Clique em **adicionar** adicionar SAML IDP e clique em **OK** selecionar IDP SAML adequado.

4. Adicionar o **identidade externas** (ex. BrittaSimon aqui) ou escolha **"Qualquer"** e clique em **OK**.

    >[!Note]
    >Se não estiver marcada "Qualquer" caixa de verificação, em seguida, o nome de utilizador no HANA tem de corresponder exatamente o nome do utilizador no UPN antes do sufixo de domínio (ou seja, BrittaSimon@contoso.com iria ficar BrittaSimon no HANA).

5. Para fins de teste, atribuir todos os **"XS"** funções ao utilizador.

    ![atribuir funções](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Deverá dar-essas permissões adequadas para os casos de utilização apenas.

6. Guarde o utilizador.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para SAP HANA.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon para SAP HANA, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **SAP HANA**.

    ![Atribua o utilizador](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de SAP HANA no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de SAP HANA.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png

