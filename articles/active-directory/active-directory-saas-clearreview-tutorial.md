---
title: "Tutorial: Integração do Azure Active Directory com revisão limpar | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e desmarque revisão."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: jeedes
ms.openlocfilehash: 09ca9f68832f58b69e8fbe78f637803eec17a336
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Tutorial: Integração do Azure Active Directory com revisão limpar

Neste tutorial, irá aprender a integrar revisão encriptado com o Azure Active Directory (Azure AD).

Integrar revisão encriptado com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao rever encriptado.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para limpar revisão (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com revisão limpar, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma revisão limpe início de sessão ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de revisão clara da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-clear-review-from-the-gallery"></a>A adição de revisão clara da galeria do
Para configurar a integração da revisão encriptado com o Azure AD, tem de adicionar revisão clara da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar a revisão clara da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **revisão limpar**, selecione **revisão limpar** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Reveja limpar na lista de resultados](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com revisão encriptado com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo em revisão encriptado é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em Limpar revisão tem de ser estabelecida.

Em Limpar revisão, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com revisão limpar, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de revisão limpar](#create-a-clear-review-test-user)**  - para ter um homólogo de Britta Simon na revisão claro que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de revisão encriptado.

**Para configurar do Azure AD-início de sessão único com revisão limpar, execute os seguintes passos:**

1. No portal do Azure, no **revisão limpar** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_samlbase.png)

3. No **limpar de revisão de domínio e os URLs** secção, execute os seguintes passos, se pretender configurar a aplicação no **IdP iniciada** modo:

    ![Limpar o domínio de revisão e URLs único informações de início de sessão](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_url.png)

    a. No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<customer name>.clearreview.com/sso/metadata`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<customer>.clearreview.com/sso/acs/`

4. Verifique **Mostrar avançadas definições de URL** e executar o passo seguinte, se pretender configurar a aplicação no **SP** iniciada modo:

    ![Limpar o domínio de revisão e URLs único informações de início de sessão](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_url_sp.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<customer name>.clearreview.com`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o identificador e o URL de resposta real. Contacte [equipa de suporte de revisão limpar](https://clearreview.com/contact/) para obter estes valores.

5. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_certificate.png)

6. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-clearreview-tutorial/tutorial_general_400.png)

7. No **eliminar a revisão configuração** secção, clique em **configurar limpar revisão** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Limpar a configuração de revisão](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_configure.png) 

8. Para configurar o início de sessão único em **revisão limpar** lado, abra o **revisão limpar** portal com credenciais de administrador.

9. Selecione **Admin** do painel de navegação esquerdo.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app_admin1.png)

10. Selecione **alteração** na parte inferior da página.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app_admin2.png)

11. Execute os seguintes passos **as definições de início de sessão único** página

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. No **URL do emissor** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.

    b. No **ponto final de SAML** caixa de texto, cole o valor de **único início de sessão no URL do serviço SAML** que copiou do portal do Azure.    

    c. No **ponto final de SLO** caixa de texto, cole o valor de **URL do serviço de início de sessão** que copiou do portal do Azure. 

    d. Abra o certificado transferido no bloco de notas e cole o conteúdo a **certificado x. 509** caixa de texto.   

12. Clique em **Guardar**.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-clearreview-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-clearreview-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-clearreview-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-clearreview-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-clear-review-test-user"></a>Criar um utilizador de teste de revisão limpar

Nesta secção, vai criar um utilizador chamado Britta Simon em Limpar revisão. Consulte [equipa de suporte de revisão limpar](https://clearreview.com/contact/) para adicionar os utilizadores na plataforma revisão limpar.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, ative Britta Simon utilizar Azure-início de sessão único, concedendo acesso a rever encriptado.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a revisão limpar, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **revisão limpar**.

    ![A ligação de revisão limpar na lista de aplicações](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de revisão limpar no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de revisão encriptado.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_203.png

