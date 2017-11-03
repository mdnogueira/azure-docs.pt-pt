---
title: "Tutorial: Integração do Azure Active Directory com Five9 adaptador Plus (CTI, contacte o Centro de agentes) | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Five9 adaptador Plus (CTI, contacte o Centro de agentes)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: d75163ea5eb3fa811e07861f06e6c4d5c758b898
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Tutorial: Integração do Azure Active Directory com Five9 adaptador Plus (CTI, contacte o Centro de agentes)

Neste tutorial, saiba como integrar Five9 adaptador Plus (CTI, agentes do System Center de contacto) com o Azure Active Directory (Azure AD).

Integrar Five9 adaptador Plus (CTI, agentes do System Center de contacto) com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Five9 adaptador Plus (CTI, contacte o Centro de agentes)
- Pode ativar a que os utilizadores automaticamente obter com sessão iniciada para Five9 adaptador Plus (CTI, contacte o Centro de agentes) (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Five9 adaptador Plus (CTI, contacte o Centro de agentes), terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um Five9 adaptador Plus (CTI, contacte o Centro de agentes)-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Five9 adaptador Plus (CTI, contacte o Centro de agentes) na galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Adicionar Five9 adaptador Plus (CTI, contacte o Centro de agentes) na galeria do
Para configurar a integração de Five9 adaptador Plus (CTI, agentes do System Center de contacto) com o Azure AD, terá de adicionar Five9 placa Plus (CTI, contacte o Centro de agentes) na Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Five9 adaptador Plus (CTI, contacte o Centro de agentes) na galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Five9 adaptador Plus (CTI, contacte o Centro de agentes)**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-five9-tutorial/tutorial_five9_search.png)

5. No painel de resultados, selecione **Five9 adaptador Plus (CTI, contacte o Centro de agentes)**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com Five9 Plus placa (CTI, agentes do System Center de contacto) com base no chamado "Britta Simon" um utilizador de teste.

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no Five9 adaptador Plus (CTI, contacte o Centro de agentes) é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Five9 adaptador Plus (CTI, contacte o Centro de agentes) tem de ser estabelecida.

No Five9 Plus adaptador (CTI, contacte o Centro de agentes), atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Five9 adaptador Plus (CTI, contacte o Centro de agentes), tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste Five9 adaptador Plus (CTI, contacte o Centro de agentes)](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)**  - para ter um homólogo de Britta Simon Five9 Plus adaptador (CTI, contacte o Centro de agentes) que está ligada a representação do Azure AD do utilizador.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Five9 adaptador Plus (CTI, contacte o Centro de agentes).

**Para configurar o Azure AD-início de sessão único com Five9 adaptador Plus (CTI, contacte o Centro de agentes), execute os seguintes passos:**

1. No portal do Azure, no **Five9 adaptador Plus (CTI, contacte o Centro de agentes)** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-five9-tutorial/tutorial_five9_samlbase.png)

3. No **domínio Five9 adaptador Plus (CTI, contacte o Centro de agentes) e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-five9-tutorial/tutorial_five9_url.png)
    
    a. No **identificador** caixa de texto, escreva um URL utilizando os seguintes padrões:

    |    Ambiente      |       URL      |
    | :-- | :-- |
    | Para "Five9 Plus adaptador para Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Para "Five9 Plus adaptador para Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Para "Five9 Plus adaptador para o Toolkit de ambiente de trabalho de agente" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:

    |      Ambiente     |      URL      |
    | :--                  | :--           |
    | Para "Five9 Plus adaptador para Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Para "Five9 Plus adaptador para Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Para "Five9 Plus adaptador para o Toolkit de ambiente de trabalho de agente" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

4. No **certificado de assinatura de SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-five9-tutorial/tutorial_five9_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/active-directory-saas-five9-tutorial/tutorial_general_400.png)

6. No **Five9 adaptador Plus (CTI, contacte o Centro de agentes) configuração** secção, clique em **configurar Five9 Plus adaptador (CTI, contacte o Centro de agentes)** para abrir **configurar início de sessão**janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/active-directory-saas-five9-tutorial/tutorial_five9_configure.png) 

7. Para configurar o início de sessão único em **Five9 adaptador Plus (CTI, contacte o Centro de agentes)** lado, terá de enviar o transferido **Certificate(Base64), Sign-Out URL, ID de entidade de SAML e único início de sessão no URL do serviço SAML**para [equipa de suporte de Five9 adaptador Plus (CTI, contacte o Centro de agentes)](https://www.five9.com/about/contact). Também além disso, para configurar o SSO mais siga os passos, de acordo com o adaptador abaixo:

    a. "Five9 Plus adaptador para o Toolkit de ambiente de trabalho de agente" Guia do administrador: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus adaptador para Microsoft Dynamics CRM" Guia do administrador: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus adaptador para Zendesk" Guia do administrador: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Criar um utilizador de teste Five9 adaptador Plus (CTI, contacte o Centro de agentes)

Nesta secção, vai criar um utilizador chamado Britta Simon no Five9 adaptador Plus (CTI, contacte o Centro de agentes). Trabalhar com [equipa de suporte de Five9 adaptador Plus (CTI, contacte o Centro de agentes)](https://www.five9.com/about/contact) para adicionar os utilizadores na plataforma Five9 adaptador Plus (CTI, contacte o Centro de agentes). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso Five9 adaptador Plus (CTI, contacte o Centro de agentes).

![Atribua o utilizador][200] 

**Para atribuir Britta Simon Five9 adaptador Plus (CTI, contacte o Centro de agentes), execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Five9 adaptador Plus (CTI, contacte o Centro de agentes)**.

    ![Configurar o início de sessão único](./media/active-directory-saas-five9-tutorial/tutorial_five9_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico Five9 adaptador Plus (CTI, contacte o Centro de agentes) no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de Five9 adaptador Plus (CTI, contacte o Centro de agentes).
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-five9-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-five9-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-five9-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-five9-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-five9-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-five9-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-five9-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-five9-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-five9-tutorial/tutorial_general_203.png

