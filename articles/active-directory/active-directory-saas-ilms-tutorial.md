---
title: "Tutorial: Integração do Azure Active Directory com iLMS | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e iLMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 22c72020200138e78835ed7dd2661f18b824c785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Tutorial: Integração do Azure Active Directory com iLMS

Neste tutorial, irá aprender a integrar iLMS com o Azure Active Directory (Azure AD).

Integrar iLMS com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao iLMS
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para iLMS (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com iLMS, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um iLMS início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar iLMS a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-ilms-from-the-gallery"></a>Adicionar iLMS a partir da Galeria
Para configurar a integração de iLMS com o Azure AD, terá de adicionar iLMS a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar iLMS a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **iLMS**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_search.png)

5. No painel de resultados, selecione **iLMS**, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com iLMS com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no iLMS é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no iLMS tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no iLMS.

Para configurar e testar o Azure AD-início de sessão único com iLMS, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste iLMS](#creating-an-ilms-test-user)**  - para ter um homólogo de Britta Simon no iLMS que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação iLMS.

**Para configurar o Azure AD-início de sessão único com iLMS, execute os seguintes passos:**

1. No portal do Azure, no **iLMS** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_samlbase.png)

3. No **iLMS domínios e URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url.png)

    a. No **identificador** caixa de texto, cole o **identificador** valor copiar de **fornecedor de serviços** secção de definições de SAML no portal de administração de iLMS.

    b. No **URL de resposta** caixa de texto, cole o **(URL) do ponto final** valor copiar de **fornecedor de serviços** secção de definições de SAML no portal de administração de iLMS ter o seguinte padrão`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >Este 123456 é um valor de exemplo do identificador.

4. Verifique **Mostrar avançadas definições de URL**, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url1.png)

    No **URL de início de sessão** caixa de texto, cole o **(URL) do ponto final** valor copiar de **fornecedor de serviços** secção de definições de SAML no portal de administração de iLMS como`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

5. Para ativar o aprovisionamento de JIT, iLMS aplicação espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. A seguinte captura de ecrã mostra um exemplo para este.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/4.png)
    
    Criar **autorizações de departamento, região** e **divisão** de atributos e adicione o nome destes atributos em iLMS. Todos os estes atributos mostrados acima são necessários.  

    > [!NOTE] 
    > Tem de ativar **criar conta de utilizador Un-recognized** no iLMS para mapear estes atributos. Siga as instruções [aqui](http://support.inspiredelearning.com/customer/portal/articles/2204526) para ter uma ideia na configuração de atributos.

6. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributos token SAML, conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |    
    | divisão | User.Department |
    | Região | User.state |
    | Departamento | User.jobtitle |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_04.png)

    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_05.png)
    
    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**

7. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_certificate.png) 

8. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-iLMS-tutorial/tutorial_general_400.png)

9. Numa janela do browser web diferente, inicie sessão no seu **portal de administração de iLMS** como administrador.

10. Clique em **SSO:SAML** em **definições** separador para abrir as definições de SAML e execute os seguintes passos:
    
    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/1.png) 

    a. Expanda o **fornecedor de serviços** secção e copie o **identificador** e **(URL) do ponto final** valor.

    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/2.png) 

    b. Em **fornecedor de identidade** secção, clique em **importar metadados**.
    
    c. Selecione o **metadados** ficheiro transferido a partir do Portal do Azure a partir do **certificado de assinatura de SAML** secção.

    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Se pretender ativar JIT criar contas de iLMS para anular o aprovisionamento-reconhece os utilizadores, siga os passos abaixo:
        
       - Verifique **criar conta de utilizador não reconhecido**.
       
       ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Mapear os atributos no Azure AD com os atributos no iLMS. Na coluna de atributo, especifique o nome de atributos ou o valor predefinido.

    e. Aceda a **regras de negócio** separador e execute os seguintes passos: 
        
       ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/5.png)

       - Verifique **divisões, criar das Un-recognized regiões e departamentos** criar divisões, das regiões e departamentos que ainda não existam no momento de início de sessão único.
        
       - Verifique **atualização utilizador perfil durante o início de sessão** para especificar se o perfil do utilizador é atualizado com cada início de sessão. 
        
       - Se o **"Atualização em branco valores para não campos no perfil de utilizador obrigatório"** opção for selecionada, os campos de perfil opcional que estejam em branco após o início de sessão irá fazer com que também o perfil do utilizador iLMS que contém valores em branco para esses campos.
        
       - Verifique **Enviar E-Mail de notificação de erro** e introduza a mensagem de e-mail do utilizador em que pretende receber o e-mail de notificação de erro.

11. Clique em **guardar** botão para guardar as definições.

    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/save.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-ilms-test-user"></a>Criar um utilizador de teste iLMS

Aplicação suporta apenas no tempo de aprovisionamento de utilizador e de utilizadores de autenticação são criados automaticamente na aplicação. JIT funcione, caso tenha clicado no **criar conta de utilizador Un-recognized** caixa de verificação durante a definição de configuração de SAML no portal de administração de iLMS.

Se precisar de criar manualmente um utilizador, em seguida, siga os passos abaixo:

1. Inicie sessão no site da sua empresa iLMS como administrador.

2. Clique em **"Registar o utilizador"** em **utilizadores** separador para abrir **registar utilizador** página. 
   
   ![Adicionar empregado](./media/active-directory-saas-ilms-tutorial/3.png)

3. No **"Registar o utilizador"** página, execute os seguintes passos.

    ![Adicionar empregado](./media/active-directory-saas-ilms-tutorial/create_testuser_add.png)

    a. No **nome próprio** caixa de texto, o nome de tipo primeiro Britta.
   
    b. No **Apelido** caixa de texto, escreva o apelido Simon.

    c. No **ID de correio eletrónico** caixa de texto, escreva o endereço de correio eletrónico da conta de Britta Simon.

    d. No **região** lista pendente, selecione o valor da região.

    e. No **divisão** lista pendente, selecione o valor de divisão.

    f. No **departamento** lista pendente, selecione o valor do departamento.

    g. Clique em **Guardar**.

    > [!NOTE] 
    > Pode enviar correio de registo para o utilizador selecionando **enviar correio de registo** caixa de verificação.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao iLMS.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a iLMS, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **iLMS**.

    ![Configurar o início de sessão único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico iLMS no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de iLMS.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_203.png

