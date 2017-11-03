---
title: "Tutorial: Integração do Azure Active Directory com Work.com | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Work.com."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 7cfec8e9ac12d43095483696a15c0580776d3114
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integração do Azure Active Directory com Work.com

Neste tutorial, irá aprender a integrar Work.com com o Azure Active Directory (Azure AD).

Integrar Work.com com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Work.com
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Work.com (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Work.com, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Work.com-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Work.com a partir da Galeria
2. Configurar e testar o Azure AD-início de sessão único

## <a name="add-workcom-from-the-gallery"></a>Adicionar Work.com a partir da Galeria
Para configurar a integração de Work.com com o Azure AD, terá de adicionar Work.com a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Work.com a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Work.com**, selecione **Work.com** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Adicionar a partir da Galeria](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com Work.com com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Work.com é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Work.com tem de ser estabelecida.

No Work.com, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Work.com, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Work.com](#create-a-workcom-test-user)**  - para ter um homólogo de Britta Simon Work.com que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Work.com.

>[!NOTE]
>Para configurar o início de sessão único, terá de configurar um nome de domínio personalizado Work.com ainda. Tem de definir, pelo menos, um nome de domínio, testar o seu nome de domínio e implementá-la em toda a organização.

**Para configurar o Azure AD-início de sessão único com Work.com, execute os seguintes passos:**

1. No portal do Azure, no **Work.com** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Baseados em SAML início de sessão](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_samlbase.png)

3. No **Work.com domínio e os URLs** secção, efetue o seguinte:

    ![Secção Work.com domínio e os URLs](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) para obter este valor. 

4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Secção de certificado de assinatura de SAML](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_certificate.png) 

5. Clique em **guardar** botão.

    ![Botão Guardar](./media/active-directory-saas-work-com-tutorial/tutorial_general_400.png)

6. No **Work.com configuração** secção, clique em **configurar Work.com** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Secção de configuração Work.com](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_configure.png) 
7. Inicie sessão no seu inquilino Work.com como administrador.

8. Aceda a **configuração**.
   
    ![A configuração](./media/active-directory-saas-work-com-tutorial/ic794108.png "programa de configuração")

9. No painel de navegação esquerdo, no **administrar** secção, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **Meu domínio** página. 
   
    ![O meu domínio](./media/active-directory-saas-work-com-tutorial/ic767825.png "meu domínio")

10. Para verificar que o domínio tiver sido configurado corretamente, certifique-se de que está a ser "**passo 4 implementada para os utilizadores**" e rever o "**My definições de domínio**".
   
    ![Domínio implementado no utilizador](./media/active-directory-saas-work-com-tutorial/ic784377.png "implementado no utilizador de domínio")

11. Inicie sessão no seu inquilino Work.com.

12. Aceda a **configuração**.
    
    ![A configuração](./media/active-directory-saas-work-com-tutorial/ic794108.png "programa de configuração")

13. Expanda o **controlos de segurança** e, em seguida, clique em **as definições de início de sessão único**.
    
    ![Single Sign-On definições](./media/active-directory-saas-work-com-tutorial/ic794113.png "único as definições de início de sessão")

14. No **as definições de início de sessão único** diálogo página, execute os seguintes passos:
    
    ![SAML ativada](./media/active-directory-saas-work-com-tutorial/ic781026.png "SAML ativada")
    
    a. Selecione **SAML ativada**.
    
    b. Clique em **Novo**.

15. No **SAML único início de sessão definições** secção, execute os seguintes passos:
    
    ![SAML único início de sessão definição](./media/active-directory-saas-work-com-tutorial/ic794114.png "SAML único início de sessão na definição")
    
    a. No **nome** caixa de texto, escreva um nome para a sua configuração.  
       
    > [!NOTE]
    > Fornecer um valor para **nome** preencher automaticamente o **nome API** caixa de texto.
    
    b. No **emissor** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.
    
    c. Para carregar o certificado transferido a partir do portal do Azure, clique em **procurar**.
    
    d. No **Id de entidade** caixa de texto, tipo `https://salesforce-work.com`.
    
    e. Como **tipo de identidade de SAML**, selecione **asserção contém o ID de Federação do objeto User**.
    
    f. Como **SAML identidade localização**, selecione **é de identidade no elemento NameIdentfier da declaração de assunto**.
    
    g. No **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.

    h. No **URL de fim de sessão do fornecedor de identidade** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure.
    
    posso. Como **fornecedor iniciada pedido vínculo de serviço**, selecione **HTTP Post**.
    
    j. Clique em **Guardar**.

16. No portal clássico do Work.com, no painel de navegação esquerdo, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **meu domínio** página. 
    
    ![O meu domínio](./media/active-directory-saas-work-com-tutorial/ic794115.png "meu domínio")

17. No **meu domínio** na página de **imagem corporativa página de início de sessão** secção, clique em **editar**.
    
    ![Página de início de sessão de imagem corporativa](./media/active-directory-saas-work-com-tutorial/ic767826.png "página de início de sessão de imagem corporativa")

14. No **imagem corporativa página de início de sessão** na página a **serviço de autenticação** secção, o nome do seu **SAML SSO definições** é apresentado. Selecionar e, em seguida, clique em **guardar**.
    
    ![Página de início de sessão de imagem corporativa](./media/active-directory-saas-work-com-tutorial/ic784366.png "página de início de sessão de imagem corporativa")

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-work-com-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Utilizadores e grupos -> todos os utilizadores](./media/active-directory-saas-work-com-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Adicionar](./media/active-directory-saas-work-com-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Página de caixa de diálogo de utilizador](./media/active-directory-saas-work-com-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-workcom-test-user"></a>Criar um utilizador de teste Work.com
Para utilizadores do Azure Active Directory conseguir iniciar sessão, tem de ser aprovisionados para Work.com. No caso de Work.com, o aprovisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:
1. Inicie sessão site da sua empresa Work.com como administrador.

2. Aceda a **configuração**.
   
    ![A configuração](./media/active-directory-saas-work-com-tutorial/IC794108.png "programa de configuração")
3. Aceda a **gerir utilizadores \> utilizadores**.
   
    ![Gerir utilizadores](./media/active-directory-saas-work-com-tutorial/IC784369.png "gerir utilizadores")

4. Clique em **novo utilizador**.
   
    ![Todos os utilizadores](./media/active-directory-saas-work-com-tutorial/IC794117.png "todos os utilizadores")

5. Na secção de utilizador editar, execute os seguintes passos, nos atributos de um Azure válido conta do AD que pretende aprovisionar para caixas de texto relacionadas:
   
    ![Edição do utilizador](./media/active-directory-saas-work-com-tutorial/ic794118.png "edição do utilizador")
   
    a. No **nome próprio** caixa de texto, tipo de **nome próprio** do utilizador **Britta**.
    
    b. No **Apelido** caixa de texto, tipo de **Apelido** do utilizador **Simon**.
    
    c. No **Alias** caixa de texto, tipo de **nome** do utilizador **BrittaS**.
    
    d. No **E-Mail** caixa de texto, tipo de **endereço de correio eletrónico** do utilizador  **Brittasimon@contoso.com** .
    
    e. No **nome de utilizador** caixa de texto, escreva um nome de utilizador utilizador gostar  **Brittasimon@contoso.com** .
    
    f. No **Nick nome** caixa de texto, escreva um **nick nome** do utilizador **Simon**.
    
    g. Selecione **função**, **licença de utilizador**, e **perfil**.
    
    h. Clique em **Guardar**.  
      
    > [!NOTE]
    > Titular da conta do Azure AD irá receber uma mensagem de e-mail, incluindo uma ligação para confirmar a conta para ficar ativa.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Work.com.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Work.com, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Work.com**.

    ![Work.com na lista da aplicação](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Work.com no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Work.com.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_203.png

