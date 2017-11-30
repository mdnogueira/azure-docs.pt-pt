---
title: "Tutorial: Integração do Azure Active Directory com FileCloud | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e FileCloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2263e583-3eb2-4a06-982d-33f5f54858f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: jeedes
ms.openlocfilehash: f1f16865a3adb55fc63c09c1dbdc9720b02f9c33
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Tutorial: Integração do Azure Active Directory com FileCloud

Neste tutorial, irá aprender a integrar FileCloud com o Azure Active Directory (Azure AD).

Integrar FileCloud com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao FileCloud.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para FileCloud (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com FileCloud, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um FileCloud-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar FileCloud a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-filecloud-from-the-gallery"></a>Adicionar FileCloud a partir da Galeria
Para configurar a integração de FileCloud com o Azure AD, terá de adicionar FileCloud a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar FileCloud a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **FileCloud**, selecione **FileCloud** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![FileCloud na lista de resultados](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com FileCloud com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no FileCloud é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no FileCloud tem de ser estabelecida.

No FileCloud, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com FileCloud, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste FileCloud](#create-a-filecloud-test-user)**  - para ter um homólogo de Britta Simon FileCloud que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação FileCloud.

**Para configurar o Azure AD-início de sessão único com FileCloud, execute os seguintes passos:**

1. No portal do Azure, no **FileCloud** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_samlbase.png)

3. No **FileCloud domínio e os URLs** secção, execute os seguintes passos:

    ![Domínio FileCloud e os URLs únicos de informações de início de sessão](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.filecloudonline.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<subdomain>.filecloudonline.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente FileCloud](mailto:support@codelathe.com) para obter estes valores. 

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-filecloud-tutorial/tutorial_general_400.png)

6. No **FileCloud configuração** secção, clique em **configurar FileCloud** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML** do **secção de referência rápida.**

    ![Configuração de FileCloud](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_configure.png) 

7. Numa janela do browser web diferente, início de sessão no seu inquilino FileCloud como administrador.

8. No painel de navegação esquerdo, clique em **definições**. 
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_000.png)

9. Clique em **SSO** separador na secção de definições. 
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_001.png)

10. Selecione **SAML** como **tipo predefinido de SSO** no **único início de sessão (SSO) definições** painel.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_002.png)

11. No **URL de ponto final do IdP** caixa de texto, colar o valor de **ID de entidade de SAML** que copiou do portal do Azure.

    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_003.png)

12. Abra o ficheiro de metadados transferido no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **IdP Meta dados** caixa de texto no **SAML definições** painel.

    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_004.png)

13. Clique em **guardar** botão.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-filecloud-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-filecloud-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-filecloud-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-filecloud-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-filecloud-test-user"></a>Criar um utilizador de teste FileCloud

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon FileCloud. FileCloud suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante a tentativa de aceder FileCloud se não existir ainda.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar o [equipa de suporte de cliente FileCloud](mailto:support@codelathe.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para FileCloud.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a FileCloud, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **FileCloud**.

    ![A ligação de FileCloud na lista de aplicações](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico FileCloud no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de FileCloud.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_203.png

