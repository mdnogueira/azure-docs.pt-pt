---
title: "Tutorial: Integração do Azure Active Directory nuvem de objeto do SAP Business | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e na nuvem de objeto do SAP Business."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 6d517c5e302ac36e5bba2053998c75f8f4d42683
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Tutorial: Integração do Azure Active Directory nuvem de objeto do SAP Business

Neste tutorial, irá aprender a integração em nuvem de objeto do SAP Business com o Azure Active Directory (Azure AD).

Obter as seguintes vantagens quando integrar o SAP Business objeto de nuvem com o Azure AD:

- No Azure AD, pode controlar quem tem acesso à nuvem de objeto do SAP Business.
- Pode assinar automaticamente na nuvem de objeto do SAP Business que os utilizadores ao utilizar o início de sessão único e a conta do Azure AD de um utilizador.
- Pode gerir as contas numa, localização central, o portal do Azure.

Para saber mais sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a nuvem de objeto do SAP Business, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma nuvem de objecto de negócio SAP, com início de sessão ligado

> [!NOTE]
> Se testar os passos neste tutorial, recomendamos que não testá-los num ambiente de produção.

Recomendações para testar os passos neste tutorial:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicione SAP Business objeto nuvem da galeria do.
2. Configure e teste do Azure AD-início de sessão único.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Adicionar SAP Business objeto nuvem da galeria do
Para configurar a integração do SAP Business objeto de nuvem com o Azure AD, na galeria, adicione na nuvem de objeto do SAP Business à sua lista de aplicações SaaS geridas.

Para adicionar a nuvem de objeto do SAP Business na galeria do:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **do Azure Active Directory**. 

    ![O botão do Azure Active Directory][1]

2. Selecione **aplicações empresariais**e, em seguida, selecione **todas as aplicações**.

    ![A página de aplicações da empresa][2]
    
3. Para adicionar uma nova aplicação, selecione **nova aplicação**.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, introduza **na nuvem de objeto do SAP Business**.

    ![Caixa de pesquisa](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. No painel de resultados, selecione **na nuvem de objeto do SAP Business**e, em seguida, selecione **adicionar**.

    ![Nuvem de objeto do SAP Business na lista de resultados](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configure e teste do Azure AD-início de sessão único

Nesta secção, iremos configurar e teste do Azure AD-início de sessão único com a nuvem de objeto do SAP Business com base num utilizador de teste com o nome *Britta Simon*.

Para início de sessão trabalhar, do Azure AD tem de saber o utilizador do Azure AD homólogo na nuvem de objeto do SAP Business. Tem de ser estabelecida uma relação de ligação entre um utilizador do Azure AD e o utilizador na nuvem do SAP Business objeto relacionado.

Para estabelecer a relação de ligação, na nuvem de objeto do SAP Business, para **Username**, atribua o valor do **nome de utilizador** no Azure AD.

Para configurar e testar o Azure AD-início de sessão único com a nuvem de objeto do SAP Business, conclua as seguintes tarefas:

1. [Configurar o Azure AD-início de sessão único](#set-up-azure-ad-single-sign-on). Configura um utilizador para utilizar esta funcionalidade.
2. [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user). Os testes do Azure AD-início de sessão único com o utilizador Britta Simon.
3. [Criar um utilizador de teste de nuvem de objeto do SAP Business](#create-an-sap-business-object-cloud-test-user). Cria um homólogo de Britta Simon na nuvem de objecto de negócio SAP que está ligada a representação do Azure AD do utilizador.
4. [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user). Conjuntos de cópias de segurança Britta Simon para utilizar o Azure AD único início de sessão.
5. [Teste o início de sessão único](#test-single-sign-on). Verifica se a configuração funciona.

### <a name="set-up-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, que ative do Azure AD único início de sessão no portal do Azure. Em seguida, iremos configurar o início de sessão único na sua aplicação de nuvem de objeto do SAP Business.

Para configurar o Azure AD-início de sessão único com a nuvem de objeto do SAP Business:

1. No portal do Azure, no **na nuvem de objeto do SAP Business** página de integração de aplicações, selecione **de sessão único-**.

    ![Selecione o início de sessão único][4]

2. No **de sessão único-** página, para **modo**, selecione **baseados em SAML início de sessão**.
 
    ![Selecione baseados em SAML início de sessão](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. Em **URLs e de domínio de nuvem do SAP Business objeto**, conclua os seguintes passos:

    1. No **URL de início de sessão** box, introduza um URL com o padrão do seguinte: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. No **identificador** box, introduza um URL com o padrão do seguinte:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![URLs de página do domínio de nuvem do SAP Business objeto e os URLs](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Os valores existentes nestes URLs são para a demonstração apenas. Atualize os valores com o URL de início de sessão real e o URL de identificador. Para obter o URL de início de sessão, contacte o [equipa de suporte de cliente de Cloud de objeto do SAP Business](https://www.sap.com/product/analytics/cloud-analytics.support.html). Pode obter o URL de identificador ao transferir os metadados de nuvem de objeto do SAP Business a partir da consola de administração. Isto é explicado mais tarde no tutorial. 

4. Em **certificado de assinatura de SAML**, selecione **XML de metadados**. Em seguida, guarde o ficheiro de metadados no seu computador.

    ![Selecione o XML de metadados](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Selecione **Guardar**.

    ![Selecionar guardar](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. Numa janela do browser web diferente, inicie sessão no site da sua empresa em nuvem de objeto do SAP Business como administrador.

7. Selecione **Menu** > **sistema** > **administração**.
    
    ![Selecione o Menu, em seguida, sistema e, em seguida, administração](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. No **segurança** separador, selecione o **editar** ícone (caneta).
    
    ![No separador segurança, selecione o ícone de edição](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. Para **método de autenticação**, selecione **SAML único Sign-On (SSO)**.

    ![Seleciona SAML Single Sign-On para o método de autenticação](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Para transferir os metadados do fornecedor de serviço (passo 1), selecione **transferir**. No ficheiro de metadados, localizar e copiar a **entityID** valor. No portal do Azure, em **URLs e de domínio de nuvem do SAP Business objeto**, colar o valor no **identificador** caixa.

    ![Copie e cole o valor de entityID](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Para carregar os metadados do fornecedor de serviço (passo 2) no ficheiro que transferiu a partir do portal do Azure, em **metadados de carregar o fornecedor de identidade**, selecione **carregar**.  

    ![Nos metadados de carregar o fornecedor de identidade, selecione o carregamento](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. No **atributo de utilizador** lista, selecione o atributo de utilizador (passo 3) que pretende utilizar para a implementação. Este atributo de utilizador mapeia para o fornecedor de identidade. Para introduzir um atributo personalizado na página do utilizador, utilize o **mapeamento de SAML personalizado** opção. Em alternativa, pode escolher que o **E-Mail** ou **ID de utilizador** como o atributo de utilizador. No nosso exemplo, selecionamos **E-Mail** porque foi mapeado a afirmação de identificador de utilizador com o **userprincipalname** atributo no **atributos de utilizador** secção do Azure Portal. Esta opção fornece um e-mail de utilizador exclusivo, que é enviado para a aplicação de nuvem de objeto do SAP Business em cada resposta SAML com êxito.

    ![Selecione o atributo de utilizador](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Para verificar a conta com o fornecedor de identidade (passo 4), no **credenciais de início de sessão (E-Mail)** box, introduza o endereço de correio eletrónico do utilizador. Em seguida, selecione **verificar conta**. O sistema adiciona as credenciais de início de sessão para a conta de utilizador.

    ![Introduza o e-mail e selecione verificar conta](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Selecione o **guardar** ícone.

    ![Ícone de guardar](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Pode ler uma versão concisa destas instruções no [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação! Depois de adicionar a aplicação, selecionando **do Active Directory** > **aplicações empresariais**, selecione o **Single Sign-On** separador. Pode aceder a documentação incorporada o **configuração** secção, na parte inferior da página. Para obter mais informações, consulte [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
Nesta secção, vai criar um utilizador de teste com o nome Britta Simon no portal do Azure.

Para criar um utilizador de teste no Azure AD:

1. No portal do Azure, no menu à esquerda, selecione **do Azure Active Directory**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Para apresentar a lista de utilizadores, selecione **utilizadores e grupos**e, em seguida, selecione **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Para abrir o **utilizador** caixa de diálogo, selecione **adicionar**.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo caixa, conclua os seguintes passos:
 
    1. No **nome** box, introduza **BrittaSimon**.

    2. No **nome de utilizador** box, introduza o endereço de e-mail do utilizador Britta Simon.

    3. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    4. Selecione **Criar**.

        ![A caixa de diálogo de utilizador](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Criar utilizador do Azure AD][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Criar um utilizador de teste de nuvem de objeto do SAP Business

Os utilizadores do AD do Azure têm de ser aprovisionados na nuvem de objeto do SAP Business antes de poderem iniciar sessão na nuvem de objeto do SAP Business. Na nuvem de objeto do SAP Business, o aprovisionamento é uma tarefa manual.

Para Aprovisionar uma conta de utilizador:

1. Inicie sessão no site da sua empresa em nuvem de objeto do SAP Business como administrador.

2. Selecione **Menu** > **segurança** > **utilizadores**.

    ![Adicionar empregado](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. No **utilizadores** página, para adicionar novos detalhes de utilizador, selecione  **+** . 

    ![Adicionar página de utilizadores](./media/active-directory-saas-sapboc-tutorial/user4.png)

    Em seguida, conclua os seguintes passos:

    1. No **ID de utilizador** box, introduza o ID de utilizador do utilizador, como **Britta**.

    2. No **nome próprio** box, introduza o nome próprio do utilizador, como **Britta**.

    3. No **Apelido** box, introduza o apelido do utilizador, como **Simon**.

    4. No **nome a apresentar** box, introduza o nome completo do utilizador, como **Britta Simon**.

    5. No **correio electrónico** box, introduza o endereço de e-mail do utilizador, como  **brittasimon@contoso.com** .

    6. No **selecionar funções de** página, selecione a função adequada para o utilizador e, em seguida, selecione **OK**.

      ![Selecionar função](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. Selecione o **guardar** ícone.    


### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, que permite que o utilizador Britta Simon para utilizar o Azure AD-início de sessão único ao conceder o acesso da conta de utilizador para a nuvem de objeto do SAP Business.

Para atribuir Britta Simon a nuvem de objeto do SAP Business:

1. No portal do Azure, abra a vista de aplicações e, em seguida, avance para a vista de diretório. Selecione **aplicações empresariais**e, em seguida, selecione **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **na nuvem de objeto do SAP Business**.

    ![Configurar o início de sessão único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![Selecionar utilizadores e grupos][202] 

4. Selecione **Adicionar**. Em seguida, no **adicionar atribuição** página, selecione **utilizadores e grupos**.

    ![A página Adicionar atribuição][203]

5. No **utilizadores e grupos** página, na lista de utilizadores, selecionadas **Britta Simon**.

6. No **utilizadores e grupos** página, selecione **selecione**.

7. No **adicionar atribuição** página, selecione **atribuir**.

![Atribuir a função de utilizador][200] 
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, teste configuração do Azure AD único início de sessão utilizando o painel de acesso.

Quando seleciona o mosaico de nuvem de objeto do SAP Business no painel de acesso, esta deve ser iniciada automaticamente à sua aplicação de nuvem de objeto do SAP Business.

Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png

