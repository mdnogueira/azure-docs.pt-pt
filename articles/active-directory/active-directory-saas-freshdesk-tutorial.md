---
title: "Tutorial: Integração do Azure Active Directory com FreshDesk | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e FreshDesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: f4b47e345a40b64f69ad8a4618564662b4a6c879
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integração do Azure Active Directory com FreshDesk

Neste tutorial, irá aprender a integrar FreshDesk com o Azure Active Directory (Azure AD).

Integrar FreshDesk com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao FreshDesk
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para FreshDesk (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal de gestão do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com FreshDesk, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um FreshDesk início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar FreshDesk a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-freshdesk-from-the-gallery"></a>Adicionar FreshDesk a partir da Galeria
Para configurar a integração de FreshDesk com o Azure AD, terá de adicionar FreshDesk a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar FreshDesk a partir da galeria, execute os seguintes passos:**

1. No  **[Azure Management Portal](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **FreshDesk**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. No painel de resultados, selecione **FreshDesk**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com FreshDesk com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no FreshDesk é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no FreshDesk tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no FreshDesk.

Para configurar e testar o Azure AD-início de sessão único com FreshDesk, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste FreshDesk](#creating-a-freshdesk-test-user)**  - para ter um homólogo de Britta Simon FreshDesk que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal de gestão do Azure e configurar o início de sessão único na sua aplicação FreshDesk.

**Para configurar o Azure AD-início de sessão único com FreshDesk, execute os seguintes passos:**

1. No portal de gestão do Azure, no **FreshDesk** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, como **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. No **FreshDesk domínio e os URLs** secção, introduza o **URL de início de sessão** como: `https://<tenant-name>.freshdesk.com` ou qualquer outro valor Freshdesk tem sugeridos.

    ![Configurar o início de sessão único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Tenha em atenção que isto não é o valor real. Tem de atualizar o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obter este valor.  

4. No **certificado de assinatura de SAML** secção, clique em **certificado** e, em seguida, guarde o certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. No **FreshDesk configuração** secção, clique em **configurar FreshDesk** para abrir a janela de início de sessão de configurar. Copie o único início de sessão no URL do serviço SAML e o URL de Sign-Out do **referência rápida** secção.

    ![Configurar o início de sessão único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa Freshdesk como administrador.

8. No menu na parte superior, clique em **Admin**.
   
   ![Administração](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")

9. No **definições gerais** separador, clique em **segurança**.
   
   ![Segurança](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "segurança")

10. No **segurança** secção, execute os seguintes passos:
   
    ![Início de sessão único](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "início de sessão único")
   
    a. Para **único início de sessão (SSO)**, selecione **no**.

    b. Selecione **SAML SSO**.

    c. Tipo de **único início de sessão no URL do serviço SAML** copiou do portal do Azure para o **URL de início de sessão SAML** caixa de texto.

    d. Tipo de **Sign-Out URL** copiou do portal do Azure para o **URL de fim de sessão** caixa de texto.

    e. Copiar o **Thumbprint** valor do certificado transferido do portal do Azure e cole-o para o **impressão digital do certificado de segurança** caixa de texto.  
 
    >[!TIP]
    >Para obter mais detalhes, consulte [como obter o valor de thumbprint do certificado](http://youtu.be/YKQF266SAxI). 
    
    f. Clique em **Guardar**.


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal de gestão do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-freshdesk-test-user"></a>Criar um utilizador de teste FreshDesk

Para permitir que os utilizadores do Azure AD sessão FreshDesk, têm de ser aprovisionados para FreshDesk.  
No caso de FreshDesk, o aprovisionamento é uma tarefa manual.

**Aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Freshdesk** inquilino.
2. No menu na parte superior, clique em **Admin**.
   
   ![Administração](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")

3. No **definições gerais** separador, clique em **agentes**.
   
   ![Agentes](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "agentes")

4. Clique em **novo agente**.
   
    ![Novo agente](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "novo agente")

5. Na caixa de diálogo informações do agente, execute os seguintes passos:
   
   ![Informações de agentes](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "informações de agentes")
   
   a. No **nome completo** caixa de texto, escreva o nome da conta do Azure AD que pretende aprovisionar.

   b. No **E-Mail** caixa de texto, o Azure AD de tipo de endereço de correio eletrónico da conta do Azure AD que pretende aprovisionar.

   c. No **título** caixa de texto, escreva o título da conta do Azure AD que pretende aprovisionar.

   d. Selecione **função agentes**e, em seguida, clique em **atribuir**.
       
   e. Clique em **Guardar**.     
   
    >[!NOTE]
    >Titular da conta do Azure AD irá receber uma mensagem de e-mail que inclui uma ligação para confirmar a conta antes de ser ativado. 
    > 
    
    >[!NOTE]
    >Pode utilizar quaisquer outras Freshdesk utilizador conta criação ferramentas ou APIs fornecidas pelo Freshdesk para aprovisionar contas de utilizador do AAD. para FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo o respetivo acesso à caixa.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a FreshDesk, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **FreshDesk**.

    ![Configurar o início de sessão único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico FreshDesk no painel de acesso, deve obter a página de início de sessão para obter com sessão iniciada para a aplicação de FreshDesk.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png

