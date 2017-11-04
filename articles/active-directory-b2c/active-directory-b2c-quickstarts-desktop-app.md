---
title: "Unidade de teste de uma aplicação de ambiente de trabalho do Azure AD B2C | Microsoft Docs"
description: "Unidade de teste a iniciar sessão, inscreva-se, editar o perfil e repor percursos de utilizador de palavra-passe utilizando o ambiente de teste do Azure AD B2C"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 86293627-26fb-4e96-a76b-f263f9a945bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 9653d86dd5635016512bf6e6fafbf7195145ed07
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-desktop-application-configured-with-azure-ad-b2c"></a>Testar uma aplicação de ambiente de trabalho configurada com o Azure AD B2C

O Azure Active Directory B2C fornece gestão de identidades de nuvem para manter as suas aplicações, empresas, os clientes protegidos.  Este guia de introdução utiliza uma aplicação de ambiente de trabalho de Windows Presentation Foundation (WPF) de exemplo para demonstrar:

* Utilizar o **inscrever-se ou iniciar sessão no** política para criar ou inicie sessão com um fornecedor de identidade de redes sociais ou uma conta local utilizando um endereço de correio eletrónico. 
* **Chamar uma API** obter o nome a apresentar a partir de um Azure AD B2C, protegida por recurso.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - **Desenvolvimento de ambiente de trabalho .NET**

* Uma conta de redes social do Facebook, Google, Microsoft ou Twitter. Se não tiver uma conta de redes social, é necessário um endereço de correio eletrónico válido.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

[Transferir ou clonar a aplicação de exemplo](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) a partir do GitHub.

## <a name="run-the-app-in-visual-studio"></a>Executar a aplicação no Visual Studio

Na pasta de projeto de aplicação de exemplo, abra o `active-directory-b2c-wpf.sln` solução no Visual Studio. 

Selecione **depurar > Iniciar depuração** para compilar e executar a aplicação. 

## <a name="create-an-account"></a>Criar uma conta

Clique em **sessão** para iniciar o **inscrever-se ou iniciar sessão no** fluxo de trabalho. Ao criar uma conta, pode utilizar uma conta do fornecedor de identidade sociais existente ou uma conta de e-mail.

![Aplicação de exemplo](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Inscrever-se utilizando um fornecedor de identidade de redes sociais

Para inscrever-se utilizando um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar. Se preferir usar um endereço de e-mail, avance para o [inscrever-se utilizando um endereço de correio eletrónico](#sign-up-using-an-email-address) secção.

![Fornecedor de sessão ou inscrever-se](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

Tem de autenticar (início de sessão) com a sua conta de redes social credenciais e autorizar a aplicação a ler as informações da sua conta de redes social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de redes social, como o nome e a localidade. 

![Autenticar e autorizar utilizando uma conta de redes social](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

Os detalhes do perfil de conta nova são pré-preenchidos com informações da sua conta de redes social. Modificar os detalhes se assim o desejar e clique em **continuar**.

![Detalhes da nova conta de perfil de inscrição](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

Foi criada com êxito uma nova conta de utilizador do Azure AD B2C utiliza um fornecedor de identidade. Após o início de sessão, o token de acesso é apresentado no *informações do Token* caixa de texto. O token de acesso é utilizado quando aceder ao recurso de API.

![Token de autorização](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

Passo seguinte: [Ir para editar o perfil](#edit-your-profile) secção.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se utilizando um endereço de e-mail

Se optar por não utilizar uma conta de redes social para fornecer autenticação, pode criar uma conta de utilizador do Azure AD B2C, utilizando um endereço de correio eletrónico válido. Uma conta de utilizador local do Azure AD B2C utiliza o Azure Active Directory como o fornecedor de identidade. Para utilizar o seu endereço de e-mail, clique em de **não tiver uma conta? Inscrever-se agora** ligação.

![Sessão ou inscrever-se por e-mail](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-email-wpf.png)

Introduza um endereço de correio eletrónico e clique em **enviar o código de verificação**. É necessário um endereço de e-mail válido para receber o código de verificação do Azure AD B2C.

Introduza o código de verificação receber por e-mail e clique em **verificar código**.

Adicionar informações do seu perfil e clique em **criar**.

![Inscrever-se com a nova conta de correio eletrónico](media/active-directory-b2c-quickstarts-desktop-app/sign-up-new-account-profile-email-wpf.png)

Foi criada com êxito uma nova conta de utilizador local do Azure AD B2C. Após o início de sessão, o token de acesso é apresentado no *informações do Token* caixa de texto. O token de acesso é utilizado quando aceder ao recurso de API.

![Token de autorização](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="edit-your-profile"></a>Editar o seu perfil

O Azure Active Directory B2C fornece funcionalidades para permitir que os utilizadores atualizar os seus perfis. Clique em **Editar perfil** para editar o perfil que criou.

![Editar perfil](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

Escolha o fornecedor de identidade associado à conta que criou. Por exemplo, se utilizou Twitter como o fornecedor de identidade quando criou a conta, escolha Twitter para modificar os detalhes do perfil associadas.

![Escolha um fornecedor associado ao perfil para editar](media/active-directory-b2c-quickstarts-desktop-app/edit-account-choose-provider-wpf.png)

Alterar o **nome a apresentar** ou **Cidade**. 

![Atualizar perfil](media/active-directory-b2c-quickstarts-desktop-app/update-profile-wpf.png)

É apresentado um novo token de acesso no *informações do Token* caixa de texto. Se pretender verificar as alterações para o seu perfil, copie e cole o token de acesso a https://jwt.ms descodificador token.

![Token de autorização](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="access-a-resource"></a>Aceder a um recurso

Clique em **chamada API** efetuar um pedido para o Azure AD B2C, protegida https://fabrikamb2chello.azurewebsites.net/hello recursos. 

![Chamada de API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

A aplicação inclui o token de acesso apresentado no *informações do Token* caixa de texto no pedido. A API envia o nome a apresentar contido no token de acesso.

## <a name="next-steps"></a>Passos seguintes

O passo seguinte é criar o seu inquilino do Azure AD B2C e configurar o exemplo a serem executados utilizando o seu inquilino. 

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](active-directory-b2c-get-started.md)
