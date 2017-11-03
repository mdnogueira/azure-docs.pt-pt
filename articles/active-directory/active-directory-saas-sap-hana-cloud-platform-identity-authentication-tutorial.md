---
title: "Tutorial: Integração do Azure Active Directory com autenticação de identidade da plataforma de nuvem de SAP | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e autenticação de identidade da plataforma de nuvem de SAP."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Integração do Azure Active Directory com autenticação de identidade da plataforma de nuvem de SAP

Neste tutorial, saiba como integrar a autenticação de identidade da plataforma de nuvem de SAP no Azure Active Directory (Azure AD). Autenticação de identidade da plataforma de nuvem SAP é utilizada como um proxy IdP para aceder às aplicações de SAP através do Azure AD como IdP principal.

Autenticação de identidade da plataforma de nuvem de SAP a integração com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à aplicação de SAP.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para SAP aplicações-início de sessão único (SSO)) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com autenticação de identidade da plataforma de nuvem de SAP, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma autenticação de identidade da plataforma de nuvem de SAP-início de sessão único ativada subscrição

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar autenticação de identidade da plataforma de nuvem de SAP da galeria do
2. Configurar e testar o Azure AD de sessão único-

Antes de explorar os detalhes técnicos, é vital para compreender os conceitos que vai consultar. A Federação de autenticação de identidade da plataforma de nuvem de SAP e o Azure Active Directory permite-lhe implementar SSO em aplicações ou serviços protegidos pelo AAD (como um IdP) com aplicações SAP e serviços protegidos pela identidade de plataforma de nuvem do SAP Autenticação.

Atualmente, autenticação de identidade da plataforma de nuvem de SAP atua como um fornecedor de identidade de Proxy de aplicações SAP. Azure Active Directory por sua vez atua como o fornecedor de identidade à esquerda nesta configuração. 

O diagrama seguinte ilustra isto:

![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

Com esta configuração, o seu inquilino de autenticação de identidade da plataforma de nuvem de SAP será configurado como uma aplicação no Azure Active Directory fidedigna. 

Todas as aplicações SAP e serviços que pretende proteger através desta forma, subsequentemente, são configurados na consola de gestão de autenticação de identidade da plataforma de nuvem de SAP!

Isto significa que precisa de autorização para conceder acesso a serviços e aplicações SAP seja realizada na autenticação de identidade da plataforma de nuvem de SAP para este tipo de configuração (por oposição a configuração de autorização no Azure Active Directory).

Ao configurar a autenticação de identidade de plataforma do SAP na nuvem como uma aplicação através do Azure Active Directory Marketplace, não precisa de tratar dos processos configurar afirmações individuais necessárias / asserções de SAML e transformações necessários para produzir um válido token de autenticação para aplicações SAP.

>[!NOTE] 
>Atualmente, o Web SSO foi testado por ambas as entidades confiadoras, apenas. Fluxos necessários para a aplicação para API ou outras comunicações API para API devem funcionar, mas não foram testados, ainda. Serão testados como parte das atividades subsequentes.
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar autenticação de identidade da plataforma de nuvem de SAP da galeria do
Para configurar a integração da autenticação de identidade da plataforma de nuvem de SAP com o Azure AD, terá de adicionar a autenticação de identidade da plataforma de nuvem de SAP da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar a autenticação de identidade da plataforma de nuvem de SAP da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **autenticação de identidade da plataforma de nuvem de SAP**, selecione **autenticação de identidade da plataforma de nuvem de SAP** partir do painel de resultados, em seguida, clique em **adicionar** botão para Adicione a aplicação.

    ![SAP autenticação na identidade de plataforma em nuvem na lista de resultados](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com SAP nuvem plataforma de identidade de autenticação com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na autenticação de identidade da plataforma de nuvem de SAP é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador na autenticação de identidade da plataforma de nuvem de SAP relacionado tem de ser estabelecida.

Na autenticação de identidade de plataforma de nuvem de SAP, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com autenticação de identidade da plataforma de nuvem de SAP, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de autenticação de identidade da plataforma de nuvem de SAP](#create-an-sap-cloud-platform-identity-authentication-test-user)**  - para ter um homólogo de Britta Simon SAP Cloud Platform autenticação de identidade que está ligada a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação de autenticação de identidade da plataforma de nuvem de SAP.

**Para configurar o Azure AD-início de sessão único com autenticação de identidade da plataforma de nuvem de SAP, execute os seguintes passos:**

1. No portal do Azure, no **autenticação de identidade da plataforma de nuvem de SAP** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. No **SAP nuvem plataforma de identidade de autenticação de domínio e os URLs** secção, se pretender configurar a aplicação no **IDP** iniciada modo:

    ![SAP nuvem plataforma de identidade de autenticação de domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    No **identificador** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<entity-id>.accounts.ondemand.com`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o identificador real. Contacte [equipa de suporte de cliente de autenticação de identidade de plataforma de nuvem de SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter este valor. Se não souber este valor, siga a documentação de autenticação de identidade da plataforma de nuvem de SAP no [inquilino SAML 2.0 configuração](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Verifique **Mostrar avançadas definições de URL**. Se pretender configurar a aplicação no **SP** iniciada modo:

    ![SAP nuvem plataforma de identidade de autenticação de domínio e os URLs únicos de informações de início de sessão](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    No **URL de início de sessão** caixa de texto, escreva um URL a utilizar o padrão do seguinte:`https://<entity-id>.accounts.ondemand.com/admin`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de autenticação de identidade de plataforma de nuvem de SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter este valor.

5. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. Aplicação de autenticação de identidade de plataforma em nuvem do SAP espera as asserções de SAML num formato específico. Pode gerir os valores destes atributos a partir de "**atributos de utilizador**" secção na página de integração de aplicações. A seguinte captura de ecrã mostra um exemplo para este. 

    ![Configurar o início de sessão único](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, se espera que a aplicação de SAP um atributo, por exemplo "firstName". Na caixa de diálogo de atributos token SAML, adicione o atributo "firstName".

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o início de sessão único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. No **nome** caixa de texto, escreva o nome de atributo "firstName".
    
    c. Do **valor** lista, selecione o valor do atributo "user.givenname".
    
    d. Clique em **OK**.

8. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. No **configuração de autenticação de identidade de plataforma de nuvem de SAP** secção, clique em **configurar a autenticação na nuvem plataforma de identidade** para abrir **configurar início de sessão** janela. Copiar o **Sign-Out URL, o ID de entidade de SAML e o único início de sessão no URL do serviço SAML** do **secção de referência rápida.**

    ![Configuração de autenticação de identidade de plataforma de nuvem SAP](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Para obter SSO configurado para a sua aplicação, aceda à consola de administração do SAP nuvem plataforma de identidade de autenticação. O URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin`. Em seguida, siga a documentação do SAP nuvem plataforma de identidade de autenticação para [configurar Microsoft Azure AD como fornecedor de identidade empresarial na autenticação de identidade da plataforma de nuvem de SAP](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. No portal do Azure, clique em **guardar** botão.

12. Os seguintes passos só continue se pretende adicionar e ativar a SSO para outra aplicação de SAP. Repita os passos na secção "Adicionar SAP autenticação em nuvem plataforma identidade da galeria do" para adicionar outra instância do SAP nuvem plataforma de identidade de autenticação.

13. No portal do Azure, no **autenticação de identidade da plataforma de nuvem de SAP** página de integração de aplicações, clique em **ligado início de sessão**.

    ![Configurar o início de sessão ligado](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Guarde a configuração.

>[!NOTE] 
>A configuração de SSO para a aplicação de SAP anterior irá tirar partido da nova aplicação. Certifique-se de que utiliza o mesmo fornecedores de identidade empresarial na consola de administração de autenticação de identidade SAP Cloud Platform.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Criar um utilizador de teste de autenticação de identidade da plataforma de nuvem de SAP

Não precisa de criar um utilizador na autenticação de identidade da plataforma de nuvem de SAP. Os utilizadores que estão no arquivo de utilizador do Azure AD podem utilizar a funcionalidade SSO.

A autenticação de identidade da plataforma de nuvem SAP suporta a opção de Federação de identidade. Esta opção permite que a aplicação verificar se os utilizadores autenticados pelo fornecedor de identidade empresarial existem no arquivo de SAP nuvem plataforma de identidade autenticação de utilizador. 

Na definição de predefinição, a opção de Federação de identidade está desativada. Se estiver ativada a Federação de identidade, apenas os utilizadores que são importados na autenticação de identidade da plataforma de nuvem de SAP são capazes de aceder à aplicação. 

Para obter mais informações sobre como ativar ou desativar a Federação de identidade com a autenticação de identidade da plataforma de nuvem de SAP, consulte ativar a Federação de identidade com a autenticação de identidade do SAP Cloud Platform no [configurar a Federação de identidade com o Autenticação de identidade do utilizador arquivo do SAP na nuvem plataforma](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para autenticação de identidade da plataforma de nuvem de SAP.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a autenticação de identidade da plataforma de nuvem de SAP, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **autenticação de identidade da plataforma de nuvem de SAP**.

    ![A ligação de autenticação de identidade da plataforma de nuvem de SAP na lista de aplicações](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de autenticação de identidade da plataforma de nuvem de SAP no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de autenticação de identidade da plataforma de nuvem de SAP.
Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png

