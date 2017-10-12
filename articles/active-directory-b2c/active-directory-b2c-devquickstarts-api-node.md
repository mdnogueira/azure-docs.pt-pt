---
title: 'B2C do Azure AD: Proteger uma API Web utilizando Node.js | Microsoft Docs'
description: Como criar uma API Web de Node.js que aceita tokens de um inquilino do B2C
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: fc2b9af8-fbda-44e0-962a-8b963449106a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: xerners
ms.openlocfilehash: 2315f5d0ebb142561c65224cd9d06299466a7ee4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>B2C do Azure AD: Proteger uma API Web utilizando Node.js
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o Azure Active Directory (Azure AD) B2C, pode proteger uma API web utilizando tokens de acesso de OAuth 2.0. Estes tokens permitem que as aplicações de cliente que utilizam o Azure AD B2C se autentiquem na API. Este artigo mostra-lhe como criar uma API de "lista de tarefas" que permite aos utilizadores adicionar e listar tarefas. A API Web é protegida ao utilizar o Azure AD B2C e só permite que os utilizadores autenticados façam a gestão da respetiva lista de tarefas.

> [!NOTE]
> Este exemplo foi escrito para fazer uma ligação utilizando a nossa [aplicação de exemplo B2C para iOS](active-directory-b2c-devquickstarts-ios.md). Primeiro, explore esta passagem e, em seguida, acompanhe com esse exemplo.
>
>

**Passport** é o middleware de autenticação do Node.js. Flexível e modular, o Passport pode ser instalado discretamente em qualquer aplicação Web baseada no Express ou Restify. Um conjunto abrangente de estratégias suporta a autenticação utilizando um nome de utilizador e palavra-passe, o Facebook, o Twitter e muito mais. Desenvolvemos uma estratégia para o Azure Active Directory (Azure AD). Instale este módulo e, em seguida, adicione o plug-in `passport-azure-ad` do Azure AD.

Para efetuar este exemplo, tem de:

1. Registar uma aplicação com o Azure AD.
2. Configure a aplicação para utilizar o plug-in `passport-azure-ad` do Passport.
3. Configure uma aplicação de cliente para chamar a API Web da “lista de ações a fazer”.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C
Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino.  Um diretório é um contentor para todos os utilizadores, aplicações, grupos, etc.  Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Criar uma aplicação
Em seguida, precisa de criar uma aplicação no seu diretório do B2C que dá ao Azure AD algumas informações necessárias para comunicar de forma segura com a aplicação. Neste caso, a aplicação cliente e a API Web são representadas por um único **ID de Aplicação** porque compõem uma aplicação lógica. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

* Incluir uma **aplicação Web/API Web** na aplicação
* Introduzir `http://localhost/TodoListService` como um **URL de resposta**. É o URL predefinido para este exemplo de código.
* Criar um **Segredo de aplicação** para a aplicação e copiá-lo. Estes dados são necessários mais tarde. Tenha em atenção que este valor de ser [XML de escape](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de o utilizar.
* Copiar a **ID da Aplicação** atribuída à aplicação. Estes dados são necessários mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as políticas
No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém duas experiências de identidade: registo e início de sessão. Tem de criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy).  Quando criar as três políticas, não se esqueça de:

* Escolher o **Nome a apresentar** e os atributos de inscrição na política de inscrição.
* Escolher as afirmações de aplicação **Nome a apresentar** e **ID de objeto** em cada política.  Também pode escolher outras afirmações.
* Copie o **Nome** de cada política depois de criá-la. Deve ter o prefixo `b2c_1_`.  Vai necessitar destes nomes de política mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, está pronto para criar a aplicação.

Para saber mais sobre como funcionam as políticas no Azure AD B2C, comece com o [Tutorial de introdução da aplicação Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Transferir o código
O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Para criar o exemplo à medida que avança, pode [transferir o projeto de estrutura como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Também pode clonar a estrutura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

A aplicação concluída está também [disponível como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) ou no ramo `complete` do mesmo repositório.

## <a name="download-nodejs-for-your-platform"></a>Transferir Node.js para a plataforma
Para utilizar este exemplo com êxito, tem de ter uma instalação do Node.js em execução.

Instalar Node.js de [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Instalar MongoDB para a plataforma
Para utilizar este exemplo com êxito, tem de ter uma instalação do MongoDB. Utilizamos o MongoDB para tornar a API REST persistente em várias instâncias de servidor.

Instalar MongoDB de [mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Esta passagem parte do princípio de que irá utilizar os pontos finais do servidor e a instalação predefinida para o MongoDB, que de momento é `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Instalar os módulos Restify na API Web
Utilizamos o Restify para criar a API REST. O Restify é uma estrutura de aplicação Node.js mínima e flexível derivada do Express. Tem um conjunto robusto de funcionalidades para a criação de APIs REST por cima do Connect.

### <a name="install-restify"></a>Instalação do Restify
Na linha de comandos, altere o diretório para `azuread`. Se o diretório `azuread` não existe, crie-o.

`cd azuread` ou `mkdir azuread;`

Introduza o seguinte comando:

`npm install restify`

Este comando instala o Restify.

#### <a name="did-you-get-an-error"></a>Obteve um erro?
Em alguns sistemas operativos, quando utiliza `npm`, poderá receber o erro `Error: EPERM, chmod '/usr/local/bin/..'` e um pedido para que execute a conta como administrador. Se este problema ocorrer, utilize o comando `sudo` para executar `npm` a um nível de privilégio mais elevado.

#### <a name="did-you-get-a-dtrace-error"></a>Obteve um erro de DTrace?
Quando instalar o Restify, pode aparecer o seguinte texto:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

O Restify fornece um mecanismo poderoso para rastreio de chamadas REST utilizando o DTrace. No entanto, muitos sistemas operativos não tem o DTrace disponível. Pode ignorar com segurança estes erros.

O resultado do comando deve ter um aspeto semelhante ao que se segue:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Instalar o Passport na API Web
Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí.

Introduza o comando seguinte para instalar o Passport:

`npm install passport`

O resultado do comando deve ser semelhante ao texto que se segue:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Adicionar passport-azuread à API Web 
Em seguida, adicione a estratégia de OAuth utilizando `passport-azuread`, um conjunto de estratégias que ligam o Azure AD com o Passport. Utilize esta estratégia para os tokens de portador no exemplo API REST.

> [!NOTE]
> Embora o OAuth2 forneça uma estrutura onde pode ser emitido qualquer tipo de token conhecido, apenas alguns tipos de tokens passaram a ser amplamente utilizados. Os tokens para proteger os pontos finais são tokens de portador. Estes tipos de tokens são os mais emitidos no OAuth2. Várias implementações partem do princípio de que os tokens de portador são o único tipo de token emitido.
>
>

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí.

Introduza o comando seguinte para instalar o módulo `passport-azure-ad` do Passport:

`npm install passport-azure-ad`

O resultado do comando deve ser semelhante ao texto que se segue:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Adicione módulos do MongoDB à API Web
Este exemplo utiliza o MongoDB como arquivo de dados. Para tal, instale o Mongoose, um plug-in de gestão de modelos e esquemas muito utilizado.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Instalar módulos adicionais
Em seguida, instale os restantes módulos necessários.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Instale os módulos no diretório `node_modules`:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>Crie um ficheiro de server.js com as suas dependências
O ficheiro `server.js` proporciona a maior parte das funcionalidades do seu servidor de API Web.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Crie um ficheiro `server.js` num editor. Adicione as seguintes informações:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Guarde o ficheiro. Regressará a este mais tarde.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Criar um ficheiro de config.js para armazenar as suas definições do Azure AD
Este ficheiro de código transmite os parâmetros de configuração a partir do Portal do Azure AD para o ficheiro `Passport.js`. Criou estes valores de configuração quando adicionou a API Web ao portal na primeira parte da passagem. Vamos explicar o que deve introduzir nos valores destes parâmetros depois de copiar o código.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Crie um ficheiro `config.js` num editor. Adicione as seguintes informações:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valores necessários
`clientID`: o ID de cliente da aplicação API Web.

`IdentityMetadata`: este é o local onde o `passport-azure-ad` vai procurar os seus dados de configuração para o fornecedor de identidade. Também vai procurar aqui as chaves para validar os tokens Web JSON.

`audience`: o URI (Uniform Resource Identifier) do portal que identifica a sua aplicação de chamada.

`tenantName`: o nome do seu inquilino (por exemplo, **contoso.onmicrosoft.com**).

`policyName`: a política que pretende que valide os tokens recebidos pelo seu servidor. Esta política deve ser igual à utilizada na aplicação cliente para iniciar sessão.

> [!NOTE]
> Por agora, utilize as mesmas políticas para configurar o cliente e o servidor. Se já tiver concluído uma passagem e criado estas políticas, não terá de fazê-lo novamente. Uma vez concluída a passagem, não deverá ser necessário configurar novas políticas para passagens de cliente no site.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Adicionar configuração ao seu ficheiro server.js
Para ler os valores do ficheiro `config.js` que criou, adicione o ficheiro `.config` como recurso pretendido na sua aplicação e, em seguida, defina as variáveis globais para as existentes no documento `config.js`.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Crie o ficheiro `server.js` num editor. Adicione as seguintes informações:

```Javascript
var config = require('./config');
```
Adicione uma nova secção a `server.js` que inclua o seguinte código:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Em seguida, vamos adicionar alguns marcadores de posição para os utilizadores que recebemos a partir das nossas aplicações de chamada.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Vamos avançar e criar também o nosso registo.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Adicionar as informações de esquema e modelo MongoDB utilizando o Mongoose
A preparação prévia funciona se juntar estes três ficheiros num serviço API REST.

Para esta passagem, utilize o MongoDB para armazenar as suas tarefas, como mencionado anteriormente.

No ficheiro `config.js`, atribuiu o nome **tasklist** à base de dados. Esse nome foi também o que colocou no final da URL da ligação `mongoose_auth_local`. Não precisa de criar esta base de dados previamente no MongoDB. Cria a base de dados para si na primeira execução da sua aplicação de servidor.

Depois de indicar ao servidor qual a base de dados de MongoDB a utilizar, precisa de escrever alguns códigos adicionais para criar o modelo e o esquema das suas tarefas de servidor.

### <a name="expand-the-model"></a>Expandir o modelo
Este modelo de esquema é simples. Pode expandi-lo conforme necessário.

`owner`: quem é atribuído à tarefa. Este objeto é uma **cadeia**.  

`Text`: a tarefa propriamente dita. Este objeto é uma **cadeia**.

`date`: a data em que a tarefa deve ser concluída. Este objeto é uma **datetime**.

`completed`: se a tarefa está concluída. Este objeto é um **Booleano**.

### <a name="create-the-schema-in-the-code"></a>Criar o esquema no código
Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Crie o ficheiro `server.js` num editor. Adicione a informação seguinte abaixo da entrada de configuração:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Crie primeiro o esquema e, em seguida, crie um objeto de modelo que utilizará para armazenar os dados em todo o código, quando definir as suas **rotas**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Adicionar rotas ao seu servidor de tarefas da API REST
Agora que tem um modelo de base de dados com o qual trabalhar, adicione as rotas que vai utilizar com o servidor da API REST.

### <a name="about-routes-in-restify"></a>Sobre rotas no Restify
As rotas funcionam no Restify da mesma maneira que funcionam quando utilizam a pilha Express. Definem-se rotas utilizando o URI que se espera que as aplicações de cliente chamem.

Um padrão normal para uma rota do Restify é:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

O Restify e o Express podem fornecer funcionalidades muito mais aprofundada, tais como, definir tipos de aplicações e efetuar encaminhamentos complexos para diferentes pontos finais. Para este tutorial, mantemos estas rotas simples.

#### <a name="add-default-routes-to-your-server"></a>Adicionar rotas predefinidas ao seu servidor
Em seguida, adicione as rotas CRUD básicas de **criar** e **listar** para a nossa API REST. Outras rotas estão disponíveis no ramo `complete` do exemplo.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Crie o ficheiro `server.js` num editor. Adicione as informações seguintes abaixo das entradas da base de dados que criou acima:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>Adicionar tratamento de erro para as rotas
Adicione tratamento de erros para que possa comunicar quaisquer problemas que encontre de forma a que o cliente possa compreender.

Adicione o seguinte código:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>Criar o seu servidor
Agora definiu a base de dados e colocou as rotas. A última coisa que tem de fazer consiste em adicionar a instância de servidor que gere as suas chamadas.

O Restify e o Express fornecem personalização avançada para um servidor de API REST, mas nós utilizamos a configuração mais básica.

```Javascript

/**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Adicionar as rotas ao servidor (sem autenticação)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Adicionar autenticação ao seu servidor de API REST
Agora que um servidor de API REST está em execução, pode torná-lo útil em relação ao Azure AD.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Utilize o OIDCBearerStrategy que está incluído no passport-azure-ad
> [!TIP]
> Quando escreve APIs, deve ligar sempre os dados a algo especial do token que o utilizador não pode falsificar. Quando o servidor armazena itens ToDo, fá-lo com base no **oid** do utilizador no token (chamado de token.oid) que é incluído no campo “proprietário”. Este valor garante que apenas esse utilizador pode aceder aos próprios itens ToDo. Não existe nenhuma exposição na API de “proprietário”, de modo que um utilizador externo pode pedir itens ToDo de outras pessoas, mesmo que estejam autenticados.
>
>

Em seguida, utilize a estratégia de portador que vem com `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

O Passport utiliza o mesmo padrão para todas as respetivas estratégias. Passe-o como uma `function()` que tem `token` e `done` como parâmetros. A estratégia é-lhe devolvida depois de fazer todo o trabalho. Em seguida, deve armazenar o utilizador e guardar o token para que não seja necessário voltar a pedi-lo.

> [!IMPORTANT]
> O código acima aceita qualquer utilizador que se autentique com o seu servidor. Este processo é conhecido como registo automático. Em servidores de produção, não permita que quaisquer utilizadores acedam à API sem passarem primeiro por um processo de registo. Este processo é normalmente o padrão normal que vê nas aplicações de consumidor que lhe permitem fazer o registo através do Facebook, mas que em seguida lhe pedem para preencher informações adicionais. Se este programa não fosse um programa de linha de comandos, poderíamos extrair o e-mail do objeto do token que é devolvido e, em seguida, pedir aos utilizadores para preencher informações adicionais. Uma vez que este é um exemplo, vamos adicioná-los a uma base de dados na memória.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Execute a aplicação de servidor para verificar se esta o rejeita
Pode utilizar `curl` para ver se tem agora proteção do OAuth2 em relação aos pontos finais. Os cabeçalhos devolvidos devem ser suficientes para lhe indicar que está no caminho certo.

Certifique-se de que a instância do MongoDB está em execução:

    $sudo mongodb

Mude para o diretório e execute o servidor:

    $ cd azuread
    $ node server.js

Numa nova janela de terminal, execute `curl`

Tente um POST básico:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

A resposta pretendida é um erro 401. Indica que a camada do Passport está a tentar redirecionar para o ponto final de autorização.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Agora tem um serviço de API REST que utiliza o OAuth2
Tem implementada uma API REST utilizando o Restify e o OAuth! Tem agora código suficiente para que possa continuar a desenvolver o seu serviço e compilação neste exemplo. Fez tudo o que podia com este servidor sem utilizar um cliente compatível com o OAuth2. Para esse passo seguinte, utilize uma passagem walk-through adicional como a nossa passagem [Ligar a uma API web utilizando iOS com B2C](active-directory-b2c-devquickstarts-ios.md).

## <a name="next-steps"></a>Passos seguintes
Agora pode aceder a tópicos mais avançados, como:

[Ligar a uma API Web utilizando o iOS com o B2C](active-directory-b2c-devquickstarts-ios.md)
