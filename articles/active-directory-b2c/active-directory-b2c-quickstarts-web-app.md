---
title: "Unidade de teste de uma aplicação web do Azure AD B2C | Microsoft Docs"
description: "Unidade de teste a iniciar sessão, inscreva-se, editar o perfil e repor percursos de utilizador de palavra-passe utilizando o ambiente de teste do Azure AD B2C"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 2a51f15fd38a901548dcf4c56922f9715c328463
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-web-application-configured-with-azure-ad-b2c"></a>Testar uma aplicação web configurada com o Azure AD B2C

O Azure Active Directory B2C fornece gestão de identidades de nuvem para manter as suas aplicações, empresas, os clientes protegidos.  Este guia de introdução utiliza uma aplicação de lista de tarefas de exemplo para demonstrar:

* Utilizar o **inscrever-se ou iniciar sessão no** política para criar ou inicie sessão com um fornecedor de identidade de redes sociais ou uma conta local utilizando um endereço de correio eletrónico. 
* Chamar uma API protegida pelo Azure AD B2C para criar e editar itens de tarefas.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - **Desenvolvimento do ASP.NET e Web**

* Uma conta de redes social do Facebook, Google, Microsoft ou Twitter. Se não tiver uma conta de redes social, é necessário um endereço de correio eletrónico válido.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

[Transferir ou clonar a aplicação de exemplo](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) a partir do GitHub.

## <a name="run-the-app-in-visual-studio"></a>Executar a aplicação no Visual Studio

Na pasta de projeto de aplicação de exemplo, abra o `B2C-WebAPI-DotNet.sln` solução no Visual Studio. 

A solução consiste em dois projetos:

* **TaskWebApp** – aplicação de web de MVC do ASP.NET uma em que um utilizador pode gerir os seus itens de lista de tarefas.  
* **TaskService** – efetuada back-end de uma API Web do ASP.NET que gere todas as operações CRUD nos itens de lista de tarefas de um utilizador. A aplicação web chama esta API e apresenta os resultados.

Para este guia de introdução, tem de executar ambos os `TaskWebApp` e `TaskService` projetos em simultâneo. 

1. No Explorador de soluções, clique com botão direito na solução e selecione **definir projetos de arranque...** . 
2. Selecione **vários projetos de arranque** botão de opção.
3. Alterar o **ação** para ambos os projetos para **iniciar**. Clique em **OK**.

![Definir página de arranque no Visual Studio](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Selecione **depurar > Iniciar depuração** para compilar e executar ambas as aplicações. Cada aplicação abre-se no seu próprio separador do browser:

* `https://localhost:44316/`-Esta página é a aplicação web do ASP.NET. Interagir diretamente com esta aplicação no início rápido.
* `https://localhost:44332/`-Esta página é a API web que é chamado pela aplicação web ASP.NET.

## <a name="create-an-account"></a>Criar uma conta

Clique em de **inscrever / início de sessão** ligação na aplicação web ASP.NET para iniciar o **inscrever-se ou iniciar sessão no** fluxo de trabalho. Ao criar uma conta, pode utilizar uma conta do fornecedor de identidade sociais existente ou uma conta de e-mail.

![Aplicação de web de ASP.NET de exemplo](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Inscrever-se utilizando um fornecedor de identidade de redes sociais

Para inscrever-se utilizando um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar. Se preferir usar um endereço de e-mail, avance para o [inscrever-se utilizando um endereço de correio eletrónico](#sign-up-using-an-email-address) secção.

![Fornecedor de sessão ou inscrever-se](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Tem de autenticar (início de sessão) com a sua conta de redes social credenciais e autorizar a aplicação a ler as informações da sua conta de redes social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de redes social, como o nome e a localidade. 

![Autenticar e autorizar utilizando uma conta de redes social](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

Conclua o processo de início de sessão para o fornecedor de identidade. Por exemplo, clique em **sessão** botão para Twitter.

Os detalhes do perfil de conta nova são pré-preenchidos com informações da sua conta de redes social.

![Detalhes da nova conta de perfil de inscrição](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

Atualize os campos de nome a apresentar, cargo e Cidade e clique em **continuar**.  Os valores que introduzir são utilizados para o seu perfil de conta de utilizador do Azure AD B2C.

Foi criada com êxito uma nova conta de utilizador do Azure AD B2C utiliza um fornecedor de identidade. 

Passo seguinte: [Ir para ver as afirmações](#view-your-claims) secção.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se utilizando um endereço de e-mail

Se optar por não utilizar uma conta de redes social para fornecer autenticação, pode criar uma conta de utilizador do Azure AD B2C, utilizando um endereço de correio eletrónico válido. Uma conta de utilizador local do Azure AD B2C utiliza o Azure Active Directory como o fornecedor de identidade. Para utilizar o seu endereço de e-mail, clique em de **não tiver uma conta? Inscrever-se agora** ligação.

![Sessão ou inscrever-se por e-mail](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-email-web.png)

Introduza um endereço de correio eletrónico e clique em **enviar o código de verificação**. É necessário um endereço de e-mail válido para receber o código de verificação do Azure AD B2C. 

Introduza o código de verificação receber por e-mail e clique em **verificar código**.

Adicionar informações do seu perfil e clique em **criar**.

![Inscrever-se com a nova conta de correio eletrónico](media/active-directory-b2c-quickstarts-web-app/sign-up-new-account-profile-email-web.png)

Foi criada com êxito uma nova conta de utilizador local do Azure AD B2C.

## <a name="reset-your-password"></a>Repor a palavra-passe

Se tiver criado a conta com um endereço de e-mail, o Azure AD B2C tem a funcionalidade para permitir que os utilizadores para repor a palavra-passe. Para editar o perfil que criou, clique no nome do perfil na barra de menus e selecione **Repor palavra-passe**.

Verifique o seu endereço de e-mail, introduzindo-o e clicando em **enviar o código de verificação**. É enviado um código de verificação para o seu endereço de correio eletrónico.

Introduza o código de verificação que recebeu por correio eletrónico e clique em **verificar código**.

Depois de verificar o seu endereço de e-mail, clique em **continuar**.

Introduza a palavra-passe nova e clique em **continuar**.

## <a name="view-your-claims"></a>Ver as afirmações

Clique em **afirmações** na barra de menus de aplicação web para ver as afirmações associadas com a sua última ação. 

![Inscrever-se com a nova conta de correio eletrónico](media/active-directory-b2c-quickstarts-web-app/view-claims-sign-up-web.png)

Neste exemplo, a última ação foi para o *iniciar sessão ou inscrever-se* experiência. Tenha em atenção o **o tipo de afirmação** `http://schemas.microsoft.com/claims/authnclassreference` é `b2c_1_susi` que indica a última ação foi se inscrever ou iniciar sessão. Se a última ação tiver sido reposição de palavra-passe, o **o tipo de afirmação** seria `b2c_1_reset`.

## <a name="edit-your-profile"></a>Editar o seu perfil

O Azure Active Directory B2C fornece funcionalidades para permitir que os utilizadores atualizar os seus perfis. Na barra de menus de aplicação web, clique no nome de perfil e selecione **Editar perfil** para editar o perfil que criou.

![Editar perfil](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Alterar o **nome a apresentar** e **Cidade**.  Clique em **continuar** para atualizar o seu perfil.

![Atualizar perfil](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Tenha em atenção as atualizações de nome de apresentação na parte superior direita da página depois de alterar o seu nome. 

Clique em **afirmações**. As alterações que efetuou para **nome a apresentar** e **Cidade** são refletidas nas afirmações.

![Afirmações de vista](media/active-directory-b2c-quickstarts-web-app/view-claims-update-web.png)

 Tenha em atenção o **o tipo de afirmação** `http://schemas.microsoft.com/claims/authnclassreference` foi atualizado para `b2c_1_edit_profile` que indica a última ação efetuada foi uma edição de perfil. Tenha também em atenção, o nome e a Localidade são os novos valores *Sara s-.* e *Seattle*.

## <a name="access-a-resource"></a>Aceder a um recurso

Clique em **lista de tarefas** para introduzir e modificar os itens de lista de tarefas. A aplicação web do ASP.NET inclui um token de acesso no pedido para o web permissão de requerente de recursos de API para efetuar operações em itens de lista de tarefas do utilizador. 

Introduza o texto a **Novo Item** caixa de texto. Clique em **adicionar** chamar o Azure AD B2C, protegida API web que adiciona um item de lista de tarefas.

![Adicionar um item de lista de tarefas](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

## <a name="other-scenarios"></a>Outros cenários

Outros cenários para testar disco são as seguintes:

* Terminar a sessão de aplicação e clique em **lista de tarefas**. Repare como lhe for pedido para iniciar sessão e os itens de lista são mantidos. 
* Crie uma nova conta com um tipo diferente da conta. Por exemplo, utilize um fornecedor de identidade sociais se tiver criado uma conta com um endereço de e-mail anteriormente.

## <a name="next-steps"></a>Passos seguintes

O passo seguinte é criar o seu inquilino do Azure AD B2C e configurar o exemplo a serem executados utilizando o seu inquilino. 

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](active-directory-b2c-get-started.md)