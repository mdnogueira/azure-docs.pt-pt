---
title: "Tutorial: Integração do Azure Active Directory com Samanage | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Samanage."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: c54dbe407145a29a712acc3c0fb549a38ac26bed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Tutorial: Integração do Azure Active Directory com Samanage

Neste tutorial, irá aprender a integrar Samanage com o Azure Active Directory (Azure AD).

Integrar Samanage com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Samanage
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Samanage (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Samanage, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Samanage-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Samanage a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-samanage-from-the-gallery"></a>Adicionar Samanage a partir da Galeria
Para configurar a integração de Samanage com o Azure AD, terá de adicionar Samanage a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Samanage a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Samanage**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_search.png)

5. No painel de resultados, selecione **Samanage**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Samanage com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Samanage é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Samanage tem de ser estabelecida.

No Samanage, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Samanage, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Samanage](#creating-a-samanage-test-user)**  - para ter um homólogo de Britta Simon Samanage que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Samanage.

**Para configurar o Azure AD-início de sessão único com Samanage, execute os seguintes passos:**

1. No portal do Azure, no **Samanage** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_samlbase.png)

3. No **Samanage domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão real e o identificador, o que é explicada mais tarde no tutorial. Para obter mais informações contacte [equipa de suporte de cliente Samanage](https://www.samanage.com/support).    
 
4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-samanage-tutorial/tutorial_general_400.png)

6. No **Samanage configuração** secção, clique em **configurar Samanage** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o ID de entidade de SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Samanage como administrador.

8. Clique em **Dashboard** e selecione **configuração** no painel de navegação esquerdo.
   
    ![Dashboard](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")

9. Clique em **de sessão único-**.
   
    ![De sessão único-](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")

10. Navegue para **início de sessão utilizando SAML** secção, execute os seguintes passos:
   
    ![Início de sessão utilizando SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "início de sessão utilizando SAML")
 
    a. Clique em **ativar início de sessão com o SAML**.  
 
    b. No **URL do fornecedor de identidade** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.    
 
    c. Confirme a **URL de início de sessão** corresponde a **URL de início de sessão** de **Samanage domínio e os URLs** secção no portal do Azure.
 
    d. No **URL de fim de sessão** caixa de texto, introduza o valor de **Sign-Out URL** que copiou do portal do Azure.
 
    e. No **SAML emissor** caixa de texto, escreva o id de aplicação URI definido no seu fornecedor de identidade.
 
    f. Abra o certificado codificado de base-64 transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **colar a x. 509 do fornecedor de identidade certificado abaixo** caixa de texto.
 
    g. Clique em **criar utilizadores caso não existam no Samanage**.
 
    h. Clique em **atualização**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-samanage-test-user"></a>Criar um utilizador de teste Samanage

Para permitir que os utilizadores do Azure AD iniciem sessão nos Samanage, têm de ser aprovisionados para Samanage.  
No caso de Samanage, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Samanage como administrador.

2. Clique em **Dashboard** e selecione **configuração** no pan de navegação esquerdo.
   
    ![A configuração](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "programa de configuração")

3. Clique em de **utilizadores** separador
   
    ![Os utilizadores](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "utilizadores")

4. Clique em **novo utilizador**.
   
    ![Novo utilizador](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "novo utilizador")

5. Tipo de **nome** e **endereço de correio eletrónico** de uma conta do Azure Active Directory que pretende aprovisionar e clique em **criar utilizador**.
   
    ![Criar utilizador](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "criar utilizador")
   
   >[!NOTE]
   >O marcador de posição de conta do Azure Active Directory irá receber um e-mail e siga uma ligação para confirmar a respetiva conta para ficar ativa. Pode utilizar quaisquer outras Samanage utilizador conta criação ferramentas ou APIs fornecidas pelo Samanage para aprovisionar o Azure Active Directory contas de utilizador.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Samanage.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Samanage, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Samanage**.

    ![Configurar o início de sessão único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Samanage no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Samanage.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_203.png

