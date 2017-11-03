---
title: "Tutorial: Integração do Azure Active Directory com ServiceChannel | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e ServiceChannel."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.openlocfilehash: 7e1dad18ff0ae9a9102b789b2cb32e7b96ed3d38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Tutorial: Integração do Azure Active Directory com ServiceChannel

Neste tutorial, irá aprender a integrar ServiceChannel com o Azure Active Directory (Azure AD).

Integrar ServiceChannel com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ServiceChannel
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para ServiceChannel (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal de gestão do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ServiceChannel, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Um ServiceChannel início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar ServiceChannel a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-servicechannel-from-the-gallery"></a>Adicionar ServiceChannel a partir da Galeria
Para configurar a integração de ServiceChannel com o Azure AD, terá de adicionar ServiceChannel a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar ServiceChannel a partir da galeria, execute os seguintes passos:**

1. No  **[Azure Management Portal](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **ServiceChannel**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_000.png)

5. No painel de resultados, selecione **ServiceChannel**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com ServiceChannel com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo no ServiceChannel é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ServiceChannel tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no ServiceChannel.

Para configurar e testar o Azure AD-início de sessão único com ServiceChannel, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de ServiceChannel](#creating-a-servicechannel-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal de gestão do Azure e configurar o início de sessão único na sua aplicação de ServiceChannel.

**Para configurar o Azure AD-início de sessão único com ServiceChannel, execute os seguintes passos:**

1. No portal de gestão do Azure, no **ServiceChannel** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, como **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_01.png)

3. No **ServiceChannel domínio e os URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. No **identificador** caixa de texto, digite o valor como:`http://adfs.<domain>.com/adfs/service/trust`

    b. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE] 
    > Tenha em atenção que estas não são os valores reais. Tem de atualizar estes valores com o identificador e o URL de resposta real. Aqui sugerimos que utilize o valor de cadeia exclusivo no identificador de. Contacte [equipa de suporte de ServiceChannel](https://servicechannel.zendesk.com/hc/en-us) para obter estes valores.

4. A aplicação de ServiceChannel espera as asserções de SAML num formato específico, que necessita para adicionar mapeamentos de atributos personalizado à sua configuração de atributos token SAML. A seguinte captura de ecrã mostra um exemplo para este. **NameIdentifier (utilizador Identifier)** a afirmação apenas obrigatório e o valor predefinido é **user.userprincipalname** mas ServiceChannel espera este deve ser mapeado com **user.mail**. Se estiver a planear ativar o aprovisionamento de utilizador apenas In Time, deve adicionar as seguintes afirmações conforme mostrado abaixo. **Função** afirmação tem de ser mapeada para **user.assignedroles** que contém a função do utilizador.  

    Pode consultar o guia de ServiceChannel [aqui](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example) para obter mais orientações sobre afirmações.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Clique [aqui](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/) saber como configurar **função** no Azure AD

5. No **atributos de utilizador** secção, clique em **ver e editar todos os outros atributos de utilizador** e definir os atributos.

    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | Função| User.assignedroles |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Configurar o início de sessão único](./media/active-directory-saas-servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**
    
6. No **certificado de assinatura de SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_05.png) 

7. Clique em **Guardar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicechannel-tutorial/tutorial_general_400.png)

8. No **ServiceChannel configuração** secção, clique em **configurar ServiceChannel** para abrir **configurar início de sessão** janela. Tenha em atenção o **SAML Enitity ID** do **referência rápida** secção.

9. Para configurar o início de sessão único em **ServiceChannel** lado, terá de enviar o transferido **certificado (Base64)** e **ID de entidade de SAML** para [equipa de suporte de ServiceChannel](https://servicechannel.zendesk.com/hc/en-us). Estes irão configurar esta opção para poder efetuar a ligação de SAML SSO corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal de gestão do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal de gestão do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-servicechannel-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**. 

### <a name="creating-a-servicechannel-test-user"></a>Criar um utilizador de teste de ServiceChannel

Aplicação suporta apenas no tempo de aprovisionamento de utilizador e de utilizadores de autenticação serão criados na aplicação automaticamente. Para o aprovisionamento de utilizador completo, entre em contacto com [ServiceChannel a equipa de suporte](https://servicechannel.zendesk.com/hc/en-us)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao ServiceChannel.

![Atribua o utilizador][200] 

**Para atribuir Britta Simon a ServiceChannel, execute os seguintes passos:**

1. No portal de gestão do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **ServiceChannel**.

    ![Configurar o início de sessão único](./media/active-directory-saas-servicechannel-tutorial/tutorial-servicechannel_app01.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de ServiceChannel no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de ServiceChannel.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-servicechannel-tutorial/tutorial_general_203.png