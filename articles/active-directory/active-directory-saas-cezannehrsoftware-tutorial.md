---
title: "Tutorial: Integração do Azure Active Directory com o software de RH Cezanne | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o software de RH Cezanne."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>Tutorial: Integrar o Azure Active Directory com o software de RH Cezanne

Neste tutorial, irá aprender a integrar Cezanne HR software com o Azure Active Directory (Azure AD).

Integrar o software de RH Cezanne com o Azure AD fornece as seguintes vantagens. Pode:

- Controlar no Azure AD que tenha acesso ao software Cezanne HR.
- Permita aos utilizadores iniciar sessão automaticamente no software de RH Cezanne com início de sessão único (SSO) com as respetivas contas do Azure AD.
- Gerir as contas numa localização central: portal do Azure.

Para saber mais sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [que é o acesso a aplicações e SSO no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Cezanne HR software, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um software de RH Cezanne subscrição SSO ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que utilize um ambiente de produção.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testar o SSO do Azure AD num ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos modulares principais:

* Adicionar software Cezanne HR na galeria do
* Configurar e testar o SSO do Azure AD

## <a name="add-cezanne-hr-software-from-the-gallery"></a>Adicionar Cezanne HR software a partir da galeria do
Para configurar a integração de RH Cezanne software com o Azure AD, adicione software Cezanne HR na Galeria à sua lista de aplicações SaaS geridas.

Para adicionar software Cezanne HR na galeria do, efetue o seguinte:

1. No  **[portal do Azure](https://portal.azure.com)**, no painel esquerdo, selecione o **do Azure Active Directory** botão. 

    ![O botão "Do Azure Active Directory"][1]

2. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![A ligação de "Todas as aplicações"][2]
    
3. Para adicionar uma nova aplicação, na parte superior do **todas as aplicações** caixa de diálogo, selecione **nova aplicação**.

    ![A aplicação"novo" botão][3]

4. Na caixa de pesquisa, escreva **Cezanne HR Software**.

    ![Caixa de pesquisa](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. Na lista de resultados, selecione **Cezanne HR Software** e, em seguida, selecione o **adicionar** botão para adicionar a aplicação.

    ![A lista de resultados](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configurar e testar o SSO do Azure AD com o software de RH Cezanne com base num utilizador de teste chamado "Britta Simon."

Para SSO funcionar, do Azure AD tem de saber homólogo de software Cezanne HR ao utilizador do Azure AD. Por outras palavras, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no software de RH Cezanne.

Para estabelecer a relação de ligação, atribua o software de RH Cezanne **nome de utilizador** valor como o Azure AD **Username** valor.

Para configurar e testar o SSO do Azure AD utilizando o software de RH Cezanne, conclua os seguintes blocos de criação.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, pode ativar o SSO do Azure AD no portal do Azure e configurar o SSO na sua aplicação de software Cezanne HR efetuando o seguinte procedimento:

1. No portal do Azure, no **Cezanne HR Software** página de integração de aplicações, selecione **de sessão único-**.

    ![O comando "De sessão único"][4]

2. Para ativar a SSO, no **de sessão único-** caixa de diálogo, selecione o **modo** como **baseados em SAML início de sessão**.
 
    ![A caixa "Modo de"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Em **Cezanne HR Software domínio e os URLs**, efetue o seguinte procedimento:

    ![A secção "Cezanne HR Software domínio e os URLs"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. No **URL de início de sessão** caixa, escreva um URL com a seguinte sintaxe:`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. No **URL de resposta** caixa, escreva um URL com a seguinte sintaxe:`https://w3.cezanneondemand.com:443/<tenantid>`    
     
    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-as com o URL de resposta real e o URL de início de sessão. Para obter os valores, contacte o [equipa de suporte de cliente de software Cezanne HR](mailto:info@cezannehr.com).

4. Em **certificado de assinatura de SAML**, selecione **certificado (Base64)**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A secção "Certificado de assinatura de SAML"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Selecione **Guardar**.

    ![O botão "Guardar"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. Em **Cezanne HR Software configuração**, selecione **configurar Cezanne HR Software** para abrir o **configurar início de sessão** janela. Copiar o **ID de entidade de SAML** e **SAML único início de sessão no serviço** URL a partir do **referência rápida** secção.

    ![A secção "Configuração de Software de RH Cezanne"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. Numa janela do browser web diferente, inicie sessão seu inquilino do software de RH Cezanne como administrador.

8. No painel esquerdo, selecione **a configuração do sistema**. Selecione **definições de segurança** > **Single Sign-On configuração**.

    ![A ligação de "Início de sessão configuração única"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. No **permitem aos utilizadores iniciar sessão com os seguintes serviços único Sign-On (SSO)** painel, selecione o **SAML 2.0** caixa de verificação e selecione o **configuração avançada** opção.

    ![O início de sessão único dos serviços de opções](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Selecione **adicionar novos**.

    ![O botão "Adicionar novo"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. Em **fornecedores de identidade SAML 2.0**, efetue o seguinte procedimento:

    ![A secção "Fornecedores de identidade SAML 2.0"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. No **nome a apresentar** box, introduza o nome do seu fornecedor de identidade.

    b. No **identificador da entidade** caixa, cole o **ID de entidade de SAML** que copiou do portal do Azure. 

    c. No **SAML enlace** caixa de lista, selecione **POST**.

    d. No **ponto final de serviço de Token segurança** caixa, cole o **SAML único início de sessão no serviço** URL que copiou do portal do Azure. 
    
    e. No **nome de atributo de ID de utilizador** box, introduza `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Para carregar o certificado transferido do Azure AD, selecione o **carregar** botão.
    
    g. Selecione **OK**. 

12. Selecione **Guardar**.

    ![O botão "Guardar"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Como configurar a aplicação, pode ler uma versão das instruções anteriores no concisa o [portal do Azure](https://portal.azure.com). Depois de adicionar a aplicação a partir de **do Active Directory** > **aplicações empresariais** secção, selecione o **de sessão único-** separador. Em seguida, aceder a documentação do embedded o **configuração** secção. 

Para saber mais sobre a funcionalidade de documentação embedded, consulte [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
Nesta secção, vai criar o utilizador de teste Britta Simon no portal do Azure.

![O utilizador de teste Britta Simon][100]

Para criar um utilizador de teste no Azure AD, efetue o seguinte:

1. No **portal do Azure**, no painel esquerdo, selecione o **do Azure Active Directory** botão.

    ![O botão "Do Azure Active Directory"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, selecione **utilizadores e grupos** > **todos os utilizadores**.
    
    ![A hiperligação "Todos os utilizadores"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    O **todos os utilizadores** é aberta a caixa de diálogo.

3. Para abrir o **utilizador** caixa de diálogo, selecione **adicionar**.
 
    ![O botão "Adicionar"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo caixa, efetue o seguinte:
 
    ![A caixa de diálogo "Utilizador"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva de utilizador Britta Simon **endereço de correio eletrónico**.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que foi gerado no **palavra-passe** caixa.

    d. Selecione **Criar**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Criar um utilizador de teste do software de RH Cezanne

Para ativar o Azure AD aos utilizadores iniciar sessão software de RH Cezanne, têm de ser aprovisionados para Cezanne HR software. No caso de RH Cezanne software, o aprovisionamento é numa tarefa manual.

Aprovisionar uma conta de utilizador da seguinte forma:

1.  Inicie sessão no Cezanne HR software site da sua empresa como administrador.

2.  No painel esquerdo, selecione **a configuração do sistema** > **gerir utilizadores** > **adicionar novo utilizador**.

    ![A ligação de "Adicionar novo utilizador"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "novo utilizador")

3.  Em **pessoa detalhes**, efetue o seguinte procedimento:

    ![A secção "Pessoa Detalhes"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "novo utilizador")
    
    a. Definir **utilizador interno** como **OFF**.
    
    b. No **nome próprio** caixa, escreva o nome do próprio do utilizador, por exemplo, **Britta**.  
 
    c. No **Apelido** caixa, digite o apelido do utilizador, por exemplo, **Simon**.
    
    d. No **correio electrónico** caixa, escreva o endereço de e-mail do utilizador, por exemplo, Brittasimon@contoso.com.

4.  Em **informações da conta**, efetue o seguinte procedimento:

    ![A secção "Informações de conta"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "novo utilizador")
    
    a. No **Username** caixa, escreva o endereço de e-mail do utilizador, por exemplo, Brittasimon@contoso.com.
    
    b. No **palavra-passe** caixa, escreva a palavra-passe do utilizador.
    
    c. No **função de segurança** caixa, selecione **HR Professional**.
    
    d. Selecione **OK**.

5. No **de sessão único-** separador o **SAML 2.0 identificadores** secção, selecione **adicionar novo**.

    ![O botão "Adicionar novo"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "utilizador")

6. No **fornecedor de identidade** caixa de lista, selecione o seu fornecedor de identidade. No **identificador de utilizador** caixa, introduza o endereço de e-mail da conta de Simon de teste, utilizador Britta.

    ![As caixas de "Fornecedor de identidade" e "Utilizador identificador"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "utilizador")
    
7. Selecione **Guardar**.

    ![O botão "Guardar"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "utilizador")

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon para utilizar o SSO do Azure ao conceder acesso ao software Cezanne HR de utilizador de teste.

![Acesso de utilizador de teste][200] 

1. No portal do Azure, abra a vista de aplicações e, em seguida, aceda à vista de diretório. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![A ligação de "Todas as aplicações"][201] 

2. Na lista de aplicações, selecione **Cezanne HR Software**.

    ![A lista de "Aplicações"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Selecione **Adicionar**. Em seguida, no **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![Ligação de "Utilizadores e grupos"][203]

5. No **utilizadores e grupos** caixa de diálogo a **utilizadores** lista, selecione **Britta Simon**.

6. No **utilizadores e grupos** caixa de diálogo, selecione **selecione**.

7. No **adicionar atribuição** caixa de diálogo, selecione **atribuir**.
    
### <a name="test-sso"></a>Teste SSO

Nesta secção, testar a configuração de SSO do Azure AD utilizando o painel de acesso.

Quando seleciona o mosaico de software Cezanne HR no painel de acesso, a iniciar sessão automaticamente à sua aplicação de software Cezanne HR.

## <a name="next-steps"></a>Passos seguintes

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e SSO no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

