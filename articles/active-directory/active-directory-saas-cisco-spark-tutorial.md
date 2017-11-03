---
title: "Tutorial: Integração do Azure Active Directory com o Cisco Spark | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Cisco Spark."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: a0a221622afe1c801a331e2319f3a7ace3111dad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Tutorial: Integração do Azure Active Directory com o Cisco Spark

Neste tutorial, irá aprender a integrar Cisco Spark no Azure Active Directory (Azure AD).

Integrar Cisco Spark com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Cisco Spark
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada Cisco spark (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Cisco Spark, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Cisco Spark-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de Cisco Spark de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-cisco-spark-from-the-gallery"></a>A adição de Cisco Spark de galeria
Para configurar a integração do Cisco Spark com o Azure AD, tem de adicionar Cisco Spark na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Cisco Spark na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Cisco Spark**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_search.png)

5. No painel de resultados, selecione **Cisco Spark**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o Spark Cisco, com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Cisco Spark é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Cisco Spark tem de ser estabelecida.

No Cisco Spark, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o Cisco Spark, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Cisco Spark](#creating-a-cisco-spark-test-user)**  - para ter um homólogo de Britta Simon no Spark Cisco que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de Cisco Spark.

**Para configurar o Azure AD-início de sessão único com o Cisco Spark, execute os seguintes passos:**

1. No portal do Azure, no **Cisco Spark** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

3. No **Cisco Spark domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL como:`https://web.ciscospark.com/#/signin`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o identificador real. Contacte [equipa de suporte de cliente de Spark Cisco](https://support.ciscospark.com/) para obter este valor. 
 
4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

5. Aplicação de Spark Cisco espera as asserções de SAML para conter os atributos específicos. Configure os seguintes atributos para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. A seguinte captura de ecrã mostra um exemplo para este.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_07.png) 

6. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributos token SAML, conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome do atributo  | Valor do atributo |
    | --------------- | -------------------- |    
    |   UID    | User.userPrincipalName |   

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **OK**.

7. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_400.png)

8. Inicie sessão no [Cisco nuvem colaboração gestão](https://admin.ciscospark.com/) com as suas credenciais de administrador global.

9. Selecione **definições** e, no **autenticação** secção, clique em **modificar**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
10. Selecione **integrar um fornecedor de identidade de terceiros 3rd. (Avançado)**  e vá para o ecrã seguinte.

11. No **importação Idp metadados** página, a arrastar e largar o ficheiro de metadados para a página do Azure AD ou utilize a opção de browser de ficheiro para localizar e carregar o ficheiro de metadados do Azure AD. Em seguida, selecione **necessitam de um certificado assinado por uma autoridade de certificação nos metadados (mais seguro)** e clique em **seguinte**. 
    
    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

12. Selecione **testar a ligação de SSO**e quando abre um novo separador do browser, autenticar-se com o Azure AD ao iniciar sessão.

13. Volte à **Cisco nuvem colaboração gestão** separador do browser. Se o teste foi concluída com êxito, selecione **este teste foi concluída com êxito. Ativar a opção de Single Sign-On** e clique em **seguinte**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-cisco-spark-test-user"></a>Criar um utilizador de teste do Cisco Spark

Nesta secção, vai criar um utilizador chamado Britta Simon no Cisco Spark. Nesta secção, vai criar um utilizador chamado Britta Simon no Cisco Spark.

1. Vá para o [Cisco nuvem colaboração gestão](https://admin.ciscospark.com/) com as suas credenciais de administrador global.

2. Clique em **utilizadores** e, em seguida, **gerir utilizadores**.
   
    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. No **utilizador gerir** janela, selecione **manualmente adiciona ou modifica os utilizadores** e clique em **seguinte**.

4. Selecione **nomes e o endereço de correio eletrónico**. Em seguida, preencha a caixa de texto da seguinte forma:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. No **nome próprio** caixa de texto, tipo **Britta**. 
    
    b. No **Apelido** caixa de texto, tipo **Simon**.
    
    c. No **endereço de correio eletrónico** caixa de texto, tipo  **britta.simon@contoso.com** .

5. Clique no sinal para adicionar Britta Simon. Clique depois em **Seguinte**.

6. No **adicionar serviços para os utilizadores** janela, clique em **guardar** e, em seguida, **concluir**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso Cisco spark.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon Cisco spark, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Cisco Spark**.

    ![Configurar o início de sessão único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico do Cisco Spark no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Cisco Spark.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[100]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png

