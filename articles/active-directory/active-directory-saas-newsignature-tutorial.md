---
title: "Tutorial: Integração do Azure Active Directory com o Portal de gestão de nuvem do Microsoft Azure | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Portal de gestão de nuvem do Microsoft Azure."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: bae5f05a161b2730bf662bcb47f20ab3e1799951
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Tutorial: Integração do Azure Active Directory com o Portal de gestão de nuvem do Microsoft Azure

Neste tutorial, irá aprender a integrar o Portal de gestão de nuvem do Microsoft Azure com o Azure Active Directory (Azure AD).

Integrar Portal de gestão de nuvem do Microsoft Azure com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Portal de gestão de nuvem do Microsoft Azure
- Pode permitir que os utilizadores automaticamente obter assinado no Portal de gestão de nuvem do Microsoft Azure (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Portal de gestão de nuvem do Microsoft Azure, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Portal de gestão de nuvem para o Microsoft Azure-início de sessão único subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adição do Portal de gestão de nuvem do Microsoft Azure da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Adição do Portal de gestão de nuvem do Microsoft Azure da galeria do
Para configurar a integração do Portal de gestão de nuvem do Microsoft Azure com o Azure AD, terá de Adicionar Portal de gestão de nuvem do Microsoft Azure a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar o Portal de gestão de nuvem do Microsoft Azure da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Portal de gestão de nuvem do Microsoft Azure**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_search.png)

5. No painel de resultados, selecione **Portal de gestão de nuvem do Microsoft Azure**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o Portal de gestão de nuvem do Microsoft Azure com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Portal de gestão de nuvem do Microsoft Azure é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Portal de gestão de nuvem do Microsoft Azure tem de ser estabelecida.

No Portal de gestão de nuvem do Microsoft Azure, atribua o valor do **nome de utilizador** no Azure AD como o valor de **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com o Portal de gestão de nuvem do Microsoft Azure, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um Portal de gestão de nuvem para o utilizador de teste do Microsoft Azure](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)**  - para ter um homólogo de Britta Simon no Portal de gestão de nuvem do Microsoft Azure que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único no Portal de gestão da nuvem para a aplicação do Microsoft Azure.

**Para configurar o Azure AD-início de sessão único com o Portal de gestão de nuvem do Microsoft Azure, execute os seguintes passos:**

1. No portal do Azure, no **Portal de gestão de nuvem do Microsoft Azure** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_samlbase.png)

3. No **Portal de gestão de nuvem para o domínio do Microsoft Azure e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva um URL utilizando os seguintes padrões: 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. No **identificador** caixa de texto, escreva um URL utilizando os seguintes padrões: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. No **URL de resposta** caixa de texto, escreva um URL utilizando os seguintes padrões: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [Portal de gestão de nuvem para a equipa de suporte do Microsoft Azure cliente](mailto:jczernuszka@newsignature.com) para obter estes valores. 
 
4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-newsignature-tutorial/tutorial_general_400.png)

6. No **Portal de gestão de nuvem para a configuração do Microsoft Azure** secção, clique em **configurar Portal de gestão de nuvem do Microsoft Azure** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_configure.png) 

7. Para configurar o início de sessão único em **Portal de gestão de nuvem do Microsoft Azure** lado, terá de enviar o transferido **certificado**, **Sign-Out URL**, **único início de sessão no URL do serviço SAML** e **ID de entidade de SAML** para [Portal de gestão de nuvem para a equipa de suporte do Microsoft Azure](mailto:jczernuszka@newsignature.com). Se definir esta definição para que a ligação de SAML SSO corretamente em ambos os lados.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Criar um Portal de gestão de nuvem para o utilizador de teste do Microsoft Azure

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon no Portal de gestão de nuvem do Microsoft Azure. Consulte [Portal de gestão de nuvem para a equipa de suporte do Microsoft Azure](mailto:jczernuszka@newsignature.com) para adicionar os utilizadores no Portal de gestão da nuvem para a conta do Microsoft Azure.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso ao Portal de gestão de nuvem do Microsoft Azure.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon ao Portal de gestão de nuvem do Microsoft Azure, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Portal de gestão de nuvem do Microsoft Azure**.

    ![Configurar o início de sessão único](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.
Ao clicar em mosaico do Microsoft Azure no painel de acesso no Portal de gestão do Cloud, deve obter automaticamente com sessão iniciada ao seu Portal de gestão de nuvem para a aplicação do Microsoft Azure.

Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_203.png

