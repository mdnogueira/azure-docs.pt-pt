---
title: "Tutorial: Integração do Azure Active Directory com TimeOffManager | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e TimeOffManager."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 3f944ffbf704694b293b4b1e5bdb4f2c93ae35a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Tutorial: Integração do Azure Active Directory com TimeOffManager

Neste tutorial, irá aprender a integrar TimeOffManager com o Azure Active Directory (Azure AD).

Integrar TimeOffManager com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao TimeOffManager
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para TimeOffManager (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com TimeOffManager, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um TimeOffManager-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar TimeOffManager a partir da Galeria
2. Configurar e testar o Azure AD-início de sessão único

## <a name="add-timeoffmanager-from-the-gallery"></a>Adicionar TimeOffManager a partir da Galeria
Para configurar a integração de TimeOffManager com o Azure AD, terá de adicionar TimeOffManager a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar TimeOffManager a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **TimeOffManager**, selecione **TimeOffManager** do painel de resultados e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Adicionar a partir da Galeria](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com TimeOffManager com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no TimeOffManager é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TimeOffManager tem de ser estabelecida.

No TimeOffManager, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com TimeOffManager, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste TimeOffManager](#create-a-timeoffmanager-test-user)**  - para ter um homólogo de Britta Simon TimeOffManager que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação TimeOffManager.

**Para configurar o Azure AD-início de sessão único com TimeOffManager, execute os seguintes passos:**

1. No portal do Azure, no **TimeOffManager** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![SAML com base em início de sessão](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

3. No **TimeOffManager domínio e os URLs** secção, efetue o seguinte:

     ![Secção TimeOffManager domínio e os URLs](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de resposta real. Pode obter este valor de **início de sessão único na página Definições** que é explicada mais tarde no tutorial ou contacte [equipa de suporte de TimeOffManager](http://www.timeoffmanager.com/contact-us.aspx).
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Secção de certificado de assinatura de SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

5. O objetivo desta secção consiste em descrevem como permitir que os utilizadores para se autenticar TimeOffManger à respetiva conta no Azure AD com Federação com base no protocolo SAML.
    
    A aplicação de TimeOffManger espera as asserções de SAML num formato específico, que necessita para adicionar mapeamentos de atributos personalizado à sua configuração de atributos token SAML. A seguinte captura de ecrã mostra um exemplo para este.

    ![atributos de token SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "atributos token saml")
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | nome próprio |User.givenName |
    | Apelido |User.Surname |
    | E-mail |User.Mail |
    
    a.  Para cada linha de dados na tabela anterior, clique em **adicionar o atributo de utilizador**.
    
    ![atributos de token SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "atributos token saml")
    
    ![atributos de token SAML](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "atributos token saml")
    
    b.  No **nome de atributo** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c.  No **valor do atributo** caixa de texto, selecione o valor do atributo apresentado para essa linha.
    
    d.  Clique em **OK**.
    
6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_400.png)

7. No **TimeOffManager configuração** secção, clique em **configurar TimeOffManager** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Secção de configuração de TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

8. Numa janela do browser web diferente, inicie sessão no site da sua empresa TimeOffManager como administrador.

9. Aceda a **conta \> conta opções \> Single Sign-On definições**.
   
   ![Single Sign-On definições](./media/active-directory-saas-timeoffmanager-tutorial/ic795917.png "único as definições de início de sessão")
7. No **as definições de início de sessão único** secção, execute os seguintes passos:
   
   ![Single Sign-On definições](./media/active-directory-saas-timeoffmanager-tutorial/ic795918.png "único as definições de início de sessão")
   
   a. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole o certificado completo no **certificado x. 509** caixa de texto.
   
   b. No **Idp emissor** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.
   
   c. No **URL de ponto final do IdP** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
   
   d. Como **impor SAML**, selecione **não**.
   
   e. Como **Auto-criar utilizadores**, selecione **Sim**.
   
   f. No **URL de fim de sessão** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure.
   
   g. Clique em **guardar alterações**.

11. No **as definições de início de sessão único** página, copie o valor da **asserção URL do serviço de consumidor** e cole-a no **URL de resposta** caixa de texto em **TimeOffManager Domínios e URLs** secção no portal do Azure. 

      ![Single Sign-On definições](./media/active-directory-saas-timeoffmanager-tutorial/ic795915.png "único as definições de início de sessão")

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Utilizadores e grupos--> todos os utilizadores](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Botão Adicionar](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Página de caixa de diálogo de utilizador](./media/active-directory-saas-timeoffmanager-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-timeoffmanager-test-user"></a>Criar um utilizador de teste TimeOffManager

Para permitir que os utilizadores do Azure AD sessão TimeOffManager, tem de ser aprovisionadas para TimeOffManager.  

TimeOffManager apenas suporta o aprovisionamento de utilizadores do tempo. Não há nenhum item de ação para si.  

Os utilizadores são adicionados automaticamente durante o início de sessão primeiro utilizar o início de sessão único no.

>[!NOTE]
>Pode utilizar quaisquer outras TimeOffManager utilizador conta criação ferramentas ou APIs fornecidas pelo TimeOffManager aprovisionar contas de utilizador do Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para TimeOffManager.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a TimeOffManager, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **TimeOffManager**.

    ![TimeOffManager na lista de aplicações](./media/active-directory-saas-timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico TimeOffManager no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de TimeOffManager. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-timeoffmanager-tutorial/tutorial_general_203.png

