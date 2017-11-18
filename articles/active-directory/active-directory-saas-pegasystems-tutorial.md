---
title: "Tutorial: Integração do Azure Active Directory com sistemas de Pega | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Pega sistemas."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: ffc3d94416378f6469eaa574dc10880ef840e91f
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Azure Active Directory com sistemas de Pega

Neste tutorial, irá aprender a integrar Pega sistemas com o Azure Active Directory (Azure AD).

Integrar Pega sistemas com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Pega sistemas.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para sistemas de Pega (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com sistemas de Pega, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Pega sistemas início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de sistemas de Pega na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-pega-systems-from-the-gallery"></a>A adição de sistemas de Pega na galeria do
Para configurar a integração de Pega sistemas com o Azure AD, tem de adicionar sistemas Pega na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar sistemas Pega na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Pega sistemas**, selecione **Pega sistemas** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Sistemas pega na lista de resultados](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com os sistemas de Pega com base num utilizador teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo nos sistemas de Pega é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em sistemas de Pega tem de ser estabelecida.

Nos sistemas de Pega, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com os sistemas de Pega, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de sistemas de Pega](#create-a-pega-systems-test-user)**  - para ter um homólogo de Britta Simon em sistemas de Pega que está ligado a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Pega sistemas.

**Para configurar o Azure AD-início de sessão único com os sistemas de Pega, execute os seguintes passos:**

1. No portal do Azure, no **Pega sistemas** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. No **Pega sistemas de domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Pega sistemas de domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Pega sistemas de domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url1.png)

    No **reencaminhamento estado** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de estado de reencaminhamento. Pode encontrar os valores do identificador e o URL de resposta da aplicação Pega que é explicado posteriormente neste tutorial. Para o estado de reencaminhamento, entre em contacto com [equipa de suporte de cliente de sistemas de Pega](https://www.pega.com/contact-us) para obter o valor. 

5. A aplicação de sistemas de Pega espera as asserções de SAML num formato específico, que necessita para adicionar mapeamentos de atributos personalizado à sua configuração de atributos token SAML. Estas afirmações sejam específico do cliente e depende o requisito. Exemplo as seguintes afirmações opcionais são apenas que pode configurar para a sua aplicação. Pode gerir os valores destes atributos a partir de "**atributos de utilizador**" secção na página de integração de aplicações. 

    ![Configurar o início de sessão único](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute.png)

6. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributos token SAML, conforme mostrado na imagem anterior e execute os seguintes passos:
    
    | Nome do Atributo | Valor do Atributo |
    | ------------------- | -------------------- |    
    | UID | *********** |
    | CN  | *********** |
    | capacidade de correio | *********** |
    | accessgroup | *********** |
    | Organização | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Grupo de trabalho | *********** |
    | Telefone | *********** |

    > [!NOTE]
    > Estes são valores específicos do cliente. Forneça os valores adequados.

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_05.png)

    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **OK**.

7. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-pegasystems-tutorial/tutorial_general_400.png)
    
9. Para configurar o início de sessão único em **Pega sistemas** lado, abra o **Pega Portal** com a conta de administrador na outra janela do browser.

10. Selecione **criar** -> **SysAdmin** -> **serviço de autenticação**.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. Execute os seguintes ações em **criar serviço Aauthentication** ecrã:

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Selecione **SAML 2.0** do tipo

    b. No **nome** caixa de texto, introduza qualquer por nome exemplo SSO do Azure AD

    c. No **descrição breve** caixa de texto, introduza qualquer descrição  

    d. Clique em **criar e abrir** 
    
12. No **informações do fornecedor de identidade (IdP)** secção, clique em **IdP importar metadados** e procure o ficheiro de metadados que transferiu do portal do Azure. Clique em **submeter** ao carregar os metadados.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. Preencherá os dados IdP, conforme mostrado abaixo.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. Execute os seguintes ações em **definições do fornecedor de serviço (SP)** secção:

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Copiar o **entidade identificação** valor e cole do Portal do Azure **identificador** caixa de texto.

    b.  Copiar o **localização do serviço de consumidor da asserção (ACS)** valor e cole do Portal do Azure **URL de resposta** caixa de texto.

    c. Selecione **desativar o pedido de assinatura**.

15. Clique em **guardar**
    
> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-pega-systems-test-user"></a>Criar um utilizador de teste Pega sistemas

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon Pega sistemas. Consulte [equipa de suporte de cliente de sistemas de Pega](https://www.pega.com/contact-us) para criar utilizadores em Pega Sysyems.


### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso aos sistemas de Pega.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon Pega sistemas, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Pega sistemas**.

    ![A ligação de sistemas de Pega na lista de aplicações](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de sistemas de Pega no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de sistemas de Pega.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_203.png

