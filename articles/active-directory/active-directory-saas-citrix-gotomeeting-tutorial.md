---
title: "Tutorial: Integração do Azure Active Directory com GoToMeeting | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e GoToMeeting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: e8871ea9c3aa9f962bf961a8b44b748392a56ba7
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>Tutorial: Integração do Azure Active Directory com GoToMeeting

Neste tutorial, irá aprender a integrar GoToMeeting com o Azure Active Directory (Azure AD).

Integrar GoToMeeting com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao GoToMeeting.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para GoToMeeting (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com GoToMeeting, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um GoToMeeting-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar GoToMeeting a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-gotomeeting-from-the-gallery"></a>Adicionar GoToMeeting a partir da Galeria
Para configurar a integração de GoToMeeting com o Azure AD, terá de adicionar GoToMeeting a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar GoToMeeting a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **GoToMeeting**, selecione **GoToMeeting** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![GoToMeeting na lista de resultados](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com GoToMeeting com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no GoToMeeting é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no GoToMeeting tem de ser estabelecida.

No GoToMeeting, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com GoToMeeting, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste GoToMeeting](#create-a-gotomeeting-test-user)**  - para ter um homólogo de Britta Simon GoToMeeting que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação GoToMeeting.

**Para configurar o Azure AD-início de sessão único com GoToMeeting, execute os seguintes passos:**

1. No portal do Azure, no **GoToMeeting** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_samlbase.png)

3. No **GoToMeeting domínio e os URLs** secção, clique em **Mostrar avançadas definições de URL** e efetuar os seguintes ações -

    ![Domínio GoToMeeting e os URLs únicos de informações de início de sessão](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_ssourl.png)

    a. No **identificador** caixa de texto, escreva o URL:`https://authentication.logmeininc.com/saml/sp`

    b. No **URL de resposta** caixa de texto, escreva o URL:`https://authentication.logmeininc.com/saml/acs`

    c. No **reencaminhamento estado** caixa de texto, tipo de um dos seguintes URLs:

    **Para GoToMeeting**:`https://global.gotomeeting.com`
    
    **Para GoToTraining**:`https://global.gototraining.com`

    **Para GoToWebinar**:`https://global.gotowebinar.com`

    **Para GoToAssist**:`https://app.gotoassist.com`


4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_400.png)

6. Para gerar o **metadados** url, execute os seguintes passos:

    a. Clique em **registos de aplicação**.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appregistrations.png)
   
    b. Clique em **pontos finais** para abrir **pontos finais** caixa de diálogo.  
    
    ![Configurar o início de sessão único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpointicon.png)

    c. Clique no botão Copiar para copiar **documento de METADADOS de Federação** url e cole-o bloco de notas.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpoint.png)
     
    d. Agora, a página de propriedades do **GoToMeeting** e copie o **Id da aplicação** utilizando **cópia** botão e cole-o bloco de notas.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appid.png)

    e. Gerar o **URL de metadados** utilizando o padrão do seguinte:`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`   

7. No **GoToMeeting configuração** secção, clique em **configurar GoToMeeting** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_configure.png) 

8. Numa janela do browser diferente, inicie sessão no seu [GoToMeeting organização Center](https://account.citrixonline.com/organization/administration/)

9. Em **fornecedor de identidade** separador, pode configurar as definições do Azure está a fornecer gerada **URL de metadados** ou o transferido **ficheiro de metadados** ou **Manual**.

10. Para **URL de metadados** execute os seguintes passos:

    ![Configuração de GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/config1.png)

    a. No **como gostaria de configurar o IDP SAML?**, selecione **automática** na lista pendente.

    b. Colar o **URL de metadados**, que tiver gerado nos passos anteriores para o **URL de metadados** caixa de texto.

    c. Clique em **Guardar**.

11. Para **ficheiro de metadados** execute os seguintes passos:

    ![Configuração de GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/config2.png)

    a. No **como gostaria de configurar o IDP SAML?**, selecione **ficheiro de metadados de SAML carregar** na lista pendente.

    b. Para carregar o ficheiro de metadados transferido, clique em **ficheiro de metadados de carregamento**.

    c. Clique em **Guardar**.

12. Para **Manual** execute os seguintes passos:

    ![Configuração de GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/config3.png)

    a.  No **URL de página de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.

    b.  No **URL da página de início de sessão** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure.

    c.  No **ID de entidade do fornecedor de identidade** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.

    d. Extraia o certificado X509 do ficheiro de metadados transferido e carregar este certificado ao clicar no **carregar certificado**.

    e. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-gotomeeting-test-user"></a>Criar um utilizador de teste GoToMeeting

Nesta secção, é criado um utilizador chamado Britta Simon GoToMeeting. GoToMeeting suporta o aprovisionamento de just-in-time, que está ativada por predefinição.

Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe na GoToMeeting, uma nova é criada quando tentar aceder GoToMeeting.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de GoToMeeting](https://support.logmeininc.com/gotomeeting).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para GoToMeeting.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a GoToMeeting, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **GoToMeeting**.

    ![A ligação de GoToMeeting na lista de aplicações](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico GoToMeeting no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de GoToMeeting.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o aprovisionamento de utilizadores](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-citrixgotomeeting-provisioning-tutorial)


<!--Image references-->

[1]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_203.png

