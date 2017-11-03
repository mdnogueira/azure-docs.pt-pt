---
title: "Tutorial: Integração do Azure Active Directory SAP nuvem de cliente | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e na nuvem do SAP para o cliente."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: e4d945525a45704f34e1d9e742220928a516f341
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Tutorial: Integração do Azure Active Directory SAP nuvem de cliente

Neste tutorial, irá aprender a integrar nuvem SAP para o cliente com o Azure Active Directory (Azure AD).

Integrar o SAP nuvem para o cliente com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à nuvem de SAP para o cliente
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a nuvem de cliente (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a nuvem de cliente, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma nuvem de SAP para cliente-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar nuvem SAP para o cliente a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Adicionar nuvem SAP para o cliente a partir da Galeria
Para configurar a integração da nuvem de SAP para o cliente com o Azure AD, tem de adicionar nuvem SAP para o cliente na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar nuvem SAP para o cliente na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **nuvem SAP para o cliente**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. No painel de resultados, selecione **nuvem SAP para o cliente**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com a nuvem para o cliente com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na nuvem de SAP para o cliente é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na nuvem de SAP para o cliente tem de ser estabelecida.

Na nuvem de SAP para o cliente, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com a nuvem de cliente, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar uma nuvem de SAP para o utilizador de teste de cliente](#creating-a-sap-cloud-for-customer-test-user)**  - para ter um homólogo de Britta Simon na nuvem do SAP para o cliente está ligado a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua nuvem SAP para aplicação de cliente.

**Para configurar do Azure AD-início de sessão único com a nuvem de cliente, execute os seguintes passos:**

1. No portal do Azure, no **nuvem SAP para o cliente** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. No **nuvem SAP para o domínio de cliente e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<server name>.crm.ondemand.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [SAP nuvem para a equipa de suporte ao cliente cliente](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) para obter estes valores. 

4. No **atributos de utilizador** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. No **identificador de utilizador** lista, selecione o **ExtractMailPrefix()** função.

    b. Do **correio** lista, selecione o atributo de utilizador que pretende utilizar para a implementação.
    Por exemplo, se pretender utilizar o campo IDdeEmpregado como identificador exclusivo do utilizador e ter armazenou o valor do atributo no ExtensionAttribute2, em seguida, selecione user.extensionattribute2.  

5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_400.png)

7. No **SAP nuvem para a configuração de cliente** secção, clique em **configurar nuvem SAP para o cliente** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. Para obter SSO configurado, execute os seguintes passos:
   
    a. Início de sessão na nuvem do SAP para o portal de cliente com direitos de administrador.
   
    b. Navegue para o **aplicação e tarefas comuns de gestão de utilizador** e clique em de **fornecedor de identidade** separador.
   
    c. Clique em **novo fornecedor de identidade** e selecione o ficheiro XML de metadados transferiu do portal do Azure. Ao importar os metadados, o sistema carrega automaticamente o certificado de assinatura necessária e o certificado de encriptação.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure Active Directory requer que o elemento asserção URL do serviço de consumidor no pedido SAML, por isso, selecione o **incluem asserção consumidor URL do serviço** caixa de verificação.
   
    e. Clique em **ativar o início de sessão único**.
   
    f. Guarde as alterações.
   
    g. Clique em de **meu sistema** separador.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. No **início de sessão do Azure AD no URL** caixa de texto, colar **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    posso. Especifique se o empregado manualmente pode escolher entre o início de sessão com o ID de utilizador e palavra-passe ou SSO selecionando o **seleção de fornecedor de identidade Manual**.
   
    j. No **SSO URL** secção, especifique o URL que deve ser utilizado pelos seus empregados, para iniciar sessão sistema. 
    No **URL enviados para o empregado** lista, pode escolher entre as seguintes opções:
   
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

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Criar uma nuvem de SAP para o utilizador de teste de cliente

Nesta secção, vai criar um utilizador chamado Britta Simon na nuvem do SAP para o cliente. Consulte [SAP nuvem para a equipa de suporte ao cliente](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) para adicionar os utilizadores na nuvem SAP para a plataforma de cliente. 

> [!NOTE]
> Certifique-se que o valor de NameID deve corresponder com o campo de nome de utilizador na nuvem SAP para a plataforma de cliente.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à nuvem de SAP para o cliente.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon nuvem SAP para o cliente, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **nuvem SAP para o cliente**.

    ![Configurar o início de sessão único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Ao clicar em nuvem SAP para o mosaico de cliente no painel de acesso, deve obter automaticamente com sessão iniciada para a nuvem de SAP para aplicação de cliente.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_203.png

