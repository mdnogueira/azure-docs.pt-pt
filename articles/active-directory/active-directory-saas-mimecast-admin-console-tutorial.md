---
title: "Tutorial: Integração do Azure Active Directory com a consola de administração de Mimecast | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e a consola de administração de Mimecast."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: f401f592d79ad954aa466de74d3e3fbb18aa9a5b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Tutorial: Integração do Azure Active Directory com a consola de administração de Mimecast

Neste tutorial, irá aprender integrar Mimecast consola de administração do Azure Active Directory (Azure AD).

Consola de administração de Mimecast a integração com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à consola de administração de Mimecast.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada a consola de administração de Mimecast (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a consola de administração de Mimecast, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma consola de administração de Mimecast-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar a consola de administração de Mimecast na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Adicionar a consola de administração de Mimecast na galeria do
Para configurar a integração da consola de administração de Mimecast com o Azure AD, tem de adicionar a consola de administração do Mimecast na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar a consola de administração de Mimecast na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **consola de administração de Mimecast**, selecione **consola de administração de Mimecast** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Consola de administração de Mimecast na lista de resultados](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com a consola de administração de Mimecast com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na consola de administração de Mimecast é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na consola de administração de Mimecast tem de ser estabelecida.

Na consola de administração de Mimecast, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com a consola de administração de Mimecast, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste da consola de administração de Mimecast](#create-a-mimecast-admin-console-test-user)**  - para ter um homólogo de Britta Simon na consola de administração de Mimecast que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de consola de administração de Mimecast.

**Para configurar o Azure AD-início de sessão único com a consola de administração de Mimecast, execute os seguintes passos:**

1. No portal do Azure, no **consola de administração de Mimecast** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. No **URLs e de domínio de consola de administração de Mimecast** secção, execute os seguintes passos:

    ![Domínio de consola de administração de Mimecast e os URLs únicos de informações de início de sessão](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    No **URL de início de sessão** caixa de texto, escreva o URL:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > O URL de início de sessão é específico de região.

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_400.png)

6. No **configuração de consola de administrador Mimecast** secção, clique em **configurar a consola de administração de Mimecast** para abrir **configurar início de sessão** janela. Copiar o **ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração da consola de administração de Mimecast](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. Numa janela do browser web diferente, inicie sessão para a consola de administração Mimecast como administrador.

8. Aceda a **serviços \> aplicação**.

    ![Serviços](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794998.png "serviços")

9. Clique em **autenticação perfis**.

    ![Perfis de autenticação](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794999.png "perfis de autenticação")
    
10. Clique em **novo perfil de autenticação**.

    ![Novos perfis de autenticação](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795000.png "novos perfis de autenticação")

11. No **perfil autenticação** secção, execute os seguintes passos:

    ![Perfil de autenticação](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795015.png "perfil de autenticação")
    
    a. No **Descrição** caixa de texto, escreva um nome para a sua configuração.
    
    b. Selecione **impôr a autenticação SAML para a consola de administração de Mimecast**.
    
    c. Como **fornecedor**, selecione **do Azure Active Directory**.
    
    d. Colar **ID de entidade de SAML**, que copiou do portal do Azure para o **URL do emissor** caixa de texto.
    
    e. Colar **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure para o **URL de início de sessão** caixa de texto.

    f. Colar **único início de sessão no URL do serviço SAML**, que copiou do portal do Azure para o **URL de fim de sessão** caixa de texto.
    
    >[!NOTE]
    >O valor do URL de início de sessão e o valor do URL de fim de sessão são para a consola de administração Mimecast os mesmos.
    
    g. Abra o seu certificado de base-64 transferido a partir do portal do Azure no bloco de notas, remover a primeira linha ("*--*") e a última linha ("*--*"), copie os conteúdos restantes-lo para o área de transferência e, em seguida, cole-os para o **o certificado de fornecedor de identidade (metadados)** caixa de texto.
    
    h. Selecione **Permitir início de sessão único em**.
    
    posso. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985) 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Criar um utilizador de teste da consola de administração de Mimecast

Para permitir que os utilizadores do Azure AD iniciar sessão na consola de administração de Mimecast, têm de ser aprovisionados na consola de administração de Mimecast. No caso da consola de administração de Mimecast, o aprovisionamento é uma tarefa manual.

* Tem de registar um domínio, antes de poder criar utilizadores.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **consola de administração de Mimecast** como administrador.
2. Aceda a **diretórios \> interno**.
   
   ![Diretórios](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795003.png "diretórios")
3. Clique em **registar o novo domínio**.
   
   ![Registar o novo domínio](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795004.png "registar o novo domínio")
4. Depois de ter sido criado o novo domínio, clique em **novo endereço**.
   
   ![Novo endereço](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795005.png "novo endereço")
5. Na caixa de diálogo novo do endereço, execute os seguintes passos:
   
   ![Guardar](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795006.png "guardar")
   
   a. Tipo de **endereço de correio eletrónico**, **nome Global**, **palavra-passe**, e **Confirmar palavra-passe** atributos de um válido do Azure AD da conta pretende Aprovisionar em relacionados caixas de texto.

   b. Clique em **Guardar**.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de consola de administração de Mimecast ou APIs fornecidas pelos Mimecast consola de administração para aprovisionar contas de utilizador do Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à consola de administração de Mimecast.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a consola de administração de Mimecast, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **consola de administração de Mimecast**.

    ![A ligação da consola de administração de Mimecast na lista de aplicações](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico da consola de administração de Mimecast no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de consola de administração de Mimecast.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_203.png

