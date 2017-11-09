---
title: "Unidade de teste de uma aplicação web do Azure AD B2C ativada | Microsoft Docs"
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
ms.author: patricka
ms.openlocfilehash: 07f2c21409176d30f4570e267a4472745f843f85
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="test-drive-an-azure-ad-b2c-enabled-web-app"></a>Unidade de teste um Azure AD B2C ativada a aplicação web

O Azure Active Directory B2C fornece gestão de identidades de nuvem para manter as suas aplicações, empresas, os clientes protegidos. Este guia de introdução utiliza uma aplicação de lista de tarefas de exemplo para demonstrar:

> [!div class="checklist"]
> * Inicie sessão com uma página de início de sessão personalizada.
> * Inicie sessão com um fornecedor de identidade de redes sociais.
> * Criar e gerir o seu perfil de utilizador e conta do Azure AD B2C.
> * Chamar uma API web do protegidas pelo Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) com o **desenvolvimento ASP.NET e web** carga de trabalho. 
* Uma conta de redes social do Facebook, Google, Microsoft ou Twitter.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

[Transferir ou clonar a aplicação de exemplo](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) a partir do GitHub.

## <a name="run-the-app-in-visual-studio"></a>Executar a aplicação no Visual Studio

Na pasta de projeto de aplicação de exemplo, abra o `B2C-WebAPI-DotNet.sln` solução no Visual Studio. 

A solução é uma aplicação de lista de tarefas de exemplo consiste em dois projetos:

* **TaskWebApp** – aplicação de web de MVC do ASP.NET uma em que um utilizador pode gerir os seus itens de lista de tarefas.  
* **TaskService** – back-end de uma API Web do ASP.NET que gere operações efetuadas em itens de lista de tarefas de um utilizador. A aplicação web chama este API web e apresenta os resultados.

Para este guia de introdução, tem de executar ambos os `TaskWebApp` e `TaskService` projetos em simultâneo. 

1. No menu do Visual Studio, selecione **projetos > Definir projetos de arranque...** . 
2. Selecione **vários projetos de arranque** botão de opção.
3. Alterar o **ação** para ambos os projetos para **iniciar**. Clique em **OK**.

![Definir página de arranque no Visual Studio](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Selecione **depurar > Iniciar depuração** para compilar e executar ambas as aplicações. Cada aplicação abre-se no seu próprio separador do browser:

`https://localhost:44316/`-Esta página é a aplicação web do ASP.NET. Interagir diretamente com esta aplicação no início rápido.
`https://localhost:44332/`-Esta página é a API web que é chamado pela aplicação web ASP.NET.

## <a name="create-an-account"></a>Criar uma conta

Clique em de **inscrever / início de sessão** ligação na aplicação web ASP.NET para iniciar o **inscrever-se ou iniciar sessão no** fluxo de trabalho. Ao criar uma conta, pode utilizar uma conta do fornecedor de identidade sociais existente ou uma conta de e-mail. Para este início rápido, utilize uma conta do fornecedor de identidade de redes sociais do Facebook, Google, Microsoft ou Twitter.

![Aplicação de web de ASP.NET de exemplo](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Inscrever-se utilizando um fornecedor de identidade de redes sociais

Para inscrever-se utilizando um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar. 

![Fornecedor de sessão ou inscrever-se](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Tem de autenticar (início de sessão) com a sua conta de redes social credenciais e autorizar a aplicação a ler as informações da sua conta de redes social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de redes social, como o nome e a localidade. 

Conclua o processo de início de sessão para o fornecedor de identidade. Por exemplo, se tiver escolhido o Twitter, introduza as suas credenciais do Twitter e clique em **sessão**.

![Autenticar e autorizar utilizando uma conta de redes social](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

Os novo detalhes do perfil de conta do Azure AD B2C são pré-preenchidos com informações da sua conta de redes social.

Atualize os campos de nome a apresentar, cargo e Cidade e clique em **continuar**.  Os valores que introduzir são utilizados para o seu perfil de conta de utilizador do Azure AD B2C.

![Detalhes da nova conta de perfil de inscrição](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

Tem com êxito:

> [!div class="checklist"]
> * Autenticar através de um fornecedor de identidade.
> * Criar uma conta de utilizador do Azure AD B2C. 

## <a name="edit-your-profile"></a>Editar o seu perfil

O Azure Active Directory B2C fornece funcionalidades para permitir que os utilizadores atualizar os seus perfis. Na barra de menus de aplicação web, clique no nome de perfil e selecione **Editar perfil** para editar o perfil que criou.

![Editar perfil](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Alterar o **nome a apresentar** e **Cidade**.  Clique em **continuar** para atualizar o seu perfil.

![Atualizar perfil](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Tenha em atenção de que o nome a apresentar na parte superior direita da página mostra o nome atualizado. 

## <a name="access-a-secured-web-api-resource"></a>Aceder a uma recurso de API de web protegida

Clique em **lista de tarefas** para introduzir e modificar os itens de lista de tarefas. A aplicação web do ASP.NET inclui um token de acesso no pedido para o web permissão de requerente de recursos de API para efetuar operações em itens de lista de tarefas do utilizador. 

Introduza o texto a **Novo Item** caixa de texto. Clique em **adicionar** chamar o Azure AD B2C, protegida API web que adiciona um item de lista de tarefas.

![Adicionar um item de lista de tarefas](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

Utilizou com êxito a conta de utilizador do Azure AD B2C para efetuar uma chamada autorizada uma API web do Azure AD B2C, protegida.

## <a name="next-steps"></a>Passos seguintes

O exemplo utilizado neste guia de introdução pode ser utilizado para experimentar a outros cenários do Azure AD B2C, incluindo:

* Criar uma nova conta local utilizando um endereço de correio eletrónico.
* A repor a palavra-passe de conta local.

Se estiver pronto para delve para criar o seu inquilino do Azure AD B2C e configurar o exemplo a serem executados utilizando o seu inquilino, experimente o tutorial seguinte.

> [!div class="nextstepaction"]
> [Criar uma aplicação web ASP.NET com o Azure Active Directory B2C perfil de inscrição, início de sessão, editar e reposição de palavra-passe](active-directory-b2c-devquickstarts-web-dotnet-susi.md)