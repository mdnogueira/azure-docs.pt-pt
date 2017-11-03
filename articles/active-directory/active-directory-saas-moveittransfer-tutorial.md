---
title: "Tutorial: Integração do Azure Active Directory com MOVEit transferência - a integração do Azure AD | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e transferência MOVEit - a integração do Azure AD."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: d35aceb9be2d0ff49f86a00cc84f5deb198d88f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Integração do Azure Active Directory com MOVEit transferência - a integração do Azure AD

Neste tutorial, irá aprender a integrar MOVEit transferência - integração do Azure AD com o Azure Active Directory (Azure AD).

Integrar MOVEit transferência - integração do Azure AD com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à transferência MOVEit - a integração do Azure AD.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para transferência MOVEit - integração do Azure AD (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com MOVEit transferência - integração do AD do Azure, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Transferência de MOVEit - do Azure AD integração início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar MOVEit transferência - integração do Azure AD da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Adicionar MOVEit transferência - integração do Azure AD da galeria do
Para configurar a integração da transferência MOVEit - integração do Azure AD com o Azure AD, terá de adicionar MOVEit transferência - integração do AD do Azure na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar MOVEit transferência - integração do Azure AD da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **MOVEit transferência - a integração do Azure AD**, selecione **MOVEit transferência - a integração do Azure AD** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Transferência de MOVEit - integração do Azure AD na lista de resultados](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com MOVEit transferência - integração do Azure AD com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na transferência MOVEit - integração do AD do Azure é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na transferência MOVEit - a integração do Azure AD tem de ser estabelecida.

Na transferência MOVEit - integração do AD do Azure, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com MOVEit transferência - integração do Azure AD, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar uma transferência MOVEit - utilizador de teste de integração do Azure AD](#create-a-moveit-transfer---azure-ad-integration-test-user)**  - para ter um homólogo de Britta Simon MOVEit transferência - integração do Azure AD que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua transferência MOVEit - a aplicação de integração do Azure AD.

**Para configurar o Azure AD-início de sessão único com MOVEit transferência - integração do Azure AD, execute os seguintes passos:**

1. No portal do Azure, no **MOVEit transferência - a integração do Azure AD** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

3. No **MOVEit transferência - URLs e integração com o Azure AD domínio** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://contoso.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://contoso.com/<tenatid>`

    c. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Pode consultar estes valores mais tarde em **URL de metadados do fornecedor de serviço** secção ou contacte [MOVEit transferência - a equipa de suporte de cliente de integração do Azure AD](https://community.ipswitch.com/s/support) para obter estes valores.

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_400.png)
    
6. Inicie sessão seu inquilino de transferência MOVEit como administrador.

7. No painel de navegação esquerdo, clique em **definições**.

    ![Lado de secção na aplicação de definições](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

8. Clique em **Signon único** ligação, que está sob **políticas de segurança -> utilizador Auth**.

    ![Lado de políticas em aplicações de segurança](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

9. Clique na ligação do URL de metadados para transferir o documento de metadados.

    ![URL de metadados do fornecedor de serviço](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Certifique-se **entityID** corresponde **identificador** no **MOVEit transferência - URLs e integração com o Azure AD domínio** secção.
    * Certifique-se **AssertionConsumerService** corresponde ao URL de localização **URL de resposta** no **MOVEit transferência - URLs e integração com o Azure AD domínio** secção.
    
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

10. Clique em **Adicionar fornecedor de identidade** botão para adicionar um novo fornecedor de identidade federada.

    ![Adicione o fornecedor de identidade](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

11. Clique em **procurar...**  para selecionar o ficheiro de metadados que transferiu a partir do portal do Azure, em seguida, clique em **Adicionar fornecedor de identidade** para carregar o ficheiro transferido.

    ![Fornecedor de identidade](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

12. Selecione "**Sim**" como **ativada** no **Federado de editar as definições do fornecedor de identidade...**  página e clique em **guardar**.

    ![Definições do fornecedor de identidade federada](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

13. No **editar federado identidade do utilizador as definições do fornecedor** página, execute as seguintes ações:
    
    ![Editar as definições do fornecedor de identidade federada](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selecione **SAML NameID** como **nome de início de sessão**.
    
    b. Selecione **outros** como **nome completo** e no **nome de atributo** textbox colocar o valor: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selecione **outros** como **E-Mail** e no **nome de atributo** textbox colocar o valor: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selecione **Sim** como **Auto-criar conta no signon**.
    
    e. Clique em **guardar** botão.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Criar uma transferência MOVEit - utilizador de teste de integração do Azure AD

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon na transferência MOVEit - a integração do Azure AD. Transferência de MOVEit - integração do AD do Azure suporta just-in-time o aprovisionamento, que tiver ativado. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante a tentativa de aceder a transferência de MOVEit - se de que não existe ainda a integração do AD do Azure.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar o [MOVEit transferência - a equipa de suporte de cliente de integração do Azure AD](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à transferência MOVEit - a integração do Azure AD.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a transferência de MOVEit - integração do Azure AD, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **MOVEit transferência - a integração do Azure AD**.

    ![A transferência de MOVEit - a integração do Azure AD ligação na lista de aplicações](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Ao clicar em transferência MOVEit - a integração do Azure AD na peça de mosaico do painel de acesso, deve obter automaticamente com sessão iniciada para a transferência de MOVEit - a aplicação de integração do Azure AD. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png

