---
title: "Tutorial: Integração do Azure Active Directory com Thoughtworks Mingle | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Thoughtworks Mingle."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 268ae5affb88a718f68c08daa94fe7aba4a99c11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: Integração do Azure Active Directory com Thoughtworks Mingle

Neste tutorial, irá aprender a integrar Thoughtworks Mingle com o Azure Active Directory (Azure AD).

Integrar o Thoughtworks Mingle com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Thoughtworks Mingle
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Thoughtworks Mingle (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Thoughtworks Mingle, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Thoughtworks Mingle-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Thoughtworks Mingle a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Adicionar Thoughtworks Mingle a partir da Galeria
Para configurar a integração de Thoughtworks Mingle com o Azure AD, tem de adicionar Thoughtworks Mingle na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Thoughtworks Mingle na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Thoughtworks Mingle**, selecione **Thoughtworks Mingle** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Thoughtworks Mingle na lista de resultados](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único
Nesta secção, configure e teste do Azure AD-início de sessão único com Thoughtworks Mingle com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo Thoughtworks Mingle é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Thoughtworks Mingle tem de ser estabelecida.

No Thoughtworks Mingle, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Thoughtworks Mingle, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Thoughtworks Mingle](#create-a-thoughtworks-mingle-test-user)**  - para ter um homólogo de Britta Simon Thoughtworks Mingle que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Thoughtworks Mingle.

**Para configurar o Azure AD-início de sessão único com Thoughtworks Mingle, execute os seguintes passos:**

1. No portal do Azure, no **Thoughtworks Mingle** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

3. No **Thoughtworks Mingle domínio e os URLs** secção, execute os seguintes passos:

    ![Thoughtworks Mingle domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de Mingle Thoughtworks](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) para obter o valor. 
 
4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_400.png)

6. Inicie sessão no seu **Thoughtworks Mingle** site da empresa como administrador.

7. Clique em de **Admin** separador e, em seguida, clique em **SSO configuração**.
   
    ![Separador administrador](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785157.png "SSO configuração")

8. No **SSO configuração** secção, execute os seguintes passos:
   
    ![Configuração SSO](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785158.png "SSO configuração")
    
    a. Para carregar o ficheiro de metadados, clique em **Escolher ficheiro**. 

    b. Clique em **guardar alterações**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![O botão de adição](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Criar um utilizador de teste Thoughtworks Mingle

Para utilizadores do Azure AD conseguir iniciar sessão, têm de ser aprovisionados para a aplicação de Thoughtworks Mingle de mensagens em fila utilizando os respetivos nomes de utilizador do Azure Active Directory. No caso de Thoughtworks Mingle, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no Thoughtworks Mingle site da sua empresa como administrador.

2. Clique em **perfil**.
   
    ![O primeiro projeto](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785160.png "o primeiro projeto")

3. Clique em de **Admin** separador e, em seguida, clique em **utilizadores**.
   
    ![Os utilizadores](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785161.png "utilizadores")

4. Clique em **novo utilizador**.
   
    ![Novo utilizador](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785162.png "novo utilizador")

5. No **novo utilizador** diálogo página, execute os seguintes passos:
   
    ![Caixa de diálogo Nova utilizador](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785163.png "novo utilizador")  
 
    a. Tipo de **nome de início de sessão**, **nome a apresentar**, **palavra-passe de escolha**, **Confirmar palavra-passe** de um Azure válido conta do AD que pretende aprovisionar para caixas de texto relacionadas. 

    b. Como **tipo de utilizador**, selecione **utilizador completo**.

    c. Clique em **criar este perfil**.

>[!NOTE]
>Pode utilizar quaisquer outras Thoughtworks Mingle utilizador conta criação ferramentas ou APIs fornecidas pelo Thoughtworks Mingle para aprovisionar contas de utilizador do AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Thoughtworks Mingle.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Thoughtworks Mingle, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Thoughtworks Mingle**.

    ![A ligação Thoughtworks Mingle na lista de aplicações](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Thoughtworks Mingle no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação Thoughtworks Mingle.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_203.png

