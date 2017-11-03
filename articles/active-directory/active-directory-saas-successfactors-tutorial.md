---
title: "Tutorial: Integração do Azure Active Directory com SuccessFactors | Microsoft Docs"
description: "Saiba como utilizar SuccessFactors com o Azure Active Directory para ativar o início de sessão único, aprovisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: e85a38ccbe25263ac42bc76351416b023fb77c87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Tutorial: Integração do Azure Active Directory com SuccessFactors
O objetivo deste tutorial é para lhe mostrar como integrar SuccessFactors com o Azure Active Directory (Azure AD).

Integrar SuccessFactors com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SuccessFactors
* Pode permitir que os utilizadores automaticamente obter com sessão iniciada para SuccessFactors (Single Sign-On) com as respetivas contas do Azure AD
* Pode gerir as contas numa localização central - portal clássico do Azure

Se pretender saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [que é o acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com SuccessFactors, terá dos seguintes itens:

* Uma subscrição do Azure válida
* Um inquilino na SuccessFactors

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.
> 
> 

Para testar os passos neste tutorial, deve seguir estas recomendações:

* Não deve utilizar o seu ambiente de produção, a menos que isto é necessário.
* Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir-lhe testar do Azure AD-início de sessão único num ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos modulares principais:

1. Adicionar SuccessFactors a partir da Galeria
2. Configurar e testar o Azure AD de sessão único-

## <a name="adding-successfactors-from-the-gallery"></a>Adicionar SuccessFactors a partir da Galeria
Para configurar a integração de SuccessFactors com o Azure AD, terá de adicionar SuccessFactors a partir da Galeria à sua lista de aplicações SaaS geridas.

**Para adicionar SuccessFactors a partir da galeria, execute os seguintes passos:**

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Configurar o início de sessão único][1]
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Configurar o início de sessão único][2]
4. Clique em **adicionar** na parte inferior da página.
   
    ![Aplicações][3]
5. No **que pretende fazer** caixa de diálogo, clique em **adicionar uma aplicação na galeria do**.
   
    ![Configurar o início de sessão único][4]
6. No **caixa pesquisa**, tipo **SuccessFactors**.
   
    ![Configurar o início de sessão único][5]
7. No painel de resultados, selecione **SuccessFactors**e, em seguida, clique em **concluída** para adicionar a aplicação.
   
    ![Configurar o início de sessão único][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD de sessão único-
O objetivo desta secção consiste em Mostrar-lhe como configurar e testar o Azure AD-início de sessão único com SuccessFactors com base num utilizador de teste chamado "Britta Simon".

Para início de sessão trabalhar, do Azure AD tem de saber o que é o utilizador homólogo SuccessFactors para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SuccessFactors tem de ser estabelecida.

Esta relação de ligação é estabelecida ao atribuir o valor da **nome de utilizador** no Azure AD como o valor a **Username** no SuccessFactors.

Para configurar e testar o Azure AD-início de sessão único com SuccessFactors, tem de concluir os blocos modulares seguintes:

1. **[Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)**  - para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD-início de sessão único com Britta Simon.
3. **[Criar um utilizador de teste SuccessFactors](#creating-a-successfactors-test-user)**  - para ter um homólogo de Britta Simon SuccessFactors que está ligada a representação do Azure AD da forma.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar Britta Simon utilizar o Azure AD-início de sessão único.
5. **[Teste o início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD-início de sessão único
Nesta secção, pode ativar do Azure AD início de sessão no portal clássico e configurar o início de sessão único na sua aplicação SuccessFactors.

**Para configurar o Azure AD-início de sessão único com SuccessFactors, execute os seguintes passos:**

1. No portal clássico do Azure, no **SuccessFactors** página de integração de aplicações, clique em **configurar o início de sessão único** para abrir o **configurar início de sessão único** caixa de diálogo.
   
    ![Configurar o início de sessão único][7]
2. No **como gostaria aos utilizadores iniciar sessão SuccessFactors** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
   
    ![Configurar o início de sessão único][8]
3. No **URL da aplicação configurar** página, execute os passos seguintes e, em seguida, clique em **seguinte**.
   
    ![Configurar o início de sessão único][9]
   
    a. No **URL de início de sessão** caixa de texto, escreva um URL utilizando um dos padrões de seguintes: 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
   
    b. No **URL de resposta** caixa de texto, escreva um URL utilizando um dos padrões de seguintes: 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
    | `https://<company name>.sapsf.eu/<company name>` |
   
    c. Clique em **Seguinte**. 

    > [!NOTE]
    > Tenha em atenção que estas não são os valores reais. Tem de atualizar estes valores com o URL de início de sessão e o URL de resposta real. Para obter estes valores, contacte [SuccessFactors suporta equipa](https://www.successfactors.com/en_us/support.html).

1. No **configurar o início de sessão único em SuccessFactors** página, clique em **Transferir certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.
   
    ![Configurar o início de sessão único][10]

2. Numa janela do browser web diferente, inicie sessão no seu **portal de administração de SuccessFactors** como administrador.

3. Visite **segurança da aplicação** e nativa **início de sessão único na funcionalidade**. 

4. Coloque qualquer valor no **repor Token** e clique em **guardar Token** para ativar SAML SSO.
   
    ![Configurar o início de sessão único no lado de aplicação][11]

    > [!NOTE] 
    > Este valor é utilizado apenas como o comutador /. Se qualquer valor for guardado, SAML SSO está ON. Se um valor em branco é guardado SAML SSO está OFF.

1. Nativo para baixo captura de ecrã e efetuar as seguintes ações.
   
    ![Configurar o início de sessão único no lado de aplicação][12]
   
    a. Selecione o **SAML v2 SSO** botão de opção
   
    b. Defina o SAML Asserting terceiros Name(e.g. SAml issuer + company name).
   
    c. No **SAML emissor** textbox colocar o valor de **URL do emissor** do Assistente de configuração de aplicações do Azure AD.
   
    d. Selecione **resposta (cliente gerado/IdP/AP)** como **exigir assinatura obrigatória**.
   
    e. Selecione **ativada** como **ativar SAML sinalizador**.
   
    f. Selecione **não** como **assinatura do pedido de início de sessão (SF gerado/SP/RP)**.
   
    g. Selecione **Browser/Post perfil** como **perfil SAML**.
   
    h. Selecione **não** como **impor período válido de certificado**.
   
    posso. Copiar o conteúdo do ficheiro de certificado transferido e, em seguida, cole-o para o **SAML verificar certificado** caixa de texto.

    > [!NOTE] 
    > O conteúdo de certificado tem de ter começar etiquetas de certificado do certificado e de fim.

1. Navegue para SAML V2 e, em seguida, execute os seguintes passos:
   
    ![Configurar o início de sessão único no lado de aplicação][13]
   
    a. Selecione **Sim** como **suporta iniciado por SP Global terminar**.
   
    b. No **Global URL do serviço fim de sessão (LogoutRequest destino)** textbox colocar o valor de **URL de fim de sessão remoto** do Assistente de configuração de aplicações do Azure AD.
   
    c. Selecione **não** como **requerem sp tem de encriptar todos os NameID elemento**.
   
    d. Selecione **não especificado** como **NameID formato**.
   
    e. Selecione **Sim** como **ativar SP2 iniciada pelo início de sessão (AuthnRequest)**.
   
    f. No **pedido de envio como emissor de toda a empresa** textbox colocar o valor de **URL de início de sessão remoto** do Assistente de configuração de aplicações do Azure AD.
2. Execute estes passos, se pretender que os nomes de utilizador de início de sessão sensível,.
   
    a. Visite **definições da empresa**(perto da parte inferior).
   
    b. Selecione a caixa de verificação junto **ativar o nome de utilizador de caso de não-sensíveis**.
   
    c.Click **guardar**.
   
    ![Configurar o início de sessão único][29]

    > [!NOTE] 
    > Se tentar ativar esta opção, o sistema verifica se criará um nome de início de sessão SAML duplicado. Por exemplo, se o cliente tem nomes de utilizador User1 e user1. Colocar ausente sensibilidade às maiúsculas e minúsculas torna estas duplicados. O sistema irá dar-lhe uma mensagem de erro e não irá ativar a funcionalidade. O cliente terá de alterar uma os nomes de utilizador para que, na verdade, está escrito diferentes. 

1. No portal clássico do Azure, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **concluída** para fechar o **configurar início de sessão único** caixa de diálogo.
   
    ![Aplicações][14]
2. No **único início de sessão confirmação** página, clique em **concluída**.
   
    ![Aplicações][15]

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção consiste em criar um utilizador de teste no portal clássico do chamado Britta Simon.

![Criar utilizador do Azure AD][16]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássico do Azure**, no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Criar um utilizador de teste do Azure AD][17]
2. Do **diretório** lista, selecione o diretório para o qual pretende ativar a integração de diretórios.
3. Para apresentar a lista de utilizadores, no menu na parte superior, clique em **utilizadores**.
   
    ![Criar um utilizador de teste do Azure AD][18]
4. Para abrir o **adicionar utilizador** caixa de diálogo, na barra de ferramentas na parte inferior, clique em **adicionar utilizador**.
   
    ![Criar um utilizador de teste do Azure AD][19]
5. No **diga-nos informações sobre este utilizador** diálogo página, execute os seguintes passos:
   
    ![Criar um utilizador de teste do Azure AD][20]
   
    a. Como tipo de utilizador, selecione o novo utilizador na sua organização.
   
    b. O nome de utilizador **textbox**, tipo **BrittaSimon**.
   
    c. Clique em **Seguinte**.
6. No **perfil de utilizador** diálogo página, execute os seguintes passos:
   
    ![Criar um utilizador de teste do Azure AD][21]
   
    a. No **nome próprio** caixa de texto, tipo **Britta**.  
   
    b. No **Apelido** caixa de texto, tipo, **Simon**.
   
    c. No **nome a apresentar** caixa de texto, tipo **Britta Simon**.
   
    d. No **função** lista, selecione **utilizador**.
   
    e. Clique em **Seguinte**.
7. No **Get palavra-passe temporária** página da caixa de diálogo, clique em **criar**.
   
    ![Criar um utilizador de teste do Azure AD][22]
8. No **Get palavra-passe temporária** diálogo página, execute os seguintes passos:
   
    ![Criar um utilizador de teste do Azure AD][23]
   
    a. Anote o valor da **nova palavra-passe**.
   
    b. Clique em **Concluído**.  

### <a name="creating-a-successfactors-test-user"></a>Criar um utilizador de teste SuccessFactors
Para permitir que os utilizadores do Azure AD sessão SuccessFactors, têm de ser aprovisionados para SuccessFactors.  
No caso de SuccessFactors, o aprovisionamento é uma tarefa manual.

Para obter os utilizadores criados no SuccessFactors, terá de contactar o [SuccessFactors suporta equipa](https://www.successfactors.com/en_us/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD
O objetivo desta secção consiste em Ativar Britta Simon a utilizar Azure-início de sessão único, conceder o acesso ao SuccessFactors.

![Atribua o utilizador][24]

**Para atribuir Britta Simon a SuccessFactors, execute os seguintes passos:**

1. No portal clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
   
    ![Atribua o utilizador][25]
2. Na lista de aplicações, selecione **SuccessFactors**.
   
    ![Configurar o início de sessão único][26]
3. No menu na parte superior, clique em **utilizadores**.
   
    ![Atribua o utilizador][27]
4. Na lista de utilizadores, selecione **Britta Simon**.
5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
   
    ![Atribua o utilizador][28]

### <a name="testing-single-sign-on"></a>Teste o início de sessão único
O objetivo desta secção consiste em testar a configuração do Azure AD único início de sessão através do painel de acesso.

Quando clica no mosaico SuccessFactors no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de SuccessFactors.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png
