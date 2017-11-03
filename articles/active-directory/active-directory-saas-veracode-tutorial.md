---
title: "Tutorial: Integração do Azure Active Directory com Veracode | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Veracode."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: d49349c5ae08e67d91e30967f3644623211823ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Tutorial: Integração do Azure Active Directory com Veracode

Neste tutorial, irá aprender a integrar Veracode com o Azure Active Directory (Azure AD).

Integrar Veracode com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Veracode.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Veracode (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Veracode, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Veracode-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Veracode a partir da Galeria
2. Configurar e testar o Azure AD-início de sessão único

## <a name="add-veracode-from-the-gallery"></a>Adicionar Veracode a partir da Galeria
Para configurar a integração de Veracode com o Azure AD, terá de adicionar Veracode a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Veracode a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Veracode**, selecione **Veracode** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Veracode na lista de resultados](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Veracode com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Veracode é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Veracode tem de ser estabelecida.

No Veracode, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Veracode, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Veracode](#create-a-veracode-test-user)**  - para ter um homólogo de Britta Simon Veracode que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Veracode.

**Para configurar o Azure AD-início de sessão único com Veracode, execute os seguintes passos:**

1. No portal do Azure, no **Veracode** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_samlbase.png)

3. No **Veracode domínio e os URLs** secção, o utilizador não tem de efetuar quaisquer passos, tal como a aplicação já está pré-integrada com o Azure. 

    ![Configurar o início de sessão único](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_url.png)

4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_certificate.png) 

5. O objetivo desta secção consiste em descrevem como permitir que os utilizadores para se autenticar Veracode à respetiva conta no Azure AD com Federação com base no protocolo SAML.

    A aplicação de Veracode espera as asserções de SAML num formato específico, que necessita para adicionar mapeamentos de atributos personalizados para o **atributos token saml** configuração. A seguinte captura de ecrã mostra um exemplo para este.
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_attr.png "atributos")

6. Para adicionar os mapeamentos de atributos necessários, execute os seguintes passos:

    | Nome do atributo | Valor do atributo |
    |--- |--- |
    | nome próprio |User.givenName |
    | Apelido |User.Surname |
    | Correio eletrónico |User.Mail |
    
    a. Para cada linha de dados na tabela anterior, clique em **adicionar o atributo de utilizador**.
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr.png "atributos")
    
    ![Atributos](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr1.png "atributos")
    
    b. No **nome de atributo** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. No **valor do atributo** caixa de texto, selecione o valor do atributo apresentado para essa linha.
    
    d. Clique em **OK**.

7. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-veracode-tutorial/tutorial_general_400.png)

8. No **Veracode configuração** secção, clique em **configurar Veracode** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML** do **secção de referência rápida.**

    ![Configuração de Veracode](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_configure.png) 

9. Numa janela do browser web diferente, inicie sessão no site da sua empresa Veracode como administrador.

10. No menu na parte superior, clique em **definições**e, em seguida, clique em **Admin**.
   
    ![Administração](./media/active-directory-saas-veracode-tutorial/ic802911.png "administração")

11. Clique em de **SAML** separador.

12. No **organização SAML definições** secção, execute os seguintes passos:
   
    ![Administração](./media/active-directory-saas-veracode-tutorial/ic802912.png "administração")
   
    a.  No **emissor** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.
    
    b. Para carregar o certificado transferido a partir do portal do Azure, clique em **Escolher ficheiro**.
   
    c. Selecione **ativar o registo de recuperação Self-**.

13. No **definições de registo de Self** secção, execute os seguintes passos e, em seguida, clique em **guardar**:
   
    ![Administração](./media/active-directory-saas-veracode-tutorial/ic802913.png "administração")
   
    a. Como **ativação do novo utilizador**, selecione **não ativação necessária**.
   
    b. Como **as atualizações de dados de utilizador**, selecione **preferência Veracode utilizador dados**.
   
    c. Para **detalhes de atributo de SAML**, selecione o seguinte:
      * **Funções de utilizador**
      * **Administrador de política**
      * **Revisor**
      * **Antecedência de segurança**
      * **Executivo**
      * **Submissor**
      * **Criador de**
      * **Todos os tipos de análise**
      * **Associações de equipa**
      * **Equipa predefinidos**

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-veracode-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-veracode-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-veracode-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-veracode-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-veracode-test-user"></a>Criar um utilizador de teste Veracode
Para permitir que os utilizadores do Azure AD sessão Veracode, têm de ser aprovisionados para Veracode. No caso de Veracode, o aprovisionamento é uma tarefas automatizadas. Não há nenhum item de ação para si. Os utilizadores são criados automaticamente se necessário durante a primeira único início de sessão tentativa.

> [!NOTE]
> Pode utilizar quaisquer outras Veracode utilizador conta criação ferramentas ou APIs fornecidas pelo Veracode aprovisionar contas de utilizador do Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Veracode.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Veracode, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Veracode**.

    ![A ligação de Veracode na lista de aplicações](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Veracode no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Veracode.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_203.png

