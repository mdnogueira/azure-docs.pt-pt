---
title: 'Tutorial: Integrar o Azure Active Directory vxMaintain | Microsoft Docs'
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e vxMaintain."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: ad87534af448356b8cc80d8ddd278bfb8a9165e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-vxmaintain"></a>Tutorial: Integrar o Azure Active Directory vxMaintain

Neste tutorial, irá aprender a integrar vxMaintain com o Azure Active Directory (Azure AD).

Esta integração proporciona várias vantagens importantes. Pode:

- Controlar no Azure AD que tenha acesso ao vxMaintain.
- Permita aos utilizadores iniciar sessão automaticamente no vxMaintain com início de sessão único (SSO) através das respetivas contas do Azure AD.
- Gerir as contas numa localização central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com vxMaintain, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um vxMaintain subscrição SSO ativado

> [!NOTE]
> Ao testar os passos neste tutorial, recomendamos que utilize um ambiente de produção.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. 

O cenário em que este tutorial descreve consiste em dois blocos modulares principais:

* Adicionar vxMaintain a partir da Galeria
* Configurar e testar o Azure AD de sessão único-

## <a name="add-vxmaintain-from-the-gallery"></a>Adicionar vxMaintain a partir da Galeria
Para configurar a integração de vxMaintain com o Azure AD, terá de adicionar vxMaintain a partir da Galeria à sua lista de aplicações SaaS geridas.

Para adicionar vxMaintain a partir da galeria, efetue o seguinte:

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o **do Azure Active Directory** botão. 

    ![O botão do Azure Active Directory][1]

2. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![O painel de "Aplicações da empresa"][2]
    
3. Para adicionar uma aplicação, o **todas as aplicações** caixa de diálogo, selecione **nova aplicação**.

    ![A aplicação"novo" botão][3]

4. Na caixa de pesquisa, escreva **vxMaintain**.

    ![A "Único início de sessão no modo de" na lista pendente](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. Na lista de resultados, selecione **vxMaintain**e, em seguida, selecione **adicionar**.

    ![A ligação de vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configurar e testar o SSO do Azure AD utilizando vxMaintain, com base num utilizador de teste chamado "Britta Simon."

Para SSO funcionar, do Azure AD tem de saber o homólogo vxMaintain ao utilizador do Azure AD. Ou seja, tem de estabelecer uma relação de ligação entre o utilizador do Azure AD e o utilizador vxMaintain correspondente.

Para estabelecer a relação de ligação, atribua o vxMaintain **nome de utilizador** valor como o Azure AD **Username** valor.

Para configurar e testar o SSO do Azure AD utilizando vxMaintain, conclua os seguintes blocos de criação.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, pode ativar o SSO do Azure AD no portal do Azure e configurar o SSO na sua aplicação vxMaintain efetuando o seguinte procedimento:

1. No portal do Azure, no **vxMaintain** página de integração de aplicações, selecione **de sessão único-**.

    ![O comando "De sessão único"][4]

2. Para ativar a SSO, no **modo de início de sessão único** na lista pendente, selecione **baseados em SAML início de sessão**.
 
    ![O comando "baseados em SAML início de sessão"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. Em **vxMaintain domínios e URLs**, efetue o seguinte procedimento:

    ![A secção de domínio e os URLs de vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. No **identificador** caixa, escreva um URL com a seguinte sintaxe:`https://<company name>.verisae.com`

    b. No **URL de resposta** caixa, escreva um URL com a seguinte sintaxe:`https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-as com o identificador real e o URL de resposta. Para obter os valores, contacte o [equipa de suporte de vxMaintain](http://www.verisae.com/contact-us).
 
4. Em **certificado de assinatura de SAML**, selecione **XML de metadados**e, em seguida, guarde o ficheiro de metadados para o seu computador.

    ![A secção "Certificado de assinatura de SAML"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Selecione **Guardar**.

    ![O botão Guardar](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. Para configurar **vxMaintain** SSO, enviar o transferido **XML de metadados** do ficheiro para o [equipa de suporte de vxMaintain](http://www.verisae.com/contact-us).

> [!TIP]
> Como configurar a aplicação, pode ler uma versão das instruções anteriores no concisa o [portal do Azure](https://portal.azure.com). Depois de adicionar a aplicação a partir de **do Active Directory** > **aplicações empresariais** secção, selecione o **Single Sign-On** separador e, em seguida, aceder a incorporados documentação do **configuração** secção. 
>
>Para saber mais sobre a funcionalidade de documentação embedded, consulte [Gerir início de sessão único para aplicações da empresa](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
Nesta secção, criar o utilizador de teste Britta Simon no portal do Azure da seguinte forma:

![O utilizador de teste do Azure AD][100]

1. No **portal do Azure**, no painel esquerdo, selecione o **do Azure Active Directory** botão.

    ![O botão "Do Azure Active Directory"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. Para apresentar uma lista de utilizadores, aceda a **utilizadores e grupos** > **todos os utilizadores**.
    
    ![A hiperligação "Todos os utilizadores"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    O **todos os utilizadores** é aberta a caixa de diálogo. 

3. Para abrir o **utilizador** caixa de diálogo, selecione **adicionar**.
 
    ![O botão de adição](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo caixa, efetue o seguinte:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador de teste Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que foi gerado no **palavra-passe** caixa.

    d. Selecione **Criar**.
 
### <a name="create-a-vxmaintain-test-user"></a>Criar um utilizador de teste vxMaintain

Nesta secção, vai criar o utilizador de teste Britta Simon no vxMaintain. Para adicionar utilizadores na plataforma vxMaintain, trabalhar com o [equipa de suporte de vxMaintain](http://www.verisae.com/contact-us). Antes de utilizar o SSO, crie e ative os utilizadores.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon para utilizar o SSO do Azure, concedendo acesso aos vxMaintain de utilizador de teste. Para tal, faça o seguinte:

![Utilizador de teste na lista nome a apresentar][200] 

1. No portal do Azure **aplicações** vista, aceda a **diretório** vista > **aplicações empresariais** > **todas as aplicações**.

    ![A ligação de "Todas as aplicações"][201] 

2. No **aplicações** lista, selecione **vxMaintain**.

    ![A ligação de vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. No painel esquerdo, selecione **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Selecione **adicionar** e, em seguida, no **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][203]

5. No **utilizadores e grupos** caixa de diálogo a **utilizadores** lista, selecione **Britta Simon**e, em seguida, selecione o **selecione** botão.

7. No **adicionar atribuição** caixa de diálogo, selecione **atribuir**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Testar o Azure AD-início de sessão único

Nesta secção, testar a configuração de SSO do Azure AD utilizando o painel de acesso.

Selecionar o **vxMaintain** mosaico no painel de acesso deve iniciar sessão na sua aplicação vxMaintain automaticamente.

Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Passos seguintes

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

