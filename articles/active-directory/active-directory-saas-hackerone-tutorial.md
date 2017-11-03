---
title: "Tutorial: Integração do Azure Active Directory com Hackerone | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Hackerone."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 657d8d4c98b7b133698a5cda0aa675da7f68c464
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Tutorial: Integração do Azure Active Directory com HackerOne

Neste tutorial, irá aprender a integrar HackerOne com o Azure Active Directory (Azure AD).

Integrar HackerOne com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao HackerOne
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para HackerOne (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com HackerOne, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um HackerOne-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar HackerOne a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-hackerone-from-the-gallery"></a>Adicionar HackerOne a partir da Galeria
Para configurar a integração de HackerOne com o Azure AD, terá de adicionar HackerOne a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar HackerOne a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **HackerOne**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_search.png)

5. No painel de resultados, selecione **HackerOne**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-

Nesta secção, configure e teste do Azure AD-início de sessão único com HackerOne com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no HackerOne é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no HackerOne tem de ser estabelecida.

No HackerOne, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com HackerOne, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste HackerOne](#creating-a-hackerone-test-user)**  - para ter um homólogo de Britta Simon HackerOne que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação HackerOne.

**Para configurar o Azure AD-início de sessão único com HackerOne, execute os seguintes passos:**

1. No portal do Azure, no **HackerOne** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_samlbase.png)

3. No **URL de início de sessão único HackerOne e identificador** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://hackerone.com/<company name>/authentication`

    b. No **identificador** caixa de texto, escreva um URL como:`https://hackerone.com/users/saml/metadata`
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de HackerOne](mailto:support@hackerone.com) para obter este valor. 
 
4. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_general_400.png)

6. No **HackerOne configuração** secção, clique em **configurar HackerOne** para abrir **configurar início de sessão** janela. Copiar o **único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_configure.png) 

7. Início de sessão no seu inquilino HackerOne como administrador.

8. No menu na parte superior, clique em de "**definições**."
   
    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 

9. Navegue para "**autenticação**"e clique em"**adicionar definições de SAML**."
   
    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 

10. No **SAML definições** caixa de diálogo, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 

    a. No **domínio de correio eletrónico** caixa de texto, escreva um domínio registado.

    b. No **início de sessão único no URL** caixas de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.

    c. Abra o **ficheiro de certificado** no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para sua área de transferência e, em seguida, cole-os para o **certificados X509** caixa de texto.
    
    d. Clique em **Guardar**.

11. Na caixa de diálogo Definições de autenticação, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 

    a. Clique em **executar teste**.

    b. Se o valor da **estado** campo é igual a **último Estado do teste: criado**, contacte o [HackerOne a equipa de suporte](mailto:support@hackerone.com) para pedir uma revisão da configuração.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-hackerone-test-user"></a>Criar um utilizador de teste HackerOne

Em seguida, crie um utilizador chamado Britta Simon HackerOne. HackerOne suporta o aprovisionamento de just-in-time, que está ativada por predefinição.

Não há nenhum item de ação para si nesta secção. Quando acedem HackerOne, um novo utilizador é criado se não existir ainda.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar a equipa de suporte Certify. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para HackerOne.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a HackerOne, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **HackerOne**.

    ![Configurar o início de sessão único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Por fim, testar a configuração do Azure AD único início de sessão através do painel de acesso.  

Quando clica no mosaico HackerOne no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de HackerOne.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png

