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
ms.openlocfilehash: 02c751b5d9e6f13c81b9c957cd6689c1fc744a07
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Integração do Azure Active Directory com SAP HANA

Neste tutorial, irá aprender a integração de SAP HANA com o Azure Active Directory (Azure AD).

Quando integrar o SAP HANA com o Azure AD, obtenha as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao SAP HANA.
- Pode permitir aos utilizadores obter iniciada automaticamente SAP HANA com as respetivas contas do Azure AD.
- Pode gerir as contas numa única localização central, o portal do Azure.

Para obter mais informações sobre a integração de aplicações SaaS com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SAP HANA, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma subscrição de SAP HANA-início de sessão único (SSO) ativado
- Uma instância HANA que está em execução no IaaS qualquer público, no local, a VM do Azure ou a instâncias de grande SAP no Azure
- A interface de web de administração de XSA, bem como Studio HANA instalada na instância HANA

> [!NOTE]
> Não é recomendada a utilização de um ambiente de produção de SAP HANA para testar os passos neste tutorial. Testar a integração pela primeira vez no desenvolvimento ou ambiente de teste da aplicação e, em seguida, utilize o ambiente de produção.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, exceto se for necessário.
- [Obtenha uma avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/) do Azure AD, se ainda não tiver um ambiente de avaliação do Azure AD.

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário é descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de SAP HANA de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="add-sap-hana-from-the-gallery"></a>Adicionar SAP HANA a partir da Galeria
Para configurar a integração de SAP HANA com o Azure AD, adicione SAP HANA na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar SAP HANA a partir da galeria, siga os passos seguintes:**

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Aceda a **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, selecione o **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **SAP HANA**. Em seguida, selecione **SAP HANA** partir do painel de resultados. Por fim, selecione o **adicionar** botão para adicionar a aplicação. 

    ![A nova aplicação](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com SAP HANA com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber que o utilizador homólogo no SAP HANA for para um utilizador no Azure AD. Por outras palavras, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado no SAP HANA.

No SAP HANA, atribua o **Username** valor o mesmo valor da **nome de utilizador** no Azure AD. Este passo estabelece a ligação entre dois utilizadores.

Para configurar e testar o Azure AD-início de sessão único com SAP HANA, conclua os blocos modulares seguintes:

1. [Configurar o Azure AD-início de sessão único](#configuring-azure-ad-single-sign-on) para permitir aos utilizadores utilizar esta funcionalidade.
2. [Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user) para testar do Azure AD-início de sessão único com Britta Simon.
3. [Criar um utilizador de teste de SAP HANA](#creating-a-sap-hana-test-user) para ter um homólogo de Britta Simon no SAP HANA que está ligada a representação do Azure AD do utilizador.
4. [Atribua o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user) para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. [Teste o início de sessão único](#testing-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de SAP HANA.

**Para configurar o Azure AD-início de sessão único com SAP HANA, siga os passos seguintes:**

1. No portal do Azure, no **SAP HANA** página de integração de aplicações, selecione **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo em **baseados em SAML início de sessão**, selecione **modo**.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. No **SAP HANA domínio e os URLs** secção, siga os passos seguintes:

    ![Domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. No **identificador** caixa, digite o seguinte:`HA100` 

    b. No **URL de resposta** caixa, escreva um URL com o padrão do seguinte:`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Estes valores não são reais. Atualizar estes valores com o identificador real e o URL de resposta. Contacte o [equipa de suporte de cliente de SAP HANA](https://cloudplatform.sap.com/contact.html) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, selecione **XML de metadados**. Em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Se o certificado não está ativo, em seguida, torne-a ativa, selecionando o **tornar o novo certificado ativa** caixa de verificação no Azure AD. 

5. A aplicação de SAP HANA espera as asserções de SAML num formato específico. A seguinte captura de ecrã mostra um exemplo deste formato. 

    Aqui iremos tiver mapeado a **identificador de utilizador** com o **ExtractMailPrefix()** função do **user.mail**. Isto permite que o valor de prefixo de e-mail do utilizador, o que é o ID de utilizador exclusivo. Este ID de utilizador é enviada para a aplicação de SAP HANA em cada resposta com êxito.

    ![Configurar o início de sessão único](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. No **atributos de utilizador** secção o **de sessão único-** diálogo caixa, siga os passos seguintes:

    a. No **identificador de utilizador** na lista pendente, selecione **ExtractMailPrefix**.
    
    b. No **correio** na lista pendente, selecione **user.mail**.

7. Selecione o botão **Guardar**.

    ![Configurar o início de sessão de gravação botão](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Para configurar o início de sessão único no lado de SAP HANA, inicie sessão na sua **HANA XSA Web consola** acedendo para o respetivo ponto final de HTTPS.

    > [!NOTE]
    > Na configuração predefinida, o URL redireciona o pedido para um início de sessão no ecrã, que requer que as credenciais de um utilizador autenticado de base de dados SAP HANA. O utilizador que inicia sessão tem de ter permissões para efetuar tarefas de administração de SAML.

9. Na Interface de Web XSA, aceda a **fornecedor de identidade**. A partir daí, selecione o  **+**  botão na parte inferior do ecrã, para apresentar o **adicionar informações do fornecedor de identidade** painel. Em seguida, siga os passos seguintes:

    ![Adicione o fornecedor de identidade](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. No **adicionar informações do fornecedor de identidade** painel, cole o conteúdo do XML de metadados (que transferiu a partir do portal do Azure) a **metadados** caixa.

    ![Adicionar as definições do fornecedor de identidade](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Se o conteúdo do documento XML válido, o processo de análise extrai as informações que são necessárias para o **requerente, o ID de entidade e o emissor** campos no **dados gerais** ecrã área. Extrai também as informações necessárias para os campos de URL no **destino** ecrã área, por exemplo, o  **Base URL e o URL de SingleSignOn (*)** os campos.

    ![Adicionar as definições do fornecedor de identidade](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. No **nome** caixa do **dados gerais** ecrã área, introduza um nome para o novo fornecedor de identidade de SAML SSO.

    > [!NOTE]
    > O nome do SAML IDP é obrigatório e tem de ser exclusivo. É apresentado na lista de IDPs SAML disponível que é apresentado quando seleciona SAML como método de autenticação para aplicações de SAP HANA XS a utilizar. Por exemplo, pode fazê- **autenticação** área da ferramenta de administração de artefacto XS de ecrã.

10. Selecione **guardar** para guardar os detalhes do fornecedor de identidade SAML e adicionar IDP SAML novo à lista de conhecidos IDPs de SAML.

    ![botão Guardar](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. No Studio HANA, dentro as propriedades do sistema do **configuração** separador, filtrar as definições por **saml**. Em seguida, ajustar o **assertion_timeout** de **10 seg** para **seg 120**.

    ![definição de assertion_timeout](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com) enquanto estiver a configurar a aplicação! Depois de adicionar esta aplicação a partir de **do Active Directory** > **aplicações empresariais** secção, selecione o **Single Sign-On** separador e aceder a incorporados documentação através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporado no [do Azure AD incorporado documentação](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, siga os passos seguintes:**

1. No **portal do Azure**, no painel esquerdo, selecione o **do Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**. Em seguida, selecione **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, selecione **adicionar** na parte superior da caixa de diálogo.
 
    ![O botão de adição](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo caixa, siga os passos seguintes:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe**e, em seguida, anote a palavra-passe.

    d. Selecione **Criar**.
 
### <a name="create-a-sap-hana-test-user"></a>Criar um utilizador de teste de SAP HANA

Para permitir que os utilizadores do Azure AD para SAP HANA iniciar sessão, terá de aprovisioná-los no SAP HANA.
SAP HANA suporta o aprovisionamento de just-in-time, que é por ativada por predefinição.

Se precisar de criar manualmente um utilizador, siga os passos seguintes:

>[!NOTE]
>Pode alterar a autenticação externa que o utilizador utiliza. Se podem autenticar com um sistema externo, como o Kerberos. Para obter informações detalhadas sobre as identidades externas, contacte o seu [administrador de domínio](https://cloudplatform.sap.com/contact.html).

1. Abra o [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador e, em seguida, ativar o utilizador de base de dados para SAML SSO.

    ![Criar utilizador](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Selecione a caixa de verificação invisível para a esquerda da **SAML**e, em seguida, selecione o **configurar** ligação.

3. Selecione **adicionar** adicionar SAML IDP.  Selecione IDP SAML adequadas e, em seguida, selecione **OK**.

4. Adicionar o **identidade externas** (neste caso, BrittaSimon) ou escolha **qualquer**. Em seguida, selecione **OK**.

    >[!Note]
    >Se o **qualquer** não estiver selecionada a caixa de verificação, em seguida, o nome de utilizador no HANA tem de corresponder exatamente o nome do utilizador no UPN antes do sufixo de domínio. (Por exemplo, BrittaSimon@contoso.com fica BrittaSimon no HANA.)

5. Para fins de teste, atribuir todos os **XS** funções ao utilizador.

    ![atribuir funções](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Deve dar permissões que são adequadas para os casos de utilização apenas.

6. Guarde o utilizador.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para SAP HANA.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon para SAP HANA, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações. Ir para a vista de diretório e aceda a **aplicações empresariais**. Selecione **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **SAP HANA**.

    ![Atribua o utilizador](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Selecione o **adicionar** botão. No **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** no **utilizadores** lista.

6. Clique em de **selecione** clique no botão no **utilizadores e grupos** caixa de diálogo.

7. Selecione o **atribuir** clique no botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, teste configuração do Azure AD único início de sessão utilizando o painel de acesso.

Quando seleciona o mosaico de SAP HANA no painel de acesso, esta deve obter automaticamente iniciada a aplicação de SAP HANA.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
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

