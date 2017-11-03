---
title: "Tutorial: Integração do Azure Active Directory com o Rally Software | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre Rally Software e do Azure Active Directory."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: 6481c9ef0ca71419ccfa6f7956f4702985743df3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integração do Azure Active Directory com o Rally Software

Neste tutorial, irá aprender a integrar Rally Software com o Azure Active Directory (Azure AD).

Integrar Rally Software com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Rally Software.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada ao Software Rally (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Rally Software, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Rally Software-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Rally Software a partir da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-rally-software-from-the-gallery"></a>Adicionar Rally Software a partir da galeria do
Para configurar a integração do Rally Software com o Azure AD, terá de adicionar Rally Software a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Rally Software a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Rally Software**, selecione **Rally Software** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Rally Software na lista de resultados](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com o Software Rally com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Rally Software é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Rally Software tem de ser estabelecida.

No Rally Software, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Rally Software, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de Rally Software](#create-a-rally-software-test-user)**  - para ter um homólogo de Britta Simon Rally Software que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de Rally Software.

**Para configurar o Azure AD-início de sessão único com Rally Software, execute os seguintes passos:**

1. No portal do Azure, no **Rally Software** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

3. No **Rally domínio de Software e os URLs** secção, execute os seguintes passos:

    ![Rally URLs e Software domínio único início de sessão informações](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenant-name>.rally.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de Software Rally](https://help.rallydev.com/) para obter estes valores. 
 


4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-rally-software-tutorial/tutorial_general_400.png)

6. No **Rally configuração do Software** secção, clique em **configurar Software Rally** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o ID de entidade de SAML** do **secção de referência rápida.**

    ![Rally configuração de Software](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_configure.png) 

7. Inicie sessão no seu **Rally Software** inquilino.

8. Na barra de ferramentas na parte superior, clique em **configuração**e, em seguida, selecione **subscrição**.
   
    ![Subscrição](./media/active-directory-saas-rally-software-tutorial/ic769531.png "subscrição")

9. Clique em de **ação** botão. Selecione **editar subscrição** no lado superior direito da barra de ferramentas.

10. No **subscrição** página da caixa de diálogo, execute os passos seguintes e, em seguida, clique em **guardar e fechar**:
   
    ![Autenticação](./media/active-directory-saas-rally-software-tutorial/ic769542.png "autenticação")
   
    a. Selecione **autenticação Rally ou SSO** da lista pendente de autenticação.

    b. No **URL do fornecedor de identidade** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure. 

    c. No **SSO terminar** caixa de texto, cole o valor de **Sign-Out URL**, que copiou do portal do Azure.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-rally-software-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-rally-software-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-rally-software-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-rally-software-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-rally-software-test-user"></a>Criar um utilizador de teste Rally Software

Para utilizadores do Azure AD conseguir iniciar sessão, tem de ser aprovisionadas para a aplicação de Rally Software utilizando os respetivos nomes de utilizador do Azure Active Directory.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu inquilino Rally Software.

2. Aceda a **configuração \> utilizadores**e, em seguida, clique em **+ adicionar novo**.
   
    ![Os utilizadores](./media/active-directory-saas-rally-software-tutorial/ic781039.png "utilizadores")

3. Escreva o nome na caixa de texto do novo utilizador e, em seguida, clique em **adicionar com detalhes**.

4. No **criar utilizador** secção, execute os seguintes passos:
   
    ![Criar utilizador](./media/active-directory-saas-rally-software-tutorial/ic781040.png "criar utilizador")

    a. No **nome de utilizador** caixa de texto, escreva o nome de utilizador como **Brittsimon**.
   
    b. No **endereço de correio electrónico** caixa de texto, introduza o e-mail do utilizador, como  **brittasimon@contoso.com** .

    c. No **nome próprio** texto, introduza o nome de utilizador como **Britta**.

    d. No **Apelido** texto, introduza o apelido do utilizador, como **Simon**.

    e. Clique em **guardar e fechar**.

   >[!NOTE]
   >Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de Rally Software ou APIs fornecidas pelo Rally Software aprovisionar contas de utilizador do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao Rally Software.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon Rally software, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Rally Software**.

    ![A ligação ao Rally Software na lista de aplicações](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de Rally Software no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Rally Software.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_203.png

