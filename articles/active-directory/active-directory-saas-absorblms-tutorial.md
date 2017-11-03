---
title: "Tutorial: Integração do Azure Active Directory com absorver LMS | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e absorver LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 549e353bb41f1f31bc0441294d0a8066779402e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integração do Azure Active Directory com absorver LMS

Neste tutorial, irá aprender a integrar absorver LMS com o Azure Active Directory (Azure AD).

Integrar absorver LMS com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao absorver LMS.
- Pode permitir aos utilizadores iniciar sessão automaticamente no absorver LMS (através de início de sessão) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central, o portal do Azure.

Se pretender saber mais sobre sofware como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com absorver LMS, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um absorver LMS-início de sessão único ativada subscrição

> [!NOTE]
> Recomendamos que não a utilizar um ambiente de produção para este tutorial.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

* Adicionar absorver LMS na galeria do
* Configurar e testar o Azure AD de sessão único-

## <a name="add-absorb-lms-from-the-gallery"></a>Adicionar absorver LMS a partir da Galeria
Para configurar a integração de absorver LMS com o Azure AD, adicione absorver LMS na Galeria à sua lista de aplicações SaaS geridas.

Para adicionar absorver LMS a partir da galeria, efetue o seguinte:

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Aceda a **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar uma aplicação, selecione o **nova aplicação** botão.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **absorver LMS**, selecione **absorver LMS** no resultar painel e, em seguida, selecione o **adicionar** botão.

    ![Absorver LMS na lista de resultados](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com LMS absorver com base no chamado Britta Simon um utilizador de teste.

Para início de sessão trabalhar, do Azure AD tem de saber o que é o utilizador de homólogo absorver LMS no Azure AD. Por outras palavras, tem de estabelecer uma relação de ligação entre um utilizador no Azure AD e o utilizador correspondente no absorver LMS.

Estabelecer esta relação de ligação ao atribuir o *nome de utilizador* valor no Azure AD como o *Username* valor absorver LMS.

Para configurar e testar o Azure AD-início de sessão único com absorver LMS, conclua os blocos modulares nas secções junto cinco.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação absorver LMS.

Para configurar o Azure AD-início de sessão único com absorver LMS, efetue o seguinte:

1. No portal do Azure, no **absorver LMS** página de integração de aplicações, selecione **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo a **modo** caixa, selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. No **URLs e de domínio de LMS absorver** secção, efetue o seguinte:

    ![Absorver LMS domínio e os URLs único início de sessão informações](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. No **identificador** caixa, escreva um URL que utiliza a seguinte sintaxe: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. No **URL de resposta** caixa, escreva um URL que utiliza a seguinte sintaxe: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Estes URLs não são os valores reais. Atualize-as com o identificador e os URLs de resposta real. Para obter estes valores, contacte o [equipa de suporte de cliente absorver LMS](https://www.absorblms.com/support). 

4. No **certificado de assinatura de SAML** na secção de **transferir** coluna, selecione **XML de metadados**e, em seguida, guarde o ficheiro de metadados para o seu computador.

    ![A hiperligação de transferência assinatura do certificado](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Selecione **Guardar**.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. No **absorver configuração de LMS** secção, selecione **configurar LMS absorver** para abrir **configurar início de sessão** janela e, em seguida, copie o **Sign-Out URL** no **secção de referência rápida.**

    ![O painel de configuração de LMS absorver](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. Numa nova janela do browser web, inicie sessão no site da sua empresa absorver LMS como administrador.

8. Selecione o **conta** botão no canto superior direito. 

    ![O botão de conta](./media/active-directory-saas-absorblms-tutorial/1.png)

9. No painel de conta, selecione **as definições do Portal**.

    ![A ligação de definições do Portal](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. Selecione o separador **Utilizadores**.

    ![O separador de utilizadores](./media/active-directory-saas-absorblms-tutorial/3.png)

11. Na página de configuração de início de sessão único, efetue o seguinte:

    ![A página de configuração de início de sessão único](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. No **modo** caixa, selecione **iniciada do fornecedor de identidade**.

    b. No bloco de notas, abra o certificado que transferiu a partir do portal do Azure. Remova o **---BEGIN CERTIFICATE---** e **---fim certificado---** etiquetas. Em seguida, no **chave** caixa, cole os conteúdos restantes.
    
    c. No **propriedade Id** caixa, selecione o atributo que configurou como o identificador de utilizador no Azure AD. Por exemplo, se *userPrincipalName* está selecionado no Azure AD, selecione **Username**.

    d. No **URL de início de sessão** caixa, cole o **URL de acesso de utilizador** da aplicação do **propriedades** página do portal do Azure.

    e. No **URL de fim de sessão**, cole o **Sign-Out URL** valor que copiou do **configurar início de sessão** janela do portal do Azure.

12. Ativar/desativar **permitir apenas o início de sessão do SSO** para **no**.

    ![O botão de alternar apenas permitir SSO início de sessão](./media/active-directory-saas-absorblms-tutorial/5.png)

13. Selecione **guardar.**

> [!TIP]
> Pode ler uma versão concisa destas instruções no [portal do Azure](https://portal.azure.com) enquanto estiver a configurar a aplicação. Depois de adicionar a aplicação a partir de **do Active Directory** > **aplicações empresariais** secção, selecione o **Single Sign-On** separador e aceder a incorporados documentação através de **configuração** secção na parte inferior. Para obter mais informações, consulte [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, vai criar o utilizador de teste Britta Simon no portal do Azure.

![Criar um utilizador de teste do Azure AD][100]

Para criar um utilizador de teste no Azure AD, efetue o seguinte:

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**.

    ![O botão do Azure Active Directory](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, selecione **utilizadores e grupos** > **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, selecione **adicionar**.
 
    ![O botão de adição](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo caixa, efetue o seguinte:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, escreva o endereço de e-mail do Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor no **palavra-passe** caixa.

    d. Selecione **Criar**.

### <a name="create-an-absorb-lms-test-user"></a>Criar um utilizador de teste absorver LMS

Para utilizadores do Azure AD iniciar sessão no absorver LMS, deve definir cópias de segurança no absorver LMS.  

Para absorver LMS, a configuração está numa tarefa manual.

Para configurar uma conta de utilizador, efetue o seguinte:

1. Inicie sessão no site da sua empresa absorver LMS como administrador.

2. No painel esquerdo, selecione **utilizadores**.

    ![A ligação de utilizadores de LMS absorver](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. No **utilizadores** painel, selecione **utilizadores**.

    ![A ligação de utilizadores](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. No **adicionar novo** na lista pendente, selecione **utilizador**.

    ![A adicionar novo na lista pendente](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. No **adicionar utilizador** página, efetue o seguinte:

    ![A página Adicionar utilizador](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. No **nome próprio** caixa, escreva o nome próprio, como **Britta**.

    b. No **Apelido** caixa, digite o apelido, tal como **Simon**.
    
    c. No **Username** caixa, digite um nome completo, tal como **Britta Simon**.

    d. No **palavra-passe** caixa, escreva a palavra-passe de Britta Simon.

    e. No **Confirmar palavra-passe** caixa, escreva novamente a palavra-passe.
    
    f. Definir o **está ativa** alternar para **Active Directory**.  

6. Selecione **guardar.**
 
### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar utilizador Britta Simon para utilizar o Azure-início de sessão único, concedendo acesso para absorver LMS.

![Atribuir a função de utilizador][200]

Para atribuir o utilizador Britta Simon a absorver LMS, efetue o seguinte:

1. No portal do Azure, abra a vista de aplicações, aceda à vista de diretório e, em seguida, selecione **aplicações empresariais** > **todas as aplicações**.

    ![A ligação de "Todas as aplicações"][201] 

2. No **aplicações** lista, selecione **absorver LMS**.

    ![A ligação de absorver LMS na lista de aplicações](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. No painel esquerdo, selecione **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Selecione **adicionar** e, em seguida, no **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo a **utilizadores** lista, selecione **Britta Simon**.

6. No **utilizadores e grupos** caixa de diálogo, selecione o **selecione** botão.

7. No **adicionar atribuição** caixa de diálogo, selecione o **atribuir** botão.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, teste configuração do Azure AD único início de sessão utilizando o painel de acesso.

No painel de acesso, selecionando o **absorver LMS** mosaico automaticamente inicia a sessão à sua aplicação absorver LMS. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png

