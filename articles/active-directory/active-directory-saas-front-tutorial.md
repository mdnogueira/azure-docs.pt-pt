---
title: "Tutorial: Integração do Azure Active Directory com início | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e frente."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: d936bc50a66ac2a3c17038ff08351edf9902c99f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Tutorial: Integração do Azure Active Directory com início

Neste tutorial, irá aprender a integrar frente com o Azure Active Directory (Azure AD).

Integrar frente com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à frente.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a frente (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com frente, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um frente-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de frente de galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-front-from-the-gallery"></a>A adição de frente de galeria
Para configurar a integração da frente com o Azure AD, tem de adicionar frente da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar frente da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **front-**, selecione **front-** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Início da lista de resultados](./media/active-directory-saas-front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com início num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo à frente é um utilizador no Azure AD. Por outras palavras, uma relação entre um utilizador do Azure AD e o utilizador relacionado à frente de ligação tem de ser estabelecida.

À frente, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o início, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de frente](#create-a-front-test-user)**  - para ter um homólogo de Britta Simon à frente que está ligado a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de início.

**Para configurar o Azure AD-início de sessão único com o início, execute os seguintes passos:**

1. No portal do Azure, no **front-** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-front-tutorial/tutorial_front_samlbase.png)

3. No **URLs e de domínio de front-** secção, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-front-tutorial/tutorial_front_url1.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.frontapp.com`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.frontapp.com/sso/saml/callback`

4. Verifique **Mostrar avançadas definições de URL**, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Configurar o início de sessão único](./media/active-directory-saas-front-tutorial/tutorial_front_url2.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.frontapp.com`
     
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualizar estes valores com o identificador real, URL de resposta e URL de início de sessão que serão explicados de forma mais tarde no tutorial ou contacte [equipa de suporte de front-cliente](mailto:support@frontapp.com) para obter estes valores. 

5. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-front-tutorial/tutorial_front_certificate.png) 

6. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-front-tutorial/tutorial_general_400.png)
    
7. No **front-configuração** secção, clique em **configurar front-** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-front-tutorial/tutorial_front_configure.png) 

8. Início de sessão no seu inquilino de frente como administrador.

9. Aceda a **definições (ícone roda dentada na parte inferior esquerda barra lateral) > Preferências**.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

10. Clique em **início de sessão único** ligação.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

11. Selecione **SAML** na lista pendente de **início de sessão único**.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

12. No **ponto de entrada** textbox colocar o valor de **URL Single Sign-on serviço** do Assistente de configuração de aplicações do Azure AD.
    
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

13. Abra o transferido **Certificate(Base64)** ficheiro no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado de assinatura** caixa de texto.
    
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

14. No **as definições do fornecedor de serviço** secção, execute os seguintes passos:

    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

    a. Copie o valor da **ID de entidade** e cole-o para o **identificador** textbox em **URLs e de domínio de front-** secção no portal do Azure.

    b. Copie o valor da **ACS URL** e cole-o para o **URL de início de sessão** textbox em **URLs e de domínio de front-** secção no portal do Azure.
    
15. Clique em **guardar** botão.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-front-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-front-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-front-test-user"></a>Criar um utilizador de teste de início

Nesta secção, vai criar um utilizador chamado Britta Simon à frente. Trabalhar com [equipa de suporte de front-cliente](mailto:support@frontapp.com) para adicionar os utilizadores na plataforma frente. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para a frente.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a frente, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **front-**.

    ![A ligação de frente na lista de aplicações](./media/active-directory-saas-front-tutorial/tutorial_front_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a sua SSOconfiguration do AD do Azure através do painel de acesso.

Quando clica no mosaico de frente no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de início. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-front-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png

