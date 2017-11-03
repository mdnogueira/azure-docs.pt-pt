---
title: "Tutorial: Integração do Azure Active Directory com AirWatch | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e AirWatch."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 1996ec97e7c0d94c5606ca43bb5956548f1f3712
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Tutorial: Integração do Azure Active Directory com AirWatch

Neste tutorial, irá aprender a integrar AirWatch com o Azure Active Directory (Azure AD).

Integrar AirWatch com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao AirWatch
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para AirWatch (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com AirWatch, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um AirWatch início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar AirWatch a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-airwatch-from-the-gallery"></a>Adicionar AirWatch a partir da Galeria
Para configurar a integração de AirWatch com o Azure AD, terá de adicionar AirWatch a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar AirWatch a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **AirWatch**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_search.png)

5. No painel de resultados, selecione **AirWatch**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com AirWatch com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no AirWatch é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no AirWatch tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no AirWatch.

Para configurar e testar o Azure AD-início de sessão único com AirWatch, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste AirWatch](#creating-a-airwatch-test-user)**  - para ter um homólogo de Britta Simon AirWatch que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação AirWatch.

**Para configurar o Azure AD-início de sessão único com AirWatch, execute os seguintes passos:**

1. No portal do Azure, no **AirWatch** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. No **AirWatch domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. No **identificador** caixa de texto, digite o valor como`AirWatch`

    > [!NOTE] 
    > Este valor não é o real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente AirWatch](http://www.air-watch.com/company/contact-us/) para obter este valor. 
 
4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. No **AirWatch configuração** secção, clique em **configurar AirWatch** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-airwatch-tutorial/tutorial_general_400.png)
<CS>
7. Numa janela do browser web diferente, inicie sessão no site da sua empresa AirWatch como administrador.

8. No painel de navegação esquerdo, clique em **contas**e, em seguida, clique em **administradores**.
   
   ![Os administradores](./media/active-directory-saas-airwatch-tutorial/ic791920.png "administradores")

9. Expanda o **definições** e, em seguida, clique em **serviços de diretório**.
   
   ![Definições](./media/active-directory-saas-airwatch-tutorial/ic791921.png "definições")

10. Clique em de **utilizador** separador o **Base DN** caixa de texto, escreva o seu nome de domínio e, em seguida, clique em **guardar**.
   
   ![Utilizador](./media/active-directory-saas-airwatch-tutorial/ic791922.png "utilizador")

11. Clique em de **servidor** separador.
   
   ![Servidor](./media/active-directory-saas-airwatch-tutorial/ic791923.png "servidor")

12. Execute os seguintes passos:
    
    ![Carregar](./media/active-directory-saas-airwatch-tutorial/ic791924.png "carregar")   
    
    a. Como **do tipo**, selecione **nenhum**.

    b. Selecione **utilizar SAML para autenticação**.

    c. Para carregar o certificado transferido, clique em **carregar**.

13. No **pedido** secção, execute os seguintes passos:
    
    ![Pedir](./media/active-directory-saas-airwatch-tutorial/ic791925.png "pedido")  

    a. Como **solicitar tipo de enlace**, selecione **POST**.

    b. No portal do Azure, no **configurar o início de sessão único em Airwatch** página da caixa de diálogo, copie o **único início de sessão no URL do serviço SAML** valor e, em seguida, cole-o para o **fornecedor único início de sessão no URL da identidade** caixa de texto.

    c. Como **NameID formato**, selecione **endereço de correio eletrónico**.

    d. Clique em **Guardar**.

14. Clique em de **utilizador** separador novamente.
    
    ![Utilizador](./media/active-directory-saas-airwatch-tutorial/ic791926.png "utilizador")

15. No **atributo** secção, execute os seguintes passos:
    
    ![Atributo](./media/active-directory-saas-airwatch-tutorial/ic791927.png "atributo")

    a. No **identificador de objeto** caixa de texto, tipo **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. No **Username** caixa de texto, tipo **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. No **nome a apresentar** caixa de texto, tipo **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. No **nome próprio** caixa de texto, tipo **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. No **Apelido** caixa de texto, tipo **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. No **E-Mail** caixa de texto, tipo **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Clique em **Guardar**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de Britta Simon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-airwatch-test-user"></a>Criar um utilizador de teste AirWatch

Para permitir que os utilizadores do Azure AD iniciem sessão nos AirWatch, estes têm de ser aprovisionados para AirWatch.

* Quando AirWatch, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **AirWatch** site da empresa como administrador.
2. No painel de navegação no lado esquerdo, clique em **contas**e, em seguida, clique em **utilizadores**.
   
   ![Os utilizadores](./media/active-directory-saas-airwatch-tutorial/ic791929.png "utilizadores")
3. No **utilizadores** menu, clique em **vista de lista**e, em seguida, clique em **adicionar \> adicionar utilizador**.
   
   ![Adicionar utilizador](./media/active-directory-saas-airwatch-tutorial/ic791930.png "adicionar utilizador")
4. No **Adicionar / Editar utilizador** caixa de diálogo, execute os seguintes passos:

   ![Adicionar utilizador](./media/active-directory-saas-airwatch-tutorial/ic791931.png "adicionar utilizador")   
   1. Tipo de **Username**, **palavra-passe**, **Confirmar palavra-passe**, **nome próprio**, **Apelido**, **endereço de correio eletrónico** de uma conta válida do Azure Active Directory que pretende aprovisionar num relacionados caixas de texto.
   2. Clique em **Guardar**.

>[!NOTE]
>Pode utilizar quaisquer outras AirWatch utilizador conta criação ferramentas ou APIs fornecidas pelo AirWatch para aprovisionar contas de utilizador do AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para AirWatch.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a AirWatch, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **AirWatch**.

    ![Configurar o início de sessão único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Se pretender testar as definições de início de sessão único, abra o painel de acesso. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_203.png

