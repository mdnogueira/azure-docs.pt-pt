---
title: "Tutorial: Integração do Azure Active Directory com o Amazon Web Services (AWS) | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e o Amazon Web Services (AWS)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 0fb9c8f428368271b548e3f174726fa01ea910c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutorial: Integração do Azure Active Directory com o Amazon Web Services (AWS)

Neste tutorial, irá aprender a integrar o Amazon Web Services (AWS) com o Azure Active Directory (Azure AD).

Integrar o Amazon Web Services (AWS) com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso para o Amazon Web Services (AWS)
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Amazon Web Services (AWS) (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Amazon Web Services (AWS), terá dos seguintes itens:

- Uma subscrição do Azure AD
- Amazon Web Services (AWS) início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, teste do Azure AD-início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar Amazon Web Services (AWS) a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar Amazon Web Services (AWS) a partir da Galeria
Para configurar a integração do Amazon Web Services (AWS) com o Azure AD, terá de adicionar Amazon Web Services (AWS) a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Amazon Web Services (AWS) a partir da galeria, execute os seguintes passos:**

1. No  **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone. 

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todas as aplicações**.

    ![Aplicações][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **Amazon Web Services (AWS)**.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. No painel de resultados, selecione **Amazon Web Services (AWS)**e, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
Nesta secção, configure e teste do Azure AD-início de sessão único com o Amazon Web Services (AWS) com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que o utilizador homólogo nos Amazon Web Services (AWS) é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos Amazon Web Services (AWS) tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** nos Amazon Web Services (AWS).

Para configurar e testar o Azure AD-início de sessão único com o Amazon Web Services (AWS), tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste do Amazon Web Services](#creating-an-amazon-web-services-test-user)**  - para ter um homólogo de Britta Simon nos Amazon Web Services (AWS) que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão no portal do Azure e configurar o início de sessão único na sua aplicação Amazon Web Services (AWS).

**Para configurar o Azure AD-início de sessão único com o Amazon Web Services (AWS), execute os seguintes passos:**

1. No Portal do Azure, no **Amazon Web Services (AWS)** página de integração de aplicações, clique em **de sessão único-**.

    ![Configurar o início de sessão único][4]

2. No **de sessão único-** caixa de diálogo, como **modo** selecione **baseados em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. No **Amazon Web Services (AWS) domínios e URLs** secção, o utilizador não tem de efetuar quaisquer passos, tal como a aplicação já está pré-integrada com o Azure.

    ![Configurar o início de sessão único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. No **certificado de assinatura de SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML no seu computador.
    
    ![Configurar o início de sessão único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. A aplicação de Software do Amazon Web Services (AWS) espera as asserções de SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores destes atributos a partir de "**atributos de utilizador**" secção na página de integração de aplicações. A seguinte captura de ecrã mostra um exemplo para este.

    ![Configurar o início de sessão único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. No **atributos de utilizador** secção no **de sessão único-** caixa de diálogo, configurar atributos token SAML, conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome do atributo  | Valor do atributo | Espaço de nomes |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://AWS.amazon.com/SAML/Attributes |
    | Função            | User.assignedroles |  https://AWS.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Terá de configurar o aprovisionamento de utilizador no Azure AD para obter todas as funções a partir da consola AWS. Consulte os passos de aprovisionamento abaixo.

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b. No **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    ![Configurar o início de sessão único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. Do **valor** lista, digite o valor de atributo apresentado para essa linha. Adicione o valor de espaço de nomes como indicado acima.
    
    d. Clique em **OK**.

7. Clique em **guardar** botão para guardar as definições no Azure.

    ![Configurar o início de sessão único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. Numa janela do browser diferente, início de sessão no site da sua empresa Amazon Web Services (AWS) como administrador.

9. Clique em **consola Home**.
   
    ![Configurar o início de sessão único][11]

10. Clique em **IAM** de **segurança, de identidade e de conformidade** serviço.
   
    ![Configurar o início de sessão único][12]

11. Clique em **fornecedores de identidade**e, em seguida, clique em **criar fornecedor**.
   
    ![Configurar o início de sessão único][13]

12. No **Configurar fornecedor** diálogo página, execute os seguintes passos:
   
    ![Configurar o início de sessão único][14]
 
    a. Como **tipo de fornecedor**, selecione **SAML**.

    b. No **nome do fornecedor** caixa de texto, escreva um nome de fornecedor (por ex.: *WAAD*).

    c. Para carregar o ficheiro de metadados transferido, clique em **Escolher ficheiro**.

    d. Clique em **passo seguinte**.

13. No **verificar informações do fornecedor** página da caixa de diálogo, clique em **criar**. 
    
    ![Configurar o início de sessão único][15]

14. Clique em **funções**e, em seguida, clique em **criar nova função**. 
    
    ![Configurar o início de sessão único][16]

15. No **definir o nome da função** caixa de diálogo, execute os seguintes passos: 
    
    ![Configurar o início de sessão único][17] 

    a. No **nome da função** caixa de texto, escreva um nome de função (por ex.: *TestUser*). 

    b. Clique em **passo seguinte**.

16. No **selecionar tipo de função** caixa de diálogo, execute os seguintes passos: 
    
    ![Configurar o início de sessão único][18] 

    a. Selecione **função para acesso do fornecedor de identidade**. 

    b. No **acesso conceder Web Single Sign-On (WebSSO) para fornecedores de SAML** secção, clique em **selecione**.

17. No **estabelecer confiança** caixa de diálogo, execute os seguintes passos:  
    
    ![Configurar o início de sessão único][19] 

    a. Como fornecedor SAML, selecione o fornecedor SAML que criou anteriormente (por ex.: *WAAD*)
  
    b. Clique em **passo seguinte**.

18. No **verificar função confiar** caixa de diálogo, clique em **passo seguinte**.
    
    ![Configurar o início de sessão único][32]

19. No **anexar política** caixa de diálogo, clique em **passo seguinte**.
    
    ![Configurar o início de sessão único][33]

20. No **revisão** caixa de diálogo, execute os seguintes passos:
    
    ![Configurar o início de sessão único][34]
 
    a. Clique em **criar função**.

    b. Criar funções tantos conforme necessário e mapeá-los para o fornecedor de identidade.

21. Configurar agora o utilizador aprovisionamento para obter todas as funções do AWS

    a. No início de sessão do consola AWS com a sua conta de raiz

    b. No canto superior direito, clique em seu nome e, em seguida, clique em de **minhas credenciais de segurança** opção. Esta ação abre um ecrã como uma mensagem de aviso. Clique no botão **credenciais de segurança** botão para passar o ecrã.
        
       ![Configurar o início de sessão único][36]

       ![Configurar o início de sessão único][37]

    c. Na secção chaves de acesso clique o **criar nova chave de acesso** botão. Isto gera o ID de chave de acesso e um valor de token.
    
       ![Configurar o início de sessão único][38]

    d. Copiar estes valores e também transferi-la, para que não perder.

    e. No Portal do Azure, na página de integração de aplicações do Amazon Web Services (AWS), clique em **aprovisionamento**.
        
       ![Configurar o início de sessão único][35]

    f. Definir o modo de aprovisionamento **automática**
        
       ![Configurar o início de sessão único][39]

    g. Agora no **clientsecret** e **segredo Token** colar os valores correspondentes, o qual copiou a partir da consola AWS.
    
    h. Pode clicar no **Testar ligação** botão para testar a conectividade. Uma vez que for bem sucedida, em seguida, pode iniciar o conector de aprovisionamento.
       
       ![Configurar o início de sessão único][40]

    posso. Ativar agora o estado de aprovisionamento para **no**. Esta ação inicia a obter as funções da aplicação.

       ![Configurar o início de sessão único][41]

    > [!NOTE]
    > Execuções de serviço do Azure AD aprovisionamento cada após algum tempo a sincronizar as funções do AWS. Deverá ver o fornecedor de identidade ligado funções AWS com o Azure AD e pode utilizá-los ao atribuir a aplicação a utilizadores ou grupos.

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal do Azure chamado Britta Simon.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique em **do Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. No **utilizador** diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    a. No **nome** caixa de texto, tipo **BrittaSimon**.

    b. No **nome de utilizador** caixa de texto, tipo de **endereço de correio eletrónico** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e anote o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-amazon-web-services-test-user"></a>Criar um utilizador de teste do Amazon Web Services

Para ativar o utilizadores do Azure AD para início de sessão para o Amazon Web Services (AWS), deve ser aprovisionado para o Amazon Web Services (AWS). No caso do Amazon Web Services (AWS), o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Amazon Web Services (AWS)** site da empresa como administrador.

2. Clique em de **consola Home** ícone. 
   
    ![Configurar o início de sessão único][11]

3. Clique na identidade e gestão de acesso. 
   
    ![Configurar o início de sessão único][28]

4. No Dashboard, clique em **utilizadores**e, em seguida, clique em **criar novos utilizadores**. 
   
    ![Configurar o início de sessão único][29]

5. Na caixa de diálogo Criar utilizador, execute os seguintes passos: 
   
    ![Configurar o início de sessão único][30]   
    
    a. No **Introduza nomes de utilizador** caixas de texto, escreva o nome de utilizador de Brita Simon (userprincipalname) no Azure AD.

    b. Clique em **criar.**
        
### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Britta Simon utilizar o Azure-início de sessão único, concedendo respetivo acesso para o Amazon Web Services (AWS).

![Atribua o utilizador][200] 

**Para atribuir Britta Simon para Amazon Web Services (AWS), execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicações e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todas as aplicações**.

    ![Atribua o utilizador][201] 

2. Na lista de aplicações, selecione **Amazon Web Services (AWS)**.

    ![Configurar o início de sessão único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribua o utilizador][202] 

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Atribua o utilizador][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Britta Simon** na lista utilizadores.

6. Clique em **selecione** botão no **utilizadores e grupos** caixa de diálogo.

7. No **selecionar função** separador, selecione a função adequada para o utilizador. Todas estas funções são apresentadas com o nome de função e o nome do fornecedor de identidade. Desta forma pode identificar facilmente as funções do AWS.

8. Clique em **atribuir** botão no **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste o início de sessão único

Nesta secção, testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico do Amazon Web Services (AWS) no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação do Amazon Web Services (AWS). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
