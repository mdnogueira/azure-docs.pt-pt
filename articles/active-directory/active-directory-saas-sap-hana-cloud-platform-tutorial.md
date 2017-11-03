---
title: "Tutorial: Integração do Azure Active Directory com a plataforma de nuvem do SAP | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e a plataforma de nuvem."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 6b4b0e3ab126e70e4b27b971b15127aae0486bed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Integração do Azure Active Directory com a plataforma de nuvem do SAP

Neste tutorial, irá aprender a integrar o SAP plataforma de nuvem com o Azure Active Directory (Azure AD).

Integrar o SAP plataforma de nuvem com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso à plataforma de nuvem do SAP.
- Pode permitir aos utilizadores automaticamente obter com sessão iniciada para SAP Cloud Platform (Single Sign-On) com as respetivas contas do Azure AD.
- Pode gerir as contas numa localização central - portal do Azure.

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a plataforma de nuvem do SAP, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma plataforma de nuvem do SAP-início de sessão único ativada subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que atribuiu à plataforma de nuvem do SAP será capazes de início de sessão único para a aplicação a utilizar o [introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Tem de implementar a sua própria aplicação ou subscrever uma aplicação na sua conta de plataforma de nuvem do SAP para testar o início de sessão único. Neste tutorial, uma aplicação é implementada na conta.
> 

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. A adição de plataforma de nuvem de SAP da galeria do
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>A adição de plataforma de nuvem de SAP da galeria do
Para configurar a integração da plataforma de nuvem do SAP com o Azure AD, tem de adicionar plataforma de nuvem de SAP da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar a plataforma de nuvem de SAP da galeria do, execute os seguintes passos:**

1. No  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![O painel de aplicações da empresa][2]
    
3. Para adicionar a nova aplicação, clique em **nova aplicação** botão no topo da caixa de diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **SAP Cloud Platform**, selecione **SAP Cloud Platform** partir do painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Plataforma de nuvem SAP na lista de resultados](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD-início de sessão único

Nesta secção, configure e teste do Azure AD-início de sessão único com a plataforma de nuvem com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo na plataforma de nuvem do SAP é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na plataforma de nuvem do SAP tem de ser estabelecida.

Na plataforma de nuvem do SAP, atribua o valor do **nome de utilizador** no Azure AD como o valor a **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD-início de sessão único com a plataforma de nuvem do SAP, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste de SAP Cloud Platform](#create-a-sap-cloud-platform-test-user)**  - para ter um homólogo de Britta Simon na plataforma de nuvem de SAP que está ligado a representação do Azure AD do utilizador.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação da plataforma de nuvem do SAP.

**Para configurar o Azure AD-início de sessão único com a plataforma de nuvem do SAP, execute os seguintes passos:**

1. No portal do Azure, no **SAP Cloud Platform** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar a ligação de início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, selecione **modo** como **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. No **SAP domínio da plataforma de nuvem e URLs** secção, execute os seguintes passos:

    ![Domínio SAP da plataforma de nuvem e os URLs únicos de informações de início de sessão](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. No **URL de início de sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para iniciar sessão na sua **SAP Cloud Platform** aplicação. Este é o URL da conta específica de um recurso protegido na sua aplicação da plataforma de nuvem do SAP. O URL é baseado no padrão do seguinte:`https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Este é o URL na aplicação SAP Cloud Platform que requer o autenticação do utilizador.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. No **identificador** irá fornecer a sua plataforma de nuvem de SAP escreva um URL utilizando um dos seguintes padrões de caixa de texto: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. No **URL de resposta** caixa de texto, escreva um URL a utilizar o padrão do seguinte:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Estes valores não estiverem reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente de plataforma de nuvem do SAP](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obter o URL de início de sessão e o identificador. URL de resposta, pode obter da secção de gestão de fidedignidade que é explicada mais tarde no tutorial.
    > 
     
4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![A hiperligação de transferência do certificado](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar botão único início de sessão guardar](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. Numa janela do browser web diferente, inicie sessão no Cockpit de plataforma de nuvem SAP em `https://account.<landscape host>.ondemand.com/cockpit`(por exemplo: https://account.hanatrial.ondemand.com/cockpit).

7. Clique em de **confiar** separador.
   
    ![Confiança](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "confiar")

8. Na secção de gestão de confiança, em **fornecedor de serviços locais**, execute os seguintes passos:

    ![Gestão de confiança](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "gestão de confiança")
   
    a. Clique em **Editar**.

    b. Como **tipo de configuração**, selecione **personalizada**.

    c. Como **nome Local do fornecedor**, deixe o valor predefinido. Este valor de copiar e colar no **identificador** campo na configuração do Azure AD para a plataforma de nuvem do SAP.

    d. Para gerar um **chave de assinatura** e um **certificado de assinatura** par de chaves, clique em **gerar par de chaves**.

    e. Como **propagação Principal**, selecione **desativado**.

    f. Como **Force autenticação**, selecione **desativado**.

    g. Clique em **Guardar**.

9. Depois de guardar o **fornecedor de serviços locais** definições, efetue o seguinte para obter o URL de resposta:
   
    ![Obter metadados](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "obter metadados")

    a. Transfira o ficheiro de metadados de plataforma de nuvem do SAP clicando **obter metadados**.

    b. Abra o ficheiro XML de metadados SAP Cloud Platform transferido e, em seguida, localize o **ns3:AssertionConsumerService** etiquetas.
 
    c. Copie o valor do **localização** de atributos e, em seguida, cole-o para o **URL de resposta** campo na configuração do Azure AD para a plataforma de nuvem do SAP.

10. Clique em de **fidedigna fornecedor de identidade** separador e, em seguida, clique em **Adicionar fornecedor de identidade fidedigna**.
   
    ![Gestão de confiança](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "gestão de confiança")
   
    >[!NOTE]
    >Para gerir a lista de fornecedores de identidade fidedigno, terá de tiver escolhido o tipo de configuração personalizada, na secção de fornecedor de serviços Local. Para o tipo de configuração predefinida, terá de uma fidedignidade não for editável e implícita para o serviço de ID de SAP. Para None, não tem quaisquer definições de fidedignidade.
    > 
    > 

11. Clique em de **geral** separador e, em seguida, clique em **procurar** para carregar o ficheiro de metadados transferido.
    
    ![Gestão de confiança](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "gestão de confiança")
    
    >[!NOTE]
    >Depois de carregar o ficheiro de metadados, os valores para **URL de início de sessão único**, **URL de fim de sessão único**, e **certificado de assinatura** são povoadas automaticamente.
    > 
     
12. Clique no separador **Atributos**.

13. No **atributos** separador, executar o passo seguinte:
    
    ![Atributos](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "atributos") 

    a. Clique em **Add Assertion-Based atributo**e, em seguida, adicione os seguintes atributos baseado na asserção:
       
    | Atributo de asserção | Atributo principal |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |nome próprio |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Apelido |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |Correio eletrónico |
   
     >[!NOTE]
     >A configuração dos atributos depende da forma como a aplicação (ões) no SCP é desenvolvidas, ou seja, os atributos pode esperar da resposta SAML e sob o nome (atributo Principal) acederem a este atributo com o código.
     > 
    
    b. O **atributo predefinido** na captura de ecrã é apenas para fins de ilustração. Não é necessário para tornar o cenário de trabalho.  
 
    c. Os nomes e valores para **atributo Principal** mostrado na captura de ecrã dependem da forma como a aplicação for desenvolvida. É possível que a aplicação requer mapeamentos diferentes.

###<a name="assertion-based-groups"></a>Grupos com base em asserção

Como um passo opcional, pode configurar grupos com base em asserção para o fornecedor de identidade do Active Directory do Azure.

Utilizar grupos na plataforma de nuvem do SAP permite-lhe atribuir dinamicamente um ou mais utilizadores para uma ou mais funções nas suas aplicações de plataforma de nuvem do SAP, determinadas pela valores dos atributos na asserção SAML 2.0. 

Por exemplo, se a asserção contém o atributo "*contrato = temporário*", poderá ser útil afetados todos os utilizadores a adicionar ao grupo de"*temporário*". O grupo "*temporário*" pode conter uma ou mais funções de uma ou mais aplicações implementadas na sua conta de plataforma de nuvem do SAP.
 
Utilize grupos com base em asserção quando pretender atribuir em simultâneo vários utilizadores a uma ou mais funções de aplicações na sua conta de plataforma de nuvem do SAP. Se pretender atribuir apenas um único ou pequeno número de utilizadores a funções específicas, recomendamos que lhes atribuir diretamente no "**autorizações**" separador de cockpit a plataforma de nuvem do SAP.

> [!TIP]
> Pode agora ler estas instruções dentro de uma versão concisa o [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir do **do Active Directory > aplicações da empresa** secção, basta clicar no **Single Sign-On** separador e aceder à documentação do embedded através de **configuração** secção na parte inferior. Pode ler mais sobre a funcionalidade de documentação incorporados aqui: [do Azure AD incorporado documentação]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique em de **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e "Todos os utilizadores" ligações](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Para abrir o **utilizador** caixa de diálogo, clique em **adicionar** na parte superior do **todos os utilizadores** caixa de diálogo.

    ![O botão de adição](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. No **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. No **nome** caixa, escreva **BrittaSimon**.

    b. No **nome de utilizador** caixa, escreva o endereço de e-mail do utilizador Britta Simon.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Criar um utilizador de teste de SAP Cloud Platform

Para ativar os utilizadores do Azure AD iniciem sessão plataforma de nuvem do SAP, tem de atribuir funções na plataforma de nuvem SAP aos mesmos.

**Para atribuir uma função a um utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **SAP Cloud Platform** cockpit.

2. Execute o seguinte:
   
    ![Autorizações](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "autorizações")
   
    a. Clique em **autorização**.

    b. Clique em de **utilizadores** separador.

    c. No **utilizador** caixa de texto, escreva o endereço de e-mail do utilizador.

    d. Clique em **atribuir** atribuir o utilizador a uma função.

    e. Clique em **Guardar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo acesso à plataforma de nuvem do SAP.

![Atribuir a função de utilizador][200] 

**Para atribuir Britta Simon a plataforma de nuvem do SAP, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **SAP Cloud Platform**.

    ![A ligação de plataforma de nuvem do SAP na lista de aplicações](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação de "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Teste o início de sessão único

O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico de plataforma de nuvem do SAP no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação da plataforma de nuvem do SAP.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

