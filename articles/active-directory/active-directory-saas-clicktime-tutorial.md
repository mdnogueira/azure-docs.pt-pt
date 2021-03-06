---
title: "Tutorial: Integração do Azure Active Directory com ClickTime | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e ClickTime."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: 0e0123a40d52dfd7a2e29c29cb2239e979089ca9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Tutorial: Integração do Azure Active Directory com ClickTime

Neste tutorial, irá aprender a integrar ClickTime com o Azure Active Directory (Azure AD).

Integrar ClickTime com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ClickTime
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para ClickTime (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ClickTime, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um ClickTime-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar ClickTime a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-clicktime-from-the-gallery"></a>Adicionar ClickTime a partir da Galeria
Para configurar a integração de ClickTime com o Azure AD, terá de adicionar ClickTime a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar ClickTime a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **ClickTime**, selecione **ClickTime** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![ClickTime na lista de resultados](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com ClickTime com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no ClickTime é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ClickTime tem de ser estabelecida.

No ClickTime, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com ClickTime, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste ClickTime](#create-a-clicktime-test-user)**  - para ter um homólogo de Britta Simon ClickTime que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação ClickTime.

**Para configurar o Azure AD-início de sessão único com ClickTime, execute os seguintes passos:**

1. No portal do Azure, no **ClickTime** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_samlbase.png)

3. No **ClickTime domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio ClickTime e os URLs únicos de informações de início de sessão](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_url.png)

    a. No **identificador** caixa de texto, escreva um URL como:`https://app.clicktime.com/sp/`
    
    b. No **URL de resposta** caixa de texto, escreva um URL utilizando os seguintes padrões: 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-clicktime-tutorial/tutorial_general_400.png)

6. No **ClickTime configuração** secção, clique em **configurar ClickTime** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de ClickTime](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa ClickTime como administrador.

8. Na barra de ferramentas na parte superior, clique em **preferências**e, em seguida, clique em **definições de segurança**.

9. No **as preferências de início de sessão único** configuração secção, execute os seguintes passos:
   
    ![Definições de segurança](./media/active-directory-saas-clicktime-tutorial/tic777280.png "definições de segurança")
   
    a.  Selecione **permitir** início de sessão único Sign-On (SSO) com a utilizar **do Azure AD**.
   
    b. No **ponto final de fornecedor de identidade** caixa de texto, colar **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
   
    c.  Abra o **certificado com codificação base 64** transferido a partir do portal do Azure no **bloco de notas**, copie o conteúdo e, em seguida, cole-o para o **certificado x. 509** caixa de texto.
   
    d.  Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-clicktime-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-clicktime-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.
 
    ![O botão de adição](./media/active-directory-saas-clicktime-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo caixa, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-clicktime-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-clicktime-test-user"></a>Criar um utilizador de teste ClickTime

Para permitir que os utilizadores do Azure AD sessão ClickTime, têm de ser aprovisionados para ClickTime.  
No caso de ClickTime, o aprovisionamento é uma tarefa manual.

> [!NOTE]
> Pode utilizar quaisquer outras ClickTime utilizador conta criação ferramentas ou APIs fornecidas pelo ClickTime aprovisionar contas de utilizador do Azure AD.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**
1. Inicie sessão no seu **ClickTime** inquilino.
2. Na barra de ferramentas na parte superior, clique em **empresa**e, em seguida, clique em **pessoas**.
   
    ![As pessoas](./media/active-directory-saas-clicktime-tutorial/tic777282.png "pessoas")
3. Clique em **Adicionar pessoa**.
   
    ![Adicionar pessoa](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Adicionar pessoa")
4. Na secção nova pessoa, execute os seguintes passos:
   
    ![As pessoas](./media/active-directory-saas-clicktime-tutorial/tic777284.png "pessoas")
   
    a.  No **nome completo** caixa de texto, tipo nome completo do utilizador, como **Britta Simon**. 
  
    b.  No **endereço de correio eletrónico** caixa de texto, como o tipo de mensagem de correio eletrónico do utilizador  **brittasimon@contoso.com** .
       
    > [!NOTE]
    > Se pretender, pode definir propriedades adicionais do novo objeto de pessoa.
   
    c.  Clique em **Guardar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para ClickTime.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a ClickTime, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **ClickTime**.

    ![Ligação ClickTimne na lista de aplicações](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico ClickTime no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de ClickTime.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png

