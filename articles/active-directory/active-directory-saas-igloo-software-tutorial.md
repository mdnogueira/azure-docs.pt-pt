---
title: "Tutorial: Integração do Azure Active Directory com o Igloo Software | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Igloo Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: ab3891e11eb33b4d233e4fc967a40c7df06e4f4e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Integração do Azure Active Directory com Igloo Software

Neste tutorial, irá aprender a integrar Igloo Software com o Azure Active Directory (Azure AD).

Integrar Igloo Software com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Igloo Software
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Software Igloo (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Igloo Software, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Igloo Software-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Igloo Software a partir da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-igloo-software-from-the-gallery"></a>Adicionar Igloo Software a partir da galeria do
Para configurar a integração de Igloo Software com o Azure AD, terá de adicionar Igloo Software a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Igloo Software a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Igloo Software**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. No painel de resultados, selecione **Igloo Software**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o Software de Igloo com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Igloo Software é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Igloo Software tem de ser estabelecida.

No Igloo Software, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o Software de Igloo, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de Igloo Software](#creating-an-igloo-software-test-user)**  - para ter um homólogo de Britta Simon Igloo Software que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Igloo Software.

**Para configurar o Azure AD-início de sessão único com o Software de Igloo, execute os seguintes passos:**

1. No portal do Azure, no **Igloo Software** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. No **Igloo Software domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<company name>.igloocommmunities.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<company name>.igloocommmunities.com/saml.digest`

    c. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [equipa de suporte de cliente de Software Igloo](https://www.igloosoftware.com/services/support) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-igloo-software-tutorial/tutorial_general_400.png)
    
6. No **Igloo Software configuração** secção, clique em **configurar Software Igloo** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Igloo Software como um administrador.

8. Vá para o **painel de controlo**.
   
     ![Painel de controlo](./media/active-directory-saas-igloo-software-tutorial/ic799949.png "painel de controlo")

9. No **associação** separador, clique em **definições de início de sessão**.
   
    ![Iniciar sessão nas definições](./media/active-directory-saas-igloo-software-tutorial/ic783968.png "iniciar sessão nas definições")

10. Na secção de configuração de SAML, clique em **configurar a autenticação SAML**.
   
    ![Configuração de SAML](./media/active-directory-saas-igloo-software-tutorial/ic783969.png "configuração SAML")
   
11. No **configuração geral** secção, execute os seguintes passos:
   
    ![Configuração geral](./media/active-directory-saas-igloo-software-tutorial/ic783970.png "configuração geral")

    a. No **nome da ligação** caixa de texto, escreva um nome personalizado para a sua configuração.
   
    b. No **URL de início de sessão do IdP** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
   
    c. No **URL de fim de sessão do IdP** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure.
    
    d. Selecione **resposta de fim de sessão e o tipo de pedido de HTTP** como **POST**.
   
    e. Abra o **base-64** certificado codificado no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado público** caixa de texto.
    
12. No **resposta e a configuração de autenticação**, execute os seguintes passos:
    
    ![Resposta e a configuração de autenticação](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "resposta e a configuração de autenticação")
  
      a. Como **fornecedor de identidade**, selecione **Microsoft ADFS**.
      
      b. Como **tipo de identificador**, selecione **endereço de correio eletrónico**. 

      c. No **atributo de correio eletrónico** caixa de texto, tipo **emailaddress**.

      d. No **nome próprio atributo** caixa de texto, tipo **givenname**.

      e. No **último nome do atributo** caixa de texto, tipo **Apelido**.

13. Execute os seguintes passos para concluir a configuração:
    
    ![Criação de utilizador no início de sessão](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "criação de utilizador no início de sessão") 

     a. Como **criação de utilizador no início de sessão**, selecione **criar um novo utilizador do seu site quando iniciam sessão no**.

     b. Como **iniciar sessão nas definições**, selecione **botão SAML de utilização no ecrã de "Iniciar sessão"**.

     c. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-igloo-software-test-user"></a>Criar um utilizador de teste Igloo Software

Não há nenhum item de ação para configurar o aprovisionamento de utilizadores para Igloo Software.  

Quando um utilizador atribuído tenta iniciar sessão no Software Igloo através do painel de acesso, o Igloo Software verifica se o utilizador existe.  Se não existe nenhuma conta de utilizador ainda estão disponíveis, é criado automaticamente pelo Software de Igloo.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao Igloo Software.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon Igloo software, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Igloo Software**.

    ![Configurar o início de sessão único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de Igloo Software no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Igloo Software.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_203.png

