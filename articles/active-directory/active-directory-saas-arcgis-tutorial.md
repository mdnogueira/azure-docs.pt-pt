---
title: "Tutorial: Integração do Azure Active Directory com ArcGIS Online | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e ArcGIS Online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: df72270ca6443b456c079b22425f1660aa522389
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integração do Azure Active Directory com ArcGIS Online

Neste tutorial, irá aprender a integrar ArcGIS Online com o Azure Active Directory (Azure AD).

Integrar ArcGIS Online com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ArcGIS Online
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para ArcGIS Online (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with ArcGIS Online, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in ArcGIS Online.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ArcGIS Online, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um ArcGIS Online-início de sessão único subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar ArcGIS Online a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-arcgis-online-from-the-gallery"></a>Adicionar ArcGIS Online a partir da Galeria
Para configurar a integração do ArcGIS Online com o Azure AD, terá de adicionar ArcGIS Online a Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar ArcGIS Online a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **nova aplicação** botão na parte superior da caixa de diálogo Adicionar nova aplicação.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **ArcGIS Online**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_search.png)

5. No painel de resultados, selecione **ArcGIS Online**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com ArcGIS Online de acordo com um utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo ArcGIS online é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ArcGIS Online tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** ArcGIS online.

Para configurar e testar o Azure AD-início de sessão único com ArcGIS Online, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste ArcGIS Online](#creating-an-arcgis-online-test-user)**  - para ter um homólogo de Britta Simon ArcGIS Online que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação ArcGIS Online.

**Para configurar o Azure AD-início de sessão único com ArcGIS Online, execute os seguintes passos:**

1. No portal do Azure, no **ArcGIS Online** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. No **domínio Online ArcGIS e URLs** secção, executar o passo seguinte:

    ![Configurar o início de sessão único](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    No **URL de início de sessão** caixa de texto, digite o valor utilizando o padrão do seguinte:`https://<company>.maps.arcgis.com`

    > [!NOTE] 
    > Este valor não é o real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Online ArcGIS](http://support.esri.com/) para obter este valor. 

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

6. Numa janela do browser web diferente, inicie sessão no site da sua empresa ArcGIS como administrador.

7. Clique em **editar definições**.

    ![Editar definições](./media/active-directory-saas-arcgis-tutorial/ic784742.png "editar as definições")

8. Clique em **segurança**.

    ![Segurança](./media/active-directory-saas-arcgis-tutorial/ic784743.png "segurança")

9. Em **Enterprise inícios de sessão**, clique em **definir o fornecedor de identidade**.

    ![Inícios de sessão do Enterprise](./media/active-directory-saas-arcgis-tutorial/ic784744.png "Enterprise inícios de sessão")

10. No **definir o fornecedor de identidade** configuração página, execute os seguintes passos:
   
    ![Definir o fornecedor de identidade](./media/active-directory-saas-arcgis-tutorial/ic784745.png "definir o fornecedor de identidade")
   
    a. No **nome** caixa de texto, escreva o nome da sua organização.

    b. Para **metadados para o fornecedor de identidade empresarial irão ser fornecido com**, selecione **um ficheiro**.

    c. Para carregar o ficheiro de metadados transferido, clique em **Escolher ficheiro**.

    d. Clique em **fornecedor de identidade do conjunto**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de Britta Simon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-arcgis-online-test-user"></a>Criar um utilizador de teste ArcGIS Online

Para permitir que os utilizadores do Azure AD sessão ArcGIS Online, têm de ser aprovisionados para ArcGIS Online.  
No caso de ArcGIS Online, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **ArcGIS** inquilino.

2. Clique em **convite membros**.
   
    ![Convidar membros](./media/active-directory-saas-arcgis-tutorial/ic784747.png "convidar membros")

3. Selecione **adicionar membros automaticamente sem enviar mensagens de correio eletrónico**e, em seguida, clique em **seguinte**.
   
    ![Adicionar automaticamente membros](./media/active-directory-saas-arcgis-tutorial/ic784748.png "automaticamente a adicionar membros")

4. No **membros** diálogo página, execute os seguintes passos:
   
     ![Adicionar e reveja](./media/active-directory-saas-arcgis-tutorial/ic784749.png "adicionar e revisão")
    
     a. Introduza o **E-Mail**, **nome próprio**, e **Apelido** de uma conta válida do AAD, pretende aprovisionar.
  
     b. Clique em **adicionar e REVEJA**.
5. Reveja os dados que introduziu e, em seguida, clique em **adicionar membros**.
   
    ![Adicionar membro](./media/active-directory-saas-arcgis-tutorial/ic784750.png "Adicionar membro")
        
    > [!NOTE]
    > O marcador de posição de conta do Azure Active Directory irá receber um e-mail e siga uma ligação para confirmar a respetiva conta para ficar ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao ArcGIS Online.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a ArcGIS Online, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **ArcGIS Online**.

    ![Configurar o início de sessão único](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico ArcGIS Online no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação ArcGIS Online.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_203.png

