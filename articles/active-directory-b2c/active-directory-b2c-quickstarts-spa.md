---
title: "Unidade de teste de uma aplicação de página única do Azure AD B2C | Microsoft Docs"
description: "Unidade de teste a iniciar sessão, inscreva-se, editar o perfil e repor percursos de utilizador de palavra-passe utilizando o ambiente de teste do Azure AD B2C"
services: active-directory-b2c
documentationcenter: 
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 5a8a46af-28bb-4b70-a7f0-01a5240d0255
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 22da1ae317ba685d32f93d3331cf794b568891ec
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-single-page-application-configured-with-azure-ad-b2c"></a>Testar uma aplicação de página única configurada com o Azure AD B2C

## <a name="about-this-sample"></a>Sobre este exemplo

O Azure Active Directory B2C fornece gestão de identidades de nuvem para manter as suas aplicações, empresas, os clientes protegidos.  Este guia de introdução utiliza um exemplo de aplicação de página única para demonstrar:

* Utilizar o **inscrever-se ou iniciar sessão no** política para criar ou inicie sessão com um fornecedor de identidade de redes sociais ou uma conta local utilizando um endereço de correio eletrónico. 
* **Chamar uma API** obter o nome a apresentar a partir de um Azure AD B2C, protegida por recurso.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [2017 do Visual Studio](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
    - **Desenvolvimento do ASP.NET e Web**

* Instalar o [Node. js](https://nodejs.org/en/download/)

* Uma conta de redes social do Facebook, Google, Microsoft ou Twitter. Se não tiver uma conta de redes social, é necessário um endereço de correio eletrónico válido.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

[Transferir ou clonar a aplicação de exemplo](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) a partir do GitHub.

## <a name="run-the-sample-application"></a>Executar a aplicação de exemplo

Para executar este exemplo de linha de comandos Node.js: 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

A janela de consola mostra o número de porta para a aplicação web em execução no seu computador.

```
Listening on port 6420...
```

Abra `http://localhost:6420` num web browser para aceder à aplicação web.


![Aplicação de exemplo no browser](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>Criar uma conta

Clique em de **início de sessão** botão para iniciar o Azure AD B2C **inscrever-se ou iniciar sessão no** fluxo de trabalho. Ao criar uma conta, pode utilizar uma conta do fornecedor de identidade sociais existente ou uma conta de e-mail.

### <a name="sign-up-using-a-social-identity-provider"></a>Inscrever-se utilizando um fornecedor de identidade de redes sociais

Para inscrever-se utilizando um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar. Se preferir usar um endereço de e-mail, avance para o [inscrever-se utilizando um endereço de correio eletrónico](#sign-up-using-an-email-address) secção.

![Fornecedor de sessão ou inscrever-se](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

Tem de autenticar (início de sessão) com a sua conta de redes social credenciais e autorizar a aplicação a ler as informações da sua conta de redes social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de redes social, como o nome e a localidade. 

![Autenticar e autorizar utilizando uma conta de redes social](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

Os detalhes do perfil de conta nova são pré-preenchidos com informações da sua conta de redes social. 

![Detalhes da nova conta de perfil de inscrição](media/active-directory-b2c-quickstarts-spa/new-account-sign-up-profile-details-spa.png)

Atualize os campos de nome a apresentar, cargo e Cidade e clique em **continuar**.  Os valores que introduzir são utilizados para o seu perfil de conta de utilizador do Azure AD B2C.

Foi criada com êxito uma nova conta de utilizador do Azure AD B2C utiliza um fornecedor de identidade. 

Passo seguinte: [chamar um recurso](#call-a-resource) secção.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se utilizando um endereço de e-mail

Se optar por não utilizar uma conta de redes social para fornecer autenticação, pode criar uma conta de utilizador do Azure AD B2C, utilizando um endereço de correio eletrónico válido. Uma conta de utilizador local do Azure AD B2C utiliza o Azure Active Directory como o fornecedor de identidade. Para utilizar o seu endereço de e-mail, clique em de **não tiver uma conta? Inscrever-se agora** ligação.

![Sessão ou inscrever-se por e-mail](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-email-spa.png)

Introduza um endereço de correio eletrónico e clique em **enviar o código de verificação**. É necessário um endereço de e-mail válido para receber o código de verificação do Azure AD B2C. 

Introduza o código de verificação receber por e-mail e clique em **verificar código**.

Adicionar informações do seu perfil e clique em **criar**.

![Inscrever-se com a nova conta de correio eletrónico](media/active-directory-b2c-quickstarts-spa/sign-up-new-account-profile-email-web.png)

Foi criada com êxito uma nova conta de utilizador local do Azure AD B2C.

## <a name="call-a-resource"></a>Chamar um recurso

Depois de iniciar sessão, pode clicar no **chamar Web API** botão com o nome a apresentar devolvido da chamada de Web API como um objeto JSON. 

![Resposta de API Web](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

## <a name="next-steps"></a>Passos seguintes

O passo seguinte é criar o seu inquilino do Azure AD B2C e configurar o exemplo a serem executados utilizando o seu inquilino. 

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](active-directory-b2c-get-started.md)