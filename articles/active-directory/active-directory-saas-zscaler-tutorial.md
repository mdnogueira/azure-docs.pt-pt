---
title: "Tutorial: Integração do Azure Active Directory com Zscaler | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Zscaler."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 73c81691b68ee820e1d905a17b4f2ab6b6ceb5fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Tutorial: Integração do Azure Active Directory com Zscaler

Neste tutorial, irá aprender a integrar Zscaler com o Azure Active Directory (Azure AD).

Integrar Zscaler com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Zscaler
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Zscaler (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Zscaler, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Zscaler-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Zscaler a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-zscaler-from-the-gallery"></a>Adicionar Zscaler a partir da Galeria
Para configurar a integração de Zscaler com o Azure AD, terá de adicionar Zscaler a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Zscaler a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Zscaler**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_search.png)

5. No painel de resultados, selecione **Zscaler**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Zscaler com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Zscaler é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zscaler tem de ser estabelecida.

No Zscaler, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Zscaler, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar as definições de proxy](#configuring-proxy-settings)**  - para configurar as definições de proxy no Internet Explorer
3. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
4. **[Criar um utilizador de teste Zscaler](#creating-a-zscaler-test-user)**  - para ter um homólogo de Britta Simon Zscaler que está ligada a representação do Azure AD do utilizador.
5. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
6. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Zscaler.

**Para configurar o Azure AD-início de sessão único com Zscaler, execute os seguintes passos:**

1. No portal do Azure, no **Zscaler** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_samlbase.png)

3. No **Zscaler domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.zsclaer.net`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Zscaler](https://www.zscaler.com/company/contact) para obter este valor. 

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-zscaler-tutorial/tutorial_general_400.png)

6. No **Zscaler configuração** secção, clique em **configurar Zscaler** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa ZScaler como administrador.

8. No menu na parte superior, clique em **administração**.
   
    ![Administração](./media/active-directory-saas-zscaler-tutorial/ic800206.png "administração")

9. Em **gerir administradores & funções**, clique em **gerir utilizadores e autenticação**.   
            
    ![Gerir utilizadores e autenticação](./media/active-directory-saas-zscaler-tutorial/ic800207.png "gerir utilizadores e de autenticação")

10. No **escolher opções de autenticação para a sua organização** secção, execute os seguintes passos:   
                
    ![Autenticação](./media/active-directory-saas-zscaler-tutorial/ic800208.png "autenticação")
   
    a. Selecione **autenticar utilizando SAML Single Sign-On**.

    b. Clique em **configurar SAML único início de sessão parâmetros**.

11. No **configurar SAML único início de sessão parâmetros** página da caixa de diálogo, execute os passos seguintes e, em seguida, clique em **concluído**

    ![De sessão único-](./media/active-directory-saas-zscaler-tutorial/ic800209.png "Single Sign-On")
    
    a. Colar o **único início de sessão no URL do serviço SAML** valor que copiou do portal do Azure para o **URL do Portal de SAML para os quais os utilizadores são enviados para a autenticação** caixa de texto.
    
    b. No **atributo que contém o nome de início de sessão** caixa de texto, tipo **NameID**.
    
    c. Para carregar o certificado transferido, clique em **Zscaler pem**.
    
    d. Selecione **ativar aprovisionamento automático de SAML**.

12. No **configurar a autenticação de utilizador** diálogo página, execute os seguintes passos:

    ![Administração](./media/active-directory-saas-zscaler-tutorial/ic800210.png "administração")
    
    a. Clique em **Guardar**.

    b. Clique em **ativar agora**.

## <a name="configuring-proxy-settings"></a>Configurar as definições de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de proxy no Internet Explorer

1. Iniciar **do Internet Explorer**.

2. Selecione **opções da Internet** do **ferramentas** menu para abrir o **opções da Internet** caixa de diálogo.   
    
     ![Opções da Internet](./media/active-directory-saas-zscaler-tutorial/ic769492.png "opções da Internet")

3. Clique em de **ligações** separador.   
  
     ![Ligações](./media/active-directory-saas-zscaler-tutorial/ic769493.png "ligações")

4. Clique em **definições de LAN** para abrir o **definições de LAN** caixa de diálogo.

5. Na secção de servidor Proxy, execute os seguintes passos:   
   
    ![Servidor proxy](./media/active-directory-saas-zscaler-tutorial/ic769494.png "servidor Proxy")

    a. Selecione **utilizar um servidor proxy para a sua LAN**.

    b. Na caixa de texto endereço, escreva **gateway.zscaler.net**.

    c. Na caixa de texto de porta, escreva **80**.

    d. Selecione **ignorar o servidor de proxy para endereços locais**.

    e. Clique em **OK** para fechar o **definições de rede de área Local (LAN)** caixa de diálogo.

6. Clique em **OK** para fechar o **opções da Internet** caixa de diálogo.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-zscaler-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-zscaler-test-user"></a>Criar um utilizador de teste Zscaler

Para permitir que os utilizadores do Azure AD iniciem sessão nos ZScaler, têm de ser aprovisionados para ZScaler.  
No caso de ZScaler, o aprovisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:

1. Inicie sessão no seu **Zscaler** inquilino.

2. Clique em **administração**.   
   
    ![Administração](./media/active-directory-saas-zscaler-tutorial/ic781035.png "administração")

3. Clique em **gestão de utilizadores**.   
        
     ![Adicionar](./media/active-directory-saas-zscaler-tutorial/ic781036.png "adicionar")

4. No **utilizadores** separador, clique em **adicionar**.
      
    ![Adicionar](./media/active-directory-saas-zscaler-tutorial/ic781037.png "adicionar")

5. Na secção de adicionar utilizadores, execute os seguintes passos:
        
    ![Adicionar utilizador](./media/active-directory-saas-zscaler-tutorial/ic781038.png "adicionar utilizador")
   
    a. Tipo de **UserID**, **nome a apresentar do utilizador**, **palavra-passe**, **Confirmar palavra-passe**e, em seguida, selecione **grupos** e **departamento** de uma conta válida do AAD, pretende aprovisionar.

    b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ZScaler utilizador conta criação ferramentas ou APIs fornecidas pelo ZScaler para aprovisionar contas de utilizador do AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Zscaler.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Zscaler, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Zscaler**.

    ![Configurar o início de sessão único](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Zscaler no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Zscaler.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_203.png

