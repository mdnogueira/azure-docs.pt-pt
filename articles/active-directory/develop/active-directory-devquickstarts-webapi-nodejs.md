---
title: "Node.js do Azure Active Directory introdução | Microsoft Docs"
description: "Como criar uma API que se integra com o Azure AD para autenticação de web de Node.js REST."
services: active-directory
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/17/2017
ms.author: cshoe
ms.custom: aaddev
ms.openlocfilehash: a0b8ff6894a1f30fd8d18beef234737573691af9
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="secure-nodejs-web-api-with-azure-active-directory"></a>Proteger Web de Node.js API no Azure Active Directory

Este artigo demonstra como proteger uma [Restify](http://restify.com/) ponto final de API com [Passport](http://passportjs.org/) utilizando o [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) módulo para processar a comunicação com o Azure Active Directory (AAD). 

O âmbito deste tutorial abrange as preocupações relativos à proteger pontos finais da API. As preocupações de início de sessão e a reter os tokens de autenticação não estão implementadas aqui e são da responsabilidade de uma aplicação de cliente. Para obter detalhes envolvente uma implementação de cliente, consulte [aplicação web de Node.js início de sessão e fim de sessão com o Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md).

O exemplo de código completo associado a este artigo não está disponível na [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>Criar o projeto de exemplo
Esta aplicação de servidor requer alguns dependências de pacote para suportar o Restify e o Passport, bem como a conta de informações são transmitidas para o AAD.

Para começar, adicione o seguinte código para um ficheiro denominado `package.json`:

```Shell
{
  "name": "node-aad-demo",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "3.0.8",
    "restify": "6.0.1",
    "restify-plugins": "1.6.0"
  }
}
```

Uma vez `package.json` é criado, execute `npm install` na sua linha de comandos para instalar as dependências de pacote. 

### <a name="configure-the-project-to-use-active-directory"></a>Configurar o projeto para utilizar o Active Directory

Para começar a configurar a aplicação, existem alguns valores específicos da conta pode obter a CLI do Azure. A forma mais fácil para começar com o CLI está a utilizar a Shell de nuvem do Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Introduza o seguinte comando na shell do cloud: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

O [argumentos](/cli/azure/ad/app?view=azure-cli-latest#az_ad_app_create) para o `create` comando incluem:

| Argumento  | Descrição |
|---------|---------|
|`display-name` | Nome amigável do registo |
|`homepage` | URL onde os utilizadores podem iniciar sessão e utilizar a aplicação |
|`identifier-uris` | Espaço separados URIs exclusivo que pode utilizar o Azure AD para esta aplicação |

Antes de poder ligar ao Azure Active Directory, terá as seguintes informações:

| Nome  | Descrição | Nome da variável no ficheiro de configuração |
| ------------- | ------------- | ------------- |
| Nome do inquilino  | [Nome do inquilino](active-directory-howto-tenant.md) que pretende utilizar para autenticação | `tenantName`  |
| ID de cliente  | ID de cliente é o termo de OAuth utilizado para o AAD _ID da aplicação_. |  `clientID`  |

Da resposta de registo na Shell de nuvem do Azure, copie o `appId` valor e criar um novo ficheiro designado `config.js`. Em seguida, adicione o seguinte código e substitua os valores com os tokens entre parênteses:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```
Para obter mais informações sobre as definições de configuração individuais, reveja o [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) documentação do módulo.

## <a name="implement-the-server"></a>Implementar o servidor
O [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) módulo funcionalidades estratégias de autenticação de dois: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) e [portador](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) estratégias. O servidor implementado neste artigo utiliza a estratégia de portador para proteger o ponto final de API.

### <a name="step-1-import-dependencies"></a>Passo 1: Dependências de importação
Criar um novo ficheiro designado `app.js` e cole o seguinte texto:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require('restify-plugins')
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

Nesta secção do código:

- O `restify` e `restify-plugins` módulos são referenciados para configurar um servidor do Restify.

- O `passport` e `passport-azure-ad` módulos são responsáveis por comunicar com o AAD.

- O `config` variável é inicializada com valores entre o `config.js` ficheiro criado no passo anterior.

- É criada uma matriz para `authenticatedUserTokens` para armazenar os tokens de utilizador, que são transmitidos para pontos finais protegidos.

- O `serverPort` é o definido da porta do ambiente de processo ou o ficheiro de configuração.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Passo 2: Instanciar estratégia de autenticação
Como proteger um ponto final, tem de fornecer uma estratégia de responsável por determinar se pretende ou não tem origem do pedido atual de um utilizador autenticado. Aqui o `authenticatonStrategy` variável é uma instância do `passport-azure-ad` `BearerStrategy` classe. Adicione o seguinte código após o `require` instruções.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let userToken = authenticatedUserTokens.find((user) => user.sub === token.sub);

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, user, token);
});
```
Esta implementação utiliza o registo automático adicionando os tokens de autenticação para o `authenticatedUserTokens` matriz caso ainda não existam.

Quando é criada uma nova instância da estratégia, tem de passá-lo no Passport através de `use` método. Adicione o seguinte código para `app.js` para utilizar a estratégia no Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Passo 3: Configuração de servidor
Com a estratégia de autenticação definida, agora pode configurar o servidor do Restify com algumas definições básicas e configurado para utilizar o Passport para segurança.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Este servidor é inicializado e configurado para analisar cabeçalhos de autorização e, em seguida, configurado para utilizar o Passport.


### <a name="step-4-define-routes"></a>Passo 4: Definir rotas
Agora pode definir as rotas e decidir que proteger com o AAD. Este projeto inclui duas rotas onde o nível de raiz está aberto e o `/api` rota é definida para exigir a autenticação.

No `app.js` adicione o seguinte código para a rota de nível de raiz:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

A rota de raiz permite que todos os pedidos através do expressroute e devolve uma mensagem que inclui um comando para testar o `/api` rota. Por outro lado, a `/api` rota bloqueada utilizando [ `passport.authenticate` ](http://passportjs.org/docs/authenticate). Adicione o seguinte código após a rota de raiz.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Esta configuração só permite autenticados pedidos que incluem um acesso de token de portador para `/api`. A opção de `session: false` é utilizado para desativar sessões para exigir que é transmitido um token com cada pedido para a API.

Por fim, o servidor está definido para escutar na porta configurada ao chamar o `listen` método.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Executar o exemplo

Agora que o servidor é implementado, pode iniciar o servidor ao abrir uma linha de comandos e introduza:

```Shell
npm start
```

Com o servidor em execução, pode submeter um pedido para o servidor para testar os resultados. Para demonstrar a resposta da rota de raiz, abra uma shell de deteção e introduza o seguinte código:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Se tiver configurado o servidor corretamente, a resposta deve ter um aspeto semelhante a:

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Em seguida, pode testar a rota que exija a autenticação ao introduzir o seguinte comando na shell de Deteção:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Se tiver configurado o servidor corretamente, em seguida, o servidor deve responder com um Estado de `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Agora que criou uma API segura, pode implementar um cliente com capacidade para passar os tokens de autenticação para a API.

## <a name="next-steps"></a>Passos seguintes
Conforme indicado na introdução, tem de implementar um homólogo de cliente para ligar ao servidor que processa o início de sessão, termine a sessão e gerir tokens. Para obter exemplos baseada no código, pode referir-se para as aplicações de cliente na [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) e [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). Para um tutorial passo a passo, consulte o artigo seguinte:

> [!div class="nextstepaction"]
> [Aplicação web de node.js início de sessão e fim de sessão com o Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md)