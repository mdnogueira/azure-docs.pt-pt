---
title: "Tutorial: Integração do Azure Active Directory com o IBM Kenexa inquérito Enterprise | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Enterprise do IBM Kenexa inquérito."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 5c276c23288292a1c54dd9d57177d5072b90c9e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Tutorial: Integração do Azure Active Directory com o IBM Kenexa inquérito Enterprise

Neste tutorial, irá aprender a integrar IBM Kenexa inquérito Enterprise com o Azure Active Directory (Azure AD).

Integrar o IBM Kenexa inquérito Enterprise com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao IBM Kenexa inquérito Enterprise.
- Pode permitir aos utilizadores iniciar sessão automaticamente IBM Kenexa inquérito empresarial utilizando início de sessão único (SSO) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central: portal do Azure.

Se pretender saber mais sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com IBM Kenexa inquérito empresarial, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma subscrição IBM Kenexa inquérito Enterprise SSO ativado

> [!NOTE]
> Ao testar os passos neste tutorial, recomendamos que utilize um ambiente de produção.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testar o SSO do Azure AD num ambiente de teste. O cenário descrito no tutorial consiste em dois blocos modulares principais:

* Adicionar IBM Kenexa inquérito Enterprise da galeria do
* Configurar e testar o SSO do Azure AD

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Adicionar IBM Kenexa inquérito Enterprise a partir da Galeria
Para configurar a integração do IBM Kenexa inquérito Enterprise com o Azure AD, adicione IBM Kenexa inquérito Enterprise da Galeria à sua lista de aplicações SaaS geridas.

Para adicionar IBM Kenexa inquérito Enterprise da galeria do, efetue o seguinte:

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, clique em de **do Azure Active Directory** botão. 

    ![O botão do Azure Active Directory][1]

2. Selecione **aplicações empresariais**e, em seguida, selecione **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar uma aplicação, clique o **nova aplicação** botão.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **IBM Kenexa inquérito Enterprise**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. Na lista de resultados, selecione **IBM Kenexa inquérito Enterprise**e, em seguida, clique em de **adicionar** botão para adicionar a aplicação.

    ![IBM Kenexa inquérito Enterprise na lista de resultados](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configurar e testar o SSO do Azure AD com o IBM Kenexa inquérito Enterprise com base num utilizador de teste chamado "Britta Simon."

Para SSO funcionar, do Azure AD tem de identificar o homólogo de utilizador do IBM Kenexa inquérito empresarial no Azure AD. Por outras palavras, do Azure AD tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e um utilizador relacionado da empresa do IBM Kenexa inquérito.

Para estabelecer a relação de ligação, atribua o valor do **nome de utilizador** IBM Kenexa inquérito empresa como o valor de **Username** no Azure AD.

Para configurar e testar o SSO do Azure AD com o IBM Kenexa inquérito Enterprise, conclua os blocos modulares nas dois secções.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, ativar o SSO do Azure AD no portal do Azure e configurar o SSO na sua aplicação IBM Kenexa inquérito empresarial da seguinte forma:

1. No portal do Azure, no **IBM Kenexa inquérito Enterprise** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o IBM Kenexa inquérito Enterprise único início de sessão na ligação][4]

2. No **de sessão único-** caixa de diálogo a **modo** caixa, selecione **baseados em SAML início de sessão** para permitir SSO.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. No **IBM Kenexa inquérito Enterprise domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio de empresa do IBM Kenexa inquérito e os URLs únicos de informações de início de sessão](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. No **identificador** caixa de texto, escreva um URL com o padrão do seguinte:`https://surveys.kenexa.com/<companycode>`

    b. No **URL de resposta** caixa de texto, escreva um URL com o padrão do seguinte:`https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-as com o identificador real e o URL de resposta. Para obter os valores reais, contacte o [equipa de suporte do IBM Kenexa inquérito Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

4. Em **certificado de assinatura de SAML**, clique em **certificado (Base64)**e, em seguida, guarde o ficheiro de certificado para o seu computador.

    ![A hiperligação de transferência do certificado (Base64)](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    A aplicação do IBM Kenexa inquérito Enterprise espera receber as asserções de segurança asserções Markup Language (SAML) num formato específico, que necessita para adicionar mapeamentos de atributos personalizados para a configuração dos seus atributos de token SAML. O valor da afirmação de identificador de utilizador na resposta tem de corresponder ao ID de SSO que está configurado no sistema Kenexa. Para mapear o identificador de utilizador adequada na sua organização como protocolo de datagrama de Internet do SSO (IDP), trabalhar com o [equipa de suporte do IBM Kenexa inquérito Enterprise](https://www.ibm.com/support/home/?lnk=fcw). 

    Por predefinição, o Azure AD define o identificador de utilizador como o valor de nome principal (UPN) do utilizador. Pode alterar este valor no **atributo** separador, conforme mostrado na captura de ecrã seguinte. A integração funciona apenas depois de concluir o mapeamento corretamente.
    
    ![A caixa de diálogo de atributos de utilizador](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. Clique em **Guardar**.

    ![A configurar-início de sessão único guardar botão](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. Para abrir o **configurar início de sessão** janela em **IBM Kenexa inquérito Enterprise configuração**, clique em **configurar IBM Kenexa inquérito Enterprise**. 
 
    ![A ligação de configurar IBM Kenexa inquérito Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. Copiar o **Sign-Out URL**, **ID de entidade de SAML**, e **único início de sessão no URL do serviço SAML** valores do **referência rápida** secção.

8. No **configurar início de sessão** janela, em **referência rápida**, copiar o **Sign-Out URL**, **ID de entidade de SAML**, e **SAML único URL do serviço de início de sessão** valores.

9. Para configurar o SSO no **IBM Kenexa inquérito Enterprise** lado, enviar o transferido **certificado (Base64)**, **Sign-Out URL**, **ID de entidade de SAML**, e **único início de sessão no URL do serviço SAML** valores para o [equipa de suporte do IBM Kenexa inquérito Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Pode fazer referência a uma versão concisa destas instruções o [portal do Azure](https://portal.azure.com) enquanto estiver a configurar a aplicação. Depois de adicionar a aplicação a partir de **do Active Directory** > **aplicações empresariais** secção, basta clicar no **de sessão único-** separador e, em seguida, aceder a incorporado documentação através de **configuração** secção no final. Para saber mais sobre a funcionalidade de documentação embedded, consulte [do Azure AD incorporado documentação](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
Nesta secção, criar o utilizador de teste Britta Simon no portal do Azure da seguinte forma:

![Criar um utilizador de teste do Azure AD][100]

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.
 
    ![O botão de adição](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo caixa, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Criar um utilizador de teste do IBM Kenexa inquérito Enterprise

Nesta secção, vai criar um utilizador chamado Britta Simon IBM Kenexa inquérito empresa. 

Para criar utilizadores no sistema IBM Kenexa inquérito Enterprise e mapear o ID de SSO para os mesmos, pode trabalhar com o [equipa de suporte do IBM Kenexa inquérito Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Este valor de ID de SSO também deve ser mapeado para o valor do identificador de utilizador do Azure AD. Pode alterar esta predefinição no **atributo** separador.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar utilizador Britta Simon para utilizar o SSO do Azure ao conceder acesso para a empresa do IBM Kenexa inquérito.

![Atribuir a função de utilizador][200] 

Para atribuir o utilizador Britta Simon para IBM Kenexa inquérito Enterprise, efetue o seguinte:

1. No portal do Azure, abra o **aplicações** vista, aceda ao **diretório** visualizar, selecione **aplicações empresariais**e, em seguida, clique em **todas as aplicações** .

    ![O "As aplicações empresariais" e ligações de "Todas as aplicações"][201] 

2. No **aplicações** lista, selecione **IBM Kenexa inquérito Enterprise**.

    ![A ligação do IBM Kenexa inquérito Enterprise na lista de aplicações](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. No painel esquerdo, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Clique em de **adicionar** botão e, em seguida, no **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo a **utilizadores** lista, selecione **Britta Simon**.

6. No **utilizadores e grupos** caixa de diálogo, clique em de **selecione** botão.

7. No **adicionar atribuição** caixa de diálogo, clique em de **atribuir** botão.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração de SSO do Azure AD utilizando o painel de acesso.

Ao clicar no **IBM Kenexa inquérito Enterprise** na peça de mosaico do painel de acesso, esta deve ser iniciada automaticamente aplicação IBM Kenexa inquérito Enterprise.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 
