---
title: "Tutorial: Integração do Azure Active Directory com Grovo | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Grovo."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: jeedes
ms.openlocfilehash: 66e5731d185399267a581e5c6b2485fb4fc7f503
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="tutorial-azure-active-directory-integration-with-grovo"></a>Tutorial: Integração do Azure Active Directory com Grovo

Neste tutorial, irá aprender a integrar Grovo com o Azure Active Directory (Azure AD).

Integrar Grovo com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Grovo.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Grovo (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Grovo, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Grovo-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Grovo a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-grovo-from-the-gallery"></a>Adicionar Grovo a partir da Galeria
Para configurar a integração de Grovo com o Azure AD, terá de adicionar Grovo a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Grovo a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![As aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Grovo**, selecione **Grovo** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Grovo na lista de resultados](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Grovo com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Grovo é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Grovo tem de ser estabelecida.

No Grovo, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Grovo, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Grovo](#create-a-grovo-test-user)**  - para ter um homólogo de Britta Simon Grovo que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Grovo.

**Para configurar o Azure AD-início de sessão único com Grovo, execute os seguintes passos:**

1. No portal do Azure, no **Grovo** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_samlbase.png)

3. No **Grovo domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Domínio Grovo e os URLs únicos de informações de início de sessão](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

4.  Verifique **Mostrar avançadas definições de URL**, execute os seguintes passos:  

    ![Domínio Grovo e os URLs únicos de informações de início de sessão](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url1.png)

    a. No **reencaminhamento estado** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.grovo.com`

    b. Se pretender configurar a aplicação no **SP** iniciada modo, execute os seguintes passos:

    ![Domínio Grovo e os URLs únicos de informações de início de sessão](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url2.png)

    No **iniciar sessão no URL** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador, o URL de resposta, real de sessão no estado de URL e reencaminhamento. Contacte [equipa de suporte de Grovo](https://www.grovo.com/contact-us) para obter estes valores.
 
5. Aplicação de Grovo espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos a partir de "**atributos de utilizador**" secção na página de integração de aplicações. A seguinte captura de ecrã mostra um exemplo para este.
    
    ![Configurar o início de sessão único atributo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_attribute.png)
    
6. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributo token SAML, conforme mostrado na imagem e efetuar os seguintes passos:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | Nome Próprio              | User.givenName |
    | Apelido               | User.Surname |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o atributo de início de sessão único](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_04.png)

    ![Configurar o atributo de início de sessão único](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_05.png)

    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **OK**.


7. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O tinta digital Transferir certificado](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_certificate.png) 

8. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-grovo-tutorial/tutorial_general_400.png)

9. No **Grovo configuração** secção, clique em **configurar Grovo** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_configure.png) 

10. Numa janela do browser web diferente, inicie sessão no Grovo como administrador.

11. Aceda a **ADMIN** > **integrações**.
 
    ![Configuração de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_admin.png) 

12. Clique em **configurar** em **SP iniciada SAML 2.0** secção.

    ![Configuração de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_setup.png)

13. No **SP iniciada SAML 2.0** janela de pop-up, execute os seguintes passos:

    ![Configuração de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_saml.png)

    a. No **id de entidade** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou a partir do portal do Azure.

    b. No **único ponto final do serviço de início de sessão** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou a partir do portal do Azure.

    c. Selecione **no enlace do ponto final de serviço início de sessão único** como `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Abra o transferido **certificado codificado Base64** no portal do Azure no bloco de notas, cole-a para o **chave pública** caixa de texto.

    e. Clique em **Seguinte**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-grovo-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-grovo-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-grovo-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-grovo-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-grovo-test-user"></a>Criar um utilizador de teste Grovo

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon Grovo. Grovo suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante a tentativa de aceder Grovo se não existir ainda.
>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de Grovo](https://www.grovo.com/contact-us).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Grovo.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Grovo, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Grovo**.

    ![A ligação de Grovo na lista de aplicações](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Grovo no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Grovo.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_203.png

