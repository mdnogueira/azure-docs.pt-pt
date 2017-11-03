---
title: "Tutorial: Integração do Azure Active Directory com RFPIO | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e RFPIO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 26a8bb17dad5a01b401ce7f9b484f09822825cbf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Tutorial: Integração do Azure Active Directory com RFPIO

Neste tutorial, irá aprender a integrar RFPIO com o Azure Active Directory (Azure AD).

Integrar RFPIO com o Azure AD fornece as seguintes vantagens:

- Pode controlar quem no Azure AD que tenha acesso ao RFPIO.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para RFPIO (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa única localização central, o portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com RFPIO, terá dos seguintes itens:

- Uma subscrição do Azure AD.
- Uma RFPIO única sessão em ativado subscrição.

> [!NOTE]
> Não recomendamos que utilize um ambiente de produção para testar os passos neste tutorial.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, exceto se for necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário é descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar RFPIO a partir da galeria.
2. Configurar e testar o Azure AD único início de sessão.

## <a name="add-rfpio-from-the-gallery"></a>Adicionar RFPIO a partir da Galeria
Para configurar a integração de RFPIO com o Azure AD, terá de adicionar RFPIO a partir da Galeria à sua lista de aplicações SaaS geridas.

### <a name="to-add-rfpio-from-the-gallery"></a>Para adicionar RFPIO a partir da Galeria

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, selecione o **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Selecione **aplicações empresariais**e, em seguida, selecione **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **RFPIO**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_search.png)

5. No painel de resultados, selecione **RFPIO**e, em seguida, selecione o **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com RFPIO com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que é a relação entre o utilizador homólogo RFPIO e um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no RFPIO tem de ser estabelecida.

No RFPIO, atribua o valor da **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com RFPIO, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**– para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**– para testar do Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste RFPIO](#creating-a-rfpio-test-user)**  – para ter um homólogo de Britta Simon no RFPIO que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**– ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação RFPIO.

**Para configurar o Azure AD-início de sessão único com RFPIO, execute os seguintes passos:**

1. No portal do Azure, no **RFPIO** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. No **RFPIO domínio e os URLs** secção, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url.png)

    a. No **identificador** caixa de texto, escreva o URL:`https://www.rfpio.com`

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Verifique **Mostrar avançadas definições de URL**.

    c. No **reencaminhamento estado** caixa de texto introduza um valor de cadeia. Contacte [RFPIO suporta equipa](https://www.rfpio.com/contact/) para obter este valor. 

4. Verifique **Mostrar avançadas definições de URL**. Se pretender configurar a aplicação no **SP** iniciada modo: 

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url2.png)

    No **iniciar sessão no URL** caixa de texto, escreva o URL:`https://www.app.rfpio.com`

5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/tutorial_general_400.png)

7. Numa janela do browser web diferente, início de sessão para o **RFPIO** Web site como um administrador.

8. Clique na parte inferior esquerda canto lista pendente.

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app1.png)

9. Clique em de **organização definições**. 

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app2.png)

10. Clique em de **funcionalidades & integração**.

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app4.png)

11. No **configuração SAML SSO** clique **editar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app3.png)

12. Nesta secção, execute os seguintes ações:

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app5.png)
    
    a. Copie o conteúdo do **transferido o XML de metadados** e cole-o para o **configuração da identidade** campo.

    > [!NOTE]
    >Para copiar o conteúdo transferido **XML de metadados** utilize **bloco de notas + +** ou adequada **Editor de XML**. 

    b. Clique em **validar**.

    c. Depois de clicar em **validar**, Flip **SAML(Enabled)** como on.

    d. Clique em **submeter**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-rfpio-test-user"></a>Criar um utilizador de teste RFPIO

Para permitir que os utilizadores do Azure AD iniciem sessão nos RFPIO, têm de ser aprovisionados para RFPIO.  
No caso de RFPIO, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa RFPIO como administrador.

2. Clique na parte inferior esquerda canto lista pendente.

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app1.png)

3. Clique em de **organização definições**. 

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app2.png)

4. Clique em **os membros da equipa**.

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app6.png)

5. Clique em **adicionar membros**.

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app7.png)

6. No **adicionar novos membros** secção. Execute os seguintes ações:

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/app8.png)

    a. Introduza **endereço de correio eletrónico** no **introduza um e-mail por linha** campo.

    b. Selecione Plese **função** de acordo com os seus requisitos.

    c. Clique em **adicionar membros**.
        
    > [!NOTE]
    > Titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue-se de uma ligação para confirmar a respetiva conta para ficar ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para RFPIO.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a RFPIO, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **RFPIO**.

    ![Configurar o início de sessão único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, teste configuração do Azure AD único início de sessão utilizando o painel de acesso.

Quando clica no mosaico RFPIO no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de RFPIO.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_203.png

