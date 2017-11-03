---
title: "Tutorial: Integração do Azure Active Directory com AnswerHub | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e AnswerHub."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 3a1c9cc5d7a2ebe28e9fb7e0e6ed8e3d393873ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integração do Azure Active Directory com AnswerHub

Neste tutorial, irá aprender a integrar AnswerHub com o Azure Active Directory (Azure AD).

Integrar AnswerHub com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao AnswerHub
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para AnswerHub (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com AnswerHub, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um AnswerHub-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar AnswerHub a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-answerhub-from-the-gallery"></a>Adicionar AnswerHub a partir da Galeria
Para configurar a integração de AnswerHub com o Azure AD, terá de adicionar AnswerHub a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar AnswerHub a partir da galeria, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **AnswerHub**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_search.png)

5. No painel de resultados, selecione **AnswerHub**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com AnswerHub com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no AnswerHub é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no AnswerHub tem de ser estabelecida.

No AnswerHub, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com AnswerHub, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste AnswerHub](#creating-an-answerhub-test-user)**  - para ter um homólogo de Britta Simon AnswerHub que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação AnswerHub.

**Para configurar o Azure AD-início de sessão único com AnswerHub, execute os seguintes passos:**

1. No portal do Azure, no **AnswerHub** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. No **AnswerHub domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<company>.answerhub.com`

    b. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<company>.answerhub.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente AnswerHub](mailto:success@answerhub.com) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-answerhub-tutorial/tutorial_general_400.png)

6. No **AnswerHub configuração** secção, clique em **configurar AnswerHub** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_configure.png) 

7. Numa janela do browser web diferente, inicie sessão no site da sua empresa AnswerHub como administrador.
   
    >[!NOTE]
    >Se precisar de ajuda para configurar AnswerHub, contacte [equipa de suporte do AnswerHub](mailto:success@answerhub.com.).
   
8. Aceda a **administração**.

9. Clique em de **utilizador e grupo** separador.

10. No painel de navegação no lado esquerdo, no **definições de redes sociais** secção, clique em **configuração SAML**.

11. Clique em **IDP configuração** separador.

12. No **IDP configuração** separador, execute os seguintes passos:

     ![A configuração SAML](./media/active-directory-saas-answerhub-tutorial/ic785172.png "configuração SAML")  
  
     a. No **URL de início de sessão do IDP** caixa de texto, colar **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.
  
     b. No **URL de fim de sessão do IDP** caixa de texto, colar **Sign-Out URL** valor que copiou do portal do Azure.
     
     c. No **formato do identificador de nome de IDP** caixa de texto, introduza o utilizador identificador valor mesmo como seleccionado no portal do Azure no **atributos de utilizador** secção.
  
     d. Clique em **chaves e certificados**.

13. No separador de chaves e certificados, execute os seguintes passos:
    
     ![As chaves e certificados](./media/active-directory-saas-answerhub-tutorial/ic785173.png "chaves e certificados")  
 
     a. Abra o certificado codificado de base-64 que transferiu a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo para a sua área de transferência e, em seguida, cole-os para o **chave pública do IDP (x 509 formato)** caixa de texto.
  
     b. Clique em **Guardar**.

14. No **IDP configuração** separador, clique em **guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-answerhub-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-answerhub-test-user"></a>Criar um utilizador de teste AnswerHub

Para permitir que os utilizadores do Azure AD iniciem sessão nos AnswerHub, têm de ser aprovisionados para AnswerHub.  
No caso de AnswerHub, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **AnswerHub** site da empresa como administrador.

2. Aceda a **administração**.

3. Clique em de **utilizadores e grupos** separador.

4. No painel de navegação no lado esquerdo, no **gerir utilizadores** secção, clique em **criar ou importar utilizadores**.
   
   ![Utilizadores e grupos](./media/active-directory-saas-answerhub-tutorial/ic785175.png "utilizadores e grupos")

5. Tipo de **endereço de correio eletrónico**, **Username** e **palavra-passe** de uma conta válida do Azure Active Directory que pretende aprovisionar em caixas de texto relacionadas e, em seguida, clique em **guardar**.

>[!NOTE]
>Pode utilizar quaisquer outras AnswerHub utilizador conta criação ferramentas ou APIs fornecidas pelo AnswerHub para aprovisionar contas de utilizador do AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para AnswerHub.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a AnswerHub, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **AnswerHub**.

    ![Configurar o início de sessão único](./media/active-directory-saas-answerhub-tutorial/tutorial_answerhub_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico AnswerHub no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de AnswerHub.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-answerhub-tutorial/tutorial_general_203.png

