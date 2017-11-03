---
title: "Tutorial: Integração do Azure Active Directory com Recognize | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Recognize."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 97d85183d0307c41a3b879d440d87a6fb0c53190
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Integração do Azure Active Directory com Recognize

Neste tutorial, irá aprender a integrar Recognize com o Azure Active Directory (Azure AD).

Integrar Recognize com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Recognize
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Recognize (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Recognize, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Recognize-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Recognize a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-recognize-from-the-gallery"></a>Adicionar Recognize a partir da Galeria
Para configurar a integração de Recognize com o Azure AD, terá de adicionar Recognize a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Recognize a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Recognize**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_search.png)

5. No painel de resultados, selecione **Recognize**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Recognize com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Recognize é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Recognize tem de ser estabelecida.

No Recognize, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Recognize, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Recognize](#creating-a-recognize-test-user)**  - para ter um homólogo de Britta Simon Recognize que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Recognize.

**Para configurar o Azure AD-início de sessão único com Recognize, execute os seguintes passos:**

1. No portal do Azure, no **Recognize** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_samlbase.png)

3. No **reconhecer domínios e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://recognizeapp.com/<your-domain>/saml/sso`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente reconhecer](mailto:support@recognizeapp.com) obter URL de início de sessão e pode obter valor do identificador abrindo o URL de metadados do fornecedor de serviço da secção definições de SSO que é explicada mais tarde no tutorial. . 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-recognize-tutorial/tutorial_general_400.png)

6. No **configuração reconhece** secção, clique em **configurar reconhecer** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_configure.png) 

7. Numa janela do browser web diferente, início de sessão no seu inquilino Recognize como administrador.

8. No canto superior direito, clique em **Menu**. Aceda a **da empresa Admin**.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

9. No painel de navegação esquerdo, clique em **definições**.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

10. Execute os seguintes passos no **SSO definições** secção.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
    
    a. Como **ativar a SSO**, selecione **ON**.

    b. No **ID da entidade IDP** caixa de texto, colar o valor de **ID de entidade de SAML** que copiou do portal do Azure.
    
    c. No **url de destino de Sso** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
    
    d. No **url de destino de Slo** caixa de texto, cole o valor de **Sign-Out URL** que copiou do portal do Azure. 
    
    e. Abra o transferido **certificado (Base64)** ficheiro no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **certificado** caixa de texto.
    
    f. Clique em de **guardar definições** botão. 

11. Junto a **SSO definições** secção, copie o URL em **url de metadados do fornecedor de serviço**.
   
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

12. Abra o **ligação do URL de metadados** sob um browser para transferir o documento de metadados em branco. Em seguida, copie o value(entityID) EntityDescriptor do ficheiro e cole-a no **identificador** textbox em **secção reconhecer o domínio e os URLs** no portal do Azure.
    
    ![Configurar lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-recognize-test-user"></a>Criar um utilizador de teste Recognize

Para permitir que os utilizadores do Azure AD sessão Recognize, têm de ser aprovisionados para Recognize. No caso de Recognize, o aprovisionamento é uma tarefa manual.

Esta aplicação não suporta o aprovisionamento de SCIM mas tem uma sincronização de utilizador alternativas que Aprovisiona utilizadores. 

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Recognize como administrador.

2. No canto superior direito, clique em **Menu**. Aceda a **da empresa Admin**.

3. No painel de navegação esquerdo, clique em **definições**.

4. Execute os seguintes passos no **utilizador sincronização** secção.
   
   ![Novo utilizador](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "novo utilizador")
   
   a. Como **sincronização ativada**, selecione **ON**.
   
   b. Como **fornecedor de sincronização escolha**, selecione **Microsoft / Office 365**.
   
   c. Clique em **executar a sincronização de utilizador**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Recognize.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a Recognize, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Recognize**.

    ![Configurar o início de sessão único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Recognize no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Recognize. Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png

