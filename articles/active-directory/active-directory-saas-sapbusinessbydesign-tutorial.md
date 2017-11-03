---
title: "Tutorial: Integração do Azure Active Directory com SAP Business ByDesign | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e do SAP Business ByDesign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: ab76a0ac1ef954efd3c66e6f565514b889ed9444
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integração do Azure Active Directory com SAP Business ByDesign

Neste tutorial, irá aprender a integrar SAP Business ByDesign com o Azure Active Directory (Azure AD).

Integração do SAP Business ByDesign com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ByDesign do SAP Business.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para SAP Business ByDesign (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ByDesign do SAP Business, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um SAP Business ByDesign-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição do SAP Business ByDesign na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>A adição do SAP Business ByDesign na galeria do
Para configurar a integração do SAP Business ByDesign com o Azure AD, tem de adicionar SAP Business ByDesign na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar SAP Business ByDesign na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **SAP Business ByDesign**, selecione **SAP Business ByDesign** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![SAP Business ByDesign na lista de resultados](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com SAP Business ByDesign com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no SAP Business ByDesign é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SAP Business ByDesign tem de ser estabelecida.

No ByDesign do SAP Business, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com ByDesign do SAP Business, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do SAP Business ByDesign](#create-an-sap-business-bydesign-test-user)**  - para ter um homólogo de Britta Simon SAP Business ByDesign que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação do SAP Business ByDesign.

**Para configurar o Azure AD-início de sessão único com ByDesign do SAP Business, execute os seguintes passos:**

1. No portal do Azure, no **SAP Business ByDesign** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. No **URLs e de domínio do SAP Business ByDesign** secção, execute os seguintes passos:

    ![Domínio do SAP Business ByDesign e URLs único informações de início de sessão](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<servername>.sapbydesign.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para obter estes valores.

4. No **atributos de utilizador** secção, execute os seguintes passos:

    ![Secção de atributo ByDesign do SAP Business](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. No **identificador de utilizador** lista, selecione o **ExtractMailPrefix()** função.
    
    b. Do **correio** lista, selecione o atributo de utilizador que pretende utilizar para a implementação. Por exemplo, se pretender utilizar o campo IDdeEmpregado como identificador exclusivo do utilizador e ter armazenou o valor do atributo no ExtensionAttribute2, em seguida, selecione user.extensionattribute2.     

5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. No **SAP Business ByDesign configuração** secção, clique em **configurar ByDesign do SAP Business** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração do SAP Business ByDesign](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Para obter SSO configurado para a sua aplicação, execute os seguintes passos:
   
    a. Inicie sessão portal do SAP Business ByDesign com direitos de administrador.
   
    b. Navegue para **aplicação e tarefas comuns de gestão de utilizador** e clique em de **fornecedor de identidade** separador.
   
    c. Clique em **novo fornecedor de identidade** e selecione o ficheiro XML de metadados do que transferiu do portal do Azure. Ao importar os metadados, o sistema carrega automaticamente o certificado de assinatura necessária e o certificado de encriptação.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Para incluir o **asserção URL do serviço de consumidor** para o pedido SAML, selecione **incluem asserção consumidor URL do serviço**.
   
    e. Clique em **ativar o início de sessão único**.
   
    f. Guarde as alterações.
   
    g. Clique em de **meu sistema** separador.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Colar **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure para o **início de sessão do Azure AD no URL** caixa de texto.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    posso. Especifique se o empregado manualmente pode escolher entre o início de sessão com o ID de utilizador e palavra-passe ou SSO selecionando **seleção de fornecedor de identidade Manual**.
   
    j. No **SSO URL** secção, especifique o URL que deve ser utilizado pelo empregado para início de sessão no sistema. 
    O URL enviados para a lista pendente de empregado, pode optar entre as seguintes opções:
   
    **URL não SSO**
   
    O sistema envia apenas o URL de sistema normal para o empregado. O empregado não é possível iniciar sessão com o SSO e tem de utilizar palavras-passe ou certificado em vez disso.
   
    **URL DE SSO** 
   
    O sistema envia apenas o URL do SSO para o empregado. O empregado pode iniciar sessão com o SSO. Pedido de autenticação é redirecionado através do IdP.
   
    **Seleção automática**
   
    Se SSO não estiver ativo, o sistema envia o URL de sistema normal para o empregado. Se o SSO está ativo, o sistema verifica se o empregado tem uma palavra-passe. Se estiver disponível uma palavra-passe, SSO URL e o URL do SSO não são enviadas para o empregado. No entanto, se o empregado não tem nenhuma palavra-passe, apenas o URL de SSO é enviado para o empregado.
   
    k. Guarde as alterações.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Criar um utilizador de teste de SAP ByDesign de negócio

Nesta secção, vai criar um utilizador chamado Britta Simon ByDesign do SAP Business. Consulte [equipa de suporte de cliente do SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para adicionar os utilizadores na plataforma do SAP Business ByDesign. 

> [!NOTE]
> Certifique-se que o valor de NameID deve corresponder com o campo de nome de utilizador na plataforma do SAP Business ByDesign.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para SAP Business ByDesign.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon para SAP Business ByDesign, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **SAP Business ByDesign**.

    ![A ligação do SAP Business ByDesign na lista de aplicações](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do SAP Business ByDesign no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação do SAP Business ByDesign.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png

