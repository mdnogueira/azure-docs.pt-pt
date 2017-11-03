---
title: "Tutorial: Integração do Azure Active Directory com iQualify LMS | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e iQualify LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: cab1ce3694372c137667e0179caf8d3523147f7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Tutorial: Integração do Azure Active Directory com iQualify LMS

Neste tutorial, irá aprender a integrar iQualify LMS com o Azure Active Directory (Azure AD).

Integrar iQualify LMS com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao iQualify LMS.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para iQualify LMS (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com iQualify LMS, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um iQualify LMS-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar iQualify LMS a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-iqualify-lms-from-the-gallery"></a>Adicionar iQualify LMS a partir da Galeria
Para configurar a integração de iQualify LMS com o Azure AD, terá de adicionar iQualify LMS a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar iQualify LMS a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **iQualify LMS**, selecione **iQualify LMS** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![iQualify LMS na lista de resultados](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configurar e testar o Azure AD-início de sessão único com iQualify que LMs com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber que o utilizador de homólogo iQualify LMS for para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no iQualify LMS tem de ser estabelecida.

No iQualify LMS, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com iQualify LMS, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste LMS iQualify](#create-an-iqualify-lms-test-user)**  - para ter um homólogo de Britta Simon no iQualify LMS que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de LMS iQualify.

**Para configurar o Azure AD-início de sessão único com iQualify LMS, execute os seguintes passos:**

1. No portal do Azure, no **iQualify LMS** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. No **iQualify LMS domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no modo de IDP iniciado:

    ![informações de iQualify LMS domínio e os URLs-início de sessão único](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte: 
    | |
    |--|--|
    | Ambiente de produção:`https://<yourorg>.iqualify.com/`|
    | Ambiente de teste:`https://<yourorg>.iqualify.io`|
    
    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte: 
    | |
    |--|--|
    | Ambiente de produção:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Ambiente de teste:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![informações de iQualify LMS domínio e os URLs-início de sessão único](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url1.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:
    | |
    |--|--|
    | Ambiente de produção:`https://<yourorg>.iqualify.com/login` |
    | Ambiente de teste:`https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [equipa de suporte de cliente LMS iQualify](https://www.iqualify.com) para obter estes valores. 

5. A aplicação de LMS iQualify espera as asserções de Security Assertion Markup Language (SAML) para ser apresentado num formato específico. Configurar as afirmações e gerir os valores dos atributos no **atributos de utilizador** secção iQualify página da aplicação integração, conforme mostrado na captura de ecrã seguinte:
    
    ![Configurar o início de sessão único](./media/active-directory-saas-iqualify-tutorial/atb.png)

6. No **atributos de utilizador** secção no **de sessão único-** diálogo execute os seguintes passos para cada linha mostrada na tabela abaixo:
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | Correio eletrónico | User.userPrincipalName |
    | first_name | User.givenName |
    | last_name | User.Surname |
    | person_id | "o atributo" | 

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-iqualify-tutorial/atb2.png)

    ![Configurar o início de sessão único](./media/active-directory-saas-iqualify-tutorial/atb3.png)
    
    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**

    e. Repita os passos "a" através de "d" para as linhas da tabela seguintes. 

    > [!Note]
    > Repetir os passos "a" através de "d" para o **person_id** atributo **opcional**

7. No **certificado de assinatura de SAML** secção, clique em **certificado (Base 64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-iqualify-tutorial/tutorial_general_400.png)
    
9. No **iQualify LMS configuração** secção, clique em **configurar iQualify LMS** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![iQualify LMS configuração](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Abra uma nova janela do browser e, em seguida, inicie sessão no seu ambiente iQualify como administrador.

11. Depois de iniciarem sessão, clique no seu avatar no canto superior direito, em seguida, clique em **"Definições da conta".**

    ![Definições da conta](./media/active-directory-saas-iqualify-tutorial/setting1.png) 
12. Na área de definições de conta, clique no menu do friso à esquerda e clique em **"INTEGRAÇÕES."**
    
    ![INTEGRAÇÕES](./media/active-directory-saas-iqualify-tutorial/setting2.png)

13. Em INTEGRAÇÕES, clique em de **SAML** ícone.

    ![Ícone SAML](./media/active-directory-saas-iqualify-tutorial/setting3.png)

14. No **definições de autenticação SAML** diálogo caixa, execute os seguintes passos:

    ![Definições de autenticação SAML](./media/active-directory-saas-iqualify-tutorial/setting4.png)

    a. No **URL do serviço no início de sessão único SAML** caixa, cole o **URL do serviço Sign‑On único SAML** valor copiados a partir da janela de configuração de aplicação do Azure AD.
    
    b. No **URL de fim de sessão SAML** caixa, cole o **Sign‑Out URL** valor copiados a partir da janela de configuração de aplicação do Azure AD.
    
    c. Abra o ficheiro de certificado transferido no bloco de notas, copie o conteúdo e, em seguida, cole-a no **certificado público** caixa.
    
    d. No **etiqueta de botão de início de sessão** introduza o nome para o botão para ser apresentada na página de início de sessão.
    
    e. Clique em **GUARDAR**.

    f. Clique em **ATUALIZAÇÃO**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-iqualify-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-iqualify-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-iqualify-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-iqualify-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Criar um utilizador de teste LMS iQualify

Nesta secção, é criado um utilizador chamado Britta Simon iQualify. iQualify LMS suporta just‑in‑time aprovisionamento de utilizadores, que está ativada por predefinição.

Não há nenhum item de ação para si nesta secção. Se um utilizador já não existe na iQualify, uma nova é criada quando tentar aceder iQualify LMS.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso aos iQualify LMS.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a iQualify LMS, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **iQualify LMS**.

    ![A ligação LMS iQualify na lista de aplicações](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Ao clicar iQualify LMS na peça de mosaico do painel de acesso, deve obter a página de início de sessão da sua aplicação de LMS iQualify. 

   ![página de início de sessão](./media/active-directory-saas-iqualify-tutorial/login.png) 

Clique em **iniciar sessão no Azure AD** botão e, deve obter automaticamente com sessão iniciada para a aplicação de LMS iQualify.

Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_203.png

