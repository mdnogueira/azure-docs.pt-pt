---
title: "Tutorial: Integração do Azure Active Directory com HR2day por Merces | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e HR2day por Merces."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: 77e2fcf9c306259286b15767f3a992510d6d79d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Tutorial: Integração do Azure Active Directory com HR2day por Merces

Neste tutorial, irá aprender a integrar HR2day por Merces com o Azure Active Directory (Azure AD).

Integrar HR2day por Merces com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao HR2day por Merces.
- Pode permitir que os utilizadores automaticamente obter a sessão iniciada no HR2day por Merces com as respetivas contas do Azure AD.
- Pode gerir as contas numa única localização central, o portal do Azure.

Para obter mais informações sobre a integração de aplicações SaaS com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com HR2day por Merces, terá dos seguintes itens:

- Uma subscrição do Azure AD.
- Um HR2day por Merces-início de sessão único ativar a subscrição.

> [!NOTE]
> Não recomendamos a utilização de um ambiente de produção para testar os passos neste tutorial.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, exceto se for necessário.
- Obter um [um mês a avaliação gratuita do Azure AD](https://azure.microsoft.com/pricing/free-trial/) se que ainda não o tiver.  

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário aqui descrito é composto por dois blocos modulares principais:

1. Adicionar HR2day por Merces partir da galeria.
2. Configurar e testar o Azure AD único início de sessão.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Adicionar HR2day por Merces a partir da Galeria
Para configurar a integração de HR2day por Merces com o Azure AD, adicione HR2day por Merces na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar HR2day por Merces a partir da galeria, siga os passos seguintes:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione o **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Aceda a **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **HR2day por Merces**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. No painel de resultados, selecione **HR2day por Merces**e, em seguida, selecione o **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configurar e testar o Azure AD-início de sessão único com HR2day por Merces com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber que o utilizador homólogo no HR2day por Merces for para um utilizador no Azure AD. Por outras palavras, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado no HR2day por Merces.

HR2day por Merces, atribuir os **nome de utilizador** no Azure AD para **Username** para estabelecer a relação.

Para configurar e testar o Azure AD-início de sessão único com HR2day por Merces, tem de concluir os blocos modulares seguintes:

1. [Configurar o Azure AD-início de sessão único](#configuring-azure-ad-single-sign-on): permitir que os utilizadores utilizar esta funcionalidade.
2. [Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user): teste do Azure AD-início de sessão único com Britta Simon.
3. [Criar um HR2day pelo utilizador de teste de Merces](#creating-an-hr2day-by-merces-test-user): criar um homólogo de Britta Simon no HR2day por Merces que está ligada a representação do Azure AD do utilizador.
4. [Atribua o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user): Ativar Britta Simon para utilizar o Azure AD-início de sessão único.
5. [Teste o início de sessão único](#testing-single-sign-on): Verifique se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, ativar o Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua HR2day Merces aplicação.

**Para configurar o Azure AD-início de sessão único com HR2day por Merces, siga os passos seguintes:**

1. No portal do Azure, no **HR2day por Merces** página de integração de aplicações, selecione **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. Para ativar o início de sessão, no **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão**.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. No **HR2day por domínio Merces e URLs** secção, siga os passos seguintes:

    ![Configurar o início de sessão único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. No **URL de início de sessão** caixa, escreva um URL, utilizando o padrão do seguinte: `https://<tenantname>.force.com/<instancename>`.

    b. No **identificador** caixa, escreva um URL, utilizando o padrão do seguinte: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte o [HR2day pela equipa de suporte de cliente Merces](mailto:servicedesk@merces.nl) para obter estes valores. 
 


4. No **certificado de assinatura de SAML** secção, selecione **Certificate(Base64)**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. Esta secção descreve como ativar utilizadores para se autenticar HR2day por Merces à respetiva conta no Azure AD. Tal utilizando federação com base no protocolo SAML.

    O HR2day por aplicação Merces espera as asserções de SAML num formato específico, que necessita para adicionar mapeamentos de atributos personalizados para o seu token SAML. A seguinte captura de ecrã mostra um exemplo desta situação. 

    ![Configurar o início de sessão único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Antes de poder configurar a asserção SAML, tem de contactar o [HR2day pela equipa de suporte de cliente Merces](mailto:servicedesk@merces.nl) e pedir o valor do atributo de identificador exclusivo para o seu inquilino. É necessário este valor para concluir os passos na secção seguinte. 

6. No **de sessão único-** caixa de diálogo a **atributos de utilizador** secção, configure o atributo de token SAML, conforme mostrado na imagem seguinte. Em seguida, siga os passos seguintes.
    
      | Nome do atributo    |   Valor do atributo |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | a associação ([correio] "102938475Z", "@" |
    
      a. Para abrir o **adicionar atributo** caixa de diálogo, selecione **adicionar atributo**.

    ![Configurar o início de sessão único](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_05.png)

    b. No **nome** caixa, escreva **ATTR_LOGINCLAIM**.

    c. Do **valor** lista, selecione **Join()**.

    d. Do **String1** lista, selecione **user.mail**.

    e. Para **String2**, escreva o identificador exclusivo que é fornecido pela sua equipa de HR2day.

    f. No **separador** caixa, escreva  **@** .
    
    g. Selecione **Ok**.

7. Selecione o botão **Guardar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-hr2day-tutorial/tutorial_general_400.png)

8. No **HR2day pela configuração Merces** secção, selecione **configurar HR2day por Merces** para abrir o **configurar início de sessão** janela. Copiar o **Sign-Out URL**, **ID de entidade de SAML**, e **único início de sessão no URL do serviço SAML** do **referência rápida** secção.

    ![Configurar o início de sessão único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. Para configurar o SSO para a sua aplicação, contacte o [HR2day pela equipa de suporte de cliente Merces](mailTo:servicedesk@merces.nl). Anexar o transferido **Certificate(Base64)** ficheiro ao seu e-mail. Também fornecem a **Sign-Out URL**, **ID de entidade de SAML**, e **único início de sessão no URL do serviço SAML** para que possa ser configurados para integração com o SSO.

    > [!NOTE]
    >Mencionar à equipa de Merces que esta integração tem o ID de entidade para ser definido com o padrão **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir de **do Active Directory** > **aplicações empresariais** secção, selecione o **Single Sign-On** separador. Em seguida, aceder a documentação incorporada através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporado no [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, siga os passos seguintes:**

1. No **portal do Azure**, no painel de navegação esquerdo, selecione o **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, selecione **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, selecione **adicionar** no topo da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo caixa, siga os passos seguintes:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe**e, em seguida, anote a palavra-passe.

    d. Selecione **Criar**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Criar um HR2day pelo utilizador de teste de Merces

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon no HR2day pelo Merces. Para adicionar os utilizadores na conta HR2day, trabalhar com o [HR2day pela equipa de suporte de cliente Merces](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Se precisar de criar manualmente um utilizador, contacte o [HR2day pela equipa de suporte de cliente Merces](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao HR2day por Merces.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a HR2day por Merces, siga os passos seguintes:**

1. No portal do Azure, abra a vista de aplicações, aceda à vista de diretório e, em seguida, aceda a **aplicações empresariais**. Em seguida, selecione **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **HR2day por Merces**.

    ![Configurar o início de sessão único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Selecione o **adicionar** botão. Em seguida, no **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo a **utilizadores** lista, selecione **Britta Simon**.

6. Clique em de **selecione** botão.

7. No **adicionar atribuição** caixa de diálogo, selecione **atribuir**.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão utilizando o painel de acesso.  

Quando seleciona o HR2day por Merces mosaico no painel de acesso, que automaticamente obter sessão na sua HR2day por aplicação Merces.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png

