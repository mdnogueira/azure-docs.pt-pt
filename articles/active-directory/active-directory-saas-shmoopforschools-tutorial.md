---
title: "Tutorial: Integração do Azure Active Directory com Shmoop para escolas | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e Shmoop para escolas."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jeedes
ms.openlocfilehash: f30e0967384fb881bab045f6544989b8baaccfa0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Tutorial: Integração do Azure Active Directory com Shmoop para escolas

Neste tutorial, irá aprender a integrar Shmoop para escolas com o Azure Active Directory (Azure AD).

Integrar Shmoop para escolas com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Shmoop para escolas.
- Pode permitir que os utilizadores automaticamente obter a sessão iniciada no Shmoop para escolas com as respetivas contas do Azure AD.
- Pode gerir as contas numa única localização central, o portal do Azure.

Para obter mais informações sobre a integração de aplicações SaaS com o Azure AD, consulte [que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Shmoop para escolas, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma subscrição do Shmoop para escolas único início de sessão em ativado

> [!NOTE]
> Não recomendamos a utilização de um ambiente de produção para testar os passos neste tutorial.

Para testar os passos neste tutorial, recomendamos que:

- Utilizar o evironment produção apenas se for necessário.
- Obter um [um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) se ainda não tiver um ambiente de avaliação do Azure AD.

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário é descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Shmoop para escolas a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Adicionar Shmoop para escolas a partir da Galeria
Para configurar a integração de Shmoop para escolas com o Azure AD, terá de adicionar Shmoop para escolas a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Shmoop para escolas a partir da galeria, siga os passos seguintes:**

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Aceda a **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **Shmoop para escolas**. Selecione **Shmoop para escolas** nos resultados, em seguida, selecione o **adicionar** botão para adicionar a aplicação.

    ![Shmoop para escolas na lista de resultados](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com Shmoop para escolas com base num utilizador de teste chamado "Britta Simon."

Para início de sessão trabalhar, do Azure AD tem de saber que o utilizador homólogo no Shmoop para escolas for para um utilizador no Azure AD. Por outras palavras, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado no Shmoop para escolas.

No Shmoop para escolas, atribua o **Username** valor o mesmo valor da **nome de utilizador** no Azure AD. Agora tem de estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com Shmoop para escolas, conclua os blocos modulares seguintes:

1. [Configurar o Azure AD-início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar esta funcionalidade.
2. [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) para testar do Azure AD-início de sessão único com Britta Simon.
3. [Criar um utilizador de teste Shmoop para escolas](#create-a-shmoop-for-schools-test-user) para ter um homólogo de Britta Simon no Shmoop para escolas que está ligada a representação do Azure AD do utilizador.
4. [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. [Teste o início de sessão único](#test-single-sign-on) para verificar que a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Shmoop para escolas.

**Para configurar o Azure AD-início de sessão único com Shmoop para escolas, execute os seguintes passos:**

1. No portal do Azure, no **Shmoop para escolas** página de integração de aplicações, selecione **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, no menu pendente em **modo de início de sessão único**, selecione **baseados em SAML início de sessão**.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. No **Shmoop para escolas com o domínio e os URLs** secção, siga os passos seguintes:

    ![Configurar o início de sessão único](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. No **URL de início de sessão** caixa, escreva um URL com o padrão do seguinte:`https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. No **identificador** caixa, escreva um URL com o padrão do seguinte:`https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão real e o identificador. Contacte o [equipa de suporte de cliente de Shmoop para escolas](mailto:support@shmoop.com) para obter estes valores. 
 
4. A aplicação de Shmoop para escolas espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Captura de ecrã seguinte mostra como configurar as asserções:

    ![Configurar o início de sessão único](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop para escola suporta duas funções para os utilizadores: **professor** e **estudante**. Configure estas funções no Azure AD para que os utilizadores podem ser atribuídos as funções adequadas. Para saber como configurar as funções no Azure AD, consulte [controlo de acesso baseado em funções na aplicações em nuvem com o Azure AD](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/).
    
5. No **atributos de utilizador** secção o **de sessão único-** diálogo caixa, configure o atributo de token SAML, conforme mostrado na imagem anterior.  Em seguida, siga os passos seguintes:

    | Nome do atributo | Valor do atributo |
    | -------------- | --------------- |
    | Função           | User.assignedroles |

    a. Para abrir o **adicionar atributo** caixa de diálogo, selecione **adicionar atributo**.
    
    ![Configurar o início de sessão único ](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o início de sessão único](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. No **nome** caixa, escreva o nome do atributo que é apresentado para essa linha.
    
    c. Do **valor** lista, selecione o valor do atributo que é apresentado para essa linha.

    d. Deixe o **espaço de nomes** caixa vazia.
    
    e. Selecione **Ok**.

6. Selecione o botão **Guardar**.

    ![Configurar o início de sessão único](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_400.png)

7. Para gerar o **metadados** URL, siga os passos seguintes:

    a. Selecione **registos de aplicação**.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appregistrations.png)
   
    b. Para abrir o **pontos finais** caixa de diálogo, selecione **pontos finais**.  
    
    ![Configurar o início de sessão único](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpointicon.png)

    c. Selecione o botão Copiar para copiar o **documento de METADADOS de Federação** URL e cole-o bloco de notas.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_endpoint.png)
     
    d. Aceda à página de propriedades do **Shmoop para escolas**. Em seguida, copie o **ID da aplicação** utilizando o **cópia** botão. Cole-o bloco de notas.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_appid.png)

    e. Gerar o **URL de metadados** utilizando o padrão do seguinte: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`.   

8. Para configurar o início de sessão único no **Shmoop para escolas** lado, terá de enviar o **URL de metadados** para o [Shmoop para escolas suporta equipa](mailto:support@shmoop.com).

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com) enquanto estiver a configurar a aplicação. Depois de adicionar esta aplicação a partir de **do Active Directory** > **aplicações empresariais** secção, selecione o **Single Sign-On** separador e aceder a incorporados documentação através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados em [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste chamado Britta Simon no portal do Azure.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, siga os passos seguintes:**

1. No portal do Azure, no painel esquerdo, selecione o **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**. Em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, selecione **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, siga os passos seguintes:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-shmoopforschools-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Selecione **Criar**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Criar um utilizador de teste Shmoop para escolas

O objetivo desta secção consiste em criar um utilizador chamado Britta Simon Shmoop para escolas. Shmoop para escolas suporta o aprovisionamento de just-in-time, que está ativada por predefinição. Não há nenhum item de ação para si nesta secção. Se um novo utilizador ainda não existir, é criada durante a tentativa de aceder Shmoop para escolas.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, contacte o [Shmoop para escolas suporta equipa](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso para Shmoop para escolas.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a Shmoop para escolas, siga os passos seguintes:**

1. No portal do Azure, abra a vista de aplicações. Em seguida, aceda a **aplicações empresariais** na vista de diretório.  Em seguida, selecione **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Shmoop para escolas**.

    ![A ligação de Shmoop para escolas na lista de aplicações](./media/active-directory-saas-shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Selecione o **adicionar** botão. Em seguida, no **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. No **utilizadores e grupos** caixa de diálogo, clique em de **selecione** botão. 

7. No **adicionar atribuição** caixa de diálogo, selecione o **atribuir** botão.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

Nesta secção, teste configuração do Azure AD único início de sessão utilizando o painel de acesso.

Quando seleciona o **Shmoop para escolas** na peça de mosaico do painel de acesso, que deve obter automaticamente sessão na sua aplicação Shmoop para escolas.

Para mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shmoopforschools-tutorial/tutorial_general_203.png

