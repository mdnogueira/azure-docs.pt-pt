---
title: Proteger uma API web v 2.0 do Azure Active Directory utilizando Node.js | Microsoft Docs
description: Saiba como criar uma Node.js API web que aceita tokens a partir de uma conta Microsoft pessoal e de trabalho ou escola contas.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 94e945a52b9df7c495de1611baa08083357670c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Proteger uma API web utilizando Node.js
> [!NOTE]
> Nem todos os cenários do Azure Active Directory e funcionalidades funcionam com o ponto final v 2.0. Para determinar se deve utilizar o ponto final v 2.0 ou o ponto final v 1.0, leia sobre [limitações de v 2.0](active-directory-v2-limitations.md).
> 
> 

Quando utiliza o ponto de final de v 2.0 do Azure Active Directory (Azure AD), pode utilizar [OAuth 2.0](active-directory-v2-protocols.md) aceder tokens para proteger a API web. Com OAuth 2.0 tokens de acesso, os utilizadores que possuem uma conta Microsoft pessoal e o trabalho ou contas profissional podem aceder em segurança a API web.

*Passport* é o middleware de autenticação do Node.js. Flexível e modulares, Passport pode ser removido discretamente em qualquer baseada em Express ou restify aplicação web. No Passport, um conjunto abrangente de estratégias suporta a autenticação utilizando um nome de utilizador e palavra-passe, Facebook, Twitter ou outras opções. Desenvolvemos uma estratégia para o Azure AD. Neste artigo, vamos mostrar-lhe como instalar o módulo e, em seguida, adicione o Azure AD `passport-azure-ad` Plug-in.

## <a name="download"></a>Transferência
O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Para seguir o tutorial, pode [transferir a estrutura da aplicação como um ficheiro. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip), ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Também pode obter a aplicação concluída no final deste tutorial.

## <a name="1-register-an-app"></a>1: registar uma aplicação
Criar uma nova aplicação em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou seguir [detalhadas destes passos](active-directory-v2-app-registration.md) para registar uma aplicação. Certifique-se de que:

* Copiar o **Id da aplicação** atribuída à aplicação. Irá precisar das mesmas para este tutorial.
* Adicionar o **Mobile** plataforma para a sua aplicação.
* Copiar o **URI de redirecionamento** do portal. Tem de utilizar o valor URI predefinido `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>2: instalar o Node.js
Para utilizar o exemplo para este tutorial, terá [instale o Node.js](http://nodejs.org).

## <a name="3-install-mongodb"></a>3: instalar MongoDB
Para utilizar este exemplo com êxito, deve [instalar MongoDB](http://www.mongodb.org). Neste exemplo, utilize o MongoDB para tornar a API REST persistente em instâncias de servidor.

> [!NOTE]
> Neste artigo, partimos do princípio que utiliza os pontos finais de servidor e a instalação predefinida para o MongoDB: mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4: instalar os módulos restify na web API
Utilizamos Resitfy para criar a nossa API REST. O restify é uma estrutura da aplicação de Node.js mínima e flexível que é derivada do Express. O restify tem um conjunto robusto de funcionalidades que pode utilizar para criar APIs REST por cima do Connect.

### <a name="install-restify"></a>Instalação do restify
1.  Numa linha de comandos, altere o diretório para **azuread**:

    `cd azuread`

    Se o **azuread** diretório não existe, crie-o:

    `mkdir azuread`

2.  Instalação do restify:

    `npm install restify`

    O resultado deste comando deve ter o seguinte aspeto:

    ```
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
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>Obteve um erro?
Em alguns sistemas operativos, quando utiliza o `npm` comando, poderá ver esta mensagem: `Error: EPERM, chmod '/usr/local/bin/..'`. O erro é seguido de um pedido, tentar executar a conta como administrador. Se isto ocorrer, utilize o comando `sudo` executar `npm` a um nível de privilégio mais elevado.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>Obteve um erro relacionado com o DTrace?
Quando instala o restify, poderá ver esta mensagem:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
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

O restify tem um mecanismo poderoso para rastreio de chamadas REST utilizando o DTrace. No entanto, o DTrace não está disponível em muitos sistemas operativos. Pode ignorar esta mensagem de erro.


## <a name="5-install-passportjs-in-your-web-api"></a>5: instalar Passport.js na web API
1.  Na linha de comandos, altere o diretório para **azuread**.

2.  Instale Passport.js:

    `npm install passport`

    O resultado do comando deve ter o seguinte aspeto:

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: adicionar passport-azure-ad para a API web
Em seguida, adicione a estratégia de OAuth utilizando passport-azuread. `passport-azuread`é um conjunto de estratégias que ligam o Azure AD com o Passport. Podemos utilizar esta estratégia para os tokens de portador no exemplo REST API.

> [!NOTE]
> Apesar de OAuth 2.0 proporciona uma arquitetura na qual pode ser emitido qualquer tipo de token conhecido, determinados tipos de tokens são frequentemente utilizados. Os tokens de portador são frequentemente utilizados para proteger pontos finais. Os tokens de portador são o tipo mais emitido de token de OAuth 2.0. Muitos implementações de OAuth 2.0 partem do princípio de que os tokens de portador são o único tipo de token emitido.
> 
> 

1.  Numa linha de comandos, altere o diretório para **azuread**.

    `cd azuread`

2.  Instalar o Passport.js `passport-azure-ad` módulo:

    `npm install passport-azure-ad`

    O resultado do comando deve ter o seguinte aspeto:

    ```
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
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: Adicione módulos do MongoDB à web API
Neste exemplo, utilizamos MongoDB como arquivo nossos dados. 

1.  Instalar o Mongoose, um amplamente utilizado Plug-in para gerir modelos e esquemas: 

    `npm install mongoose`

2.  Instale o controlador da base de dados para o MongoDB, que também é designado por MongoDB:

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: instalar módulos adicionais
Instale os restantes módulos necessários.

1.  Numa linha de comandos, altere o diretório para **azuread**:

    `cd azuread`

2.  Introduza os seguintes comandos. Os comandos de instalar os seguintes módulos no seu diretório node_modules:

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9: criar um ficheiro de Server.js para as suas dependências
Um ficheiro de Server.js contém a maioria das funcionalidades para o servidor de API web. Adicione a maior parte do seu código a este ficheiro. Para fins de produção, pode refatorar a funcionalidade em ficheiros mais pequenos, como rotas separadas e controladores. Neste artigo, utilizamos Server.js para esta finalidade.

1.  Numa linha de comandos, altere o diretório para **azuread**:

    `cd azuread`

2.  Com um editor à sua escolha, crie um ficheiro de Server.js. Adicione as seguintes informações para o ficheiro:

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Guarde o ficheiro. Regressará ao mesmo em breve.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: criar um ficheiro de configuração para armazenar as suas definições do Azure AD
Este ficheiro de código transmite os parâmetros de configuração do portal do Azure AD para Passport.js. Criou estes valores de configuração quando adicionou a API web para o portal no início do artigo. Depois de copiar o código, vamos explicar o que colocar os valores destes parâmetros.

1.  Numa linha de comandos, altere o diretório para **azuread**:

    `cd azuread`

2.  No editor, crie um ficheiro de Config.js. Adicione as seguintes informações:

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Valores necessários

*   **IdentityMetadata**: Este é onde `passport-azure-ad` procura dos seus dados de configuração para o fornecedor de identidade (IDP) e as chaves validar o JSON Web Tokens (JWTs). Se estiver a utilizar o Azure AD, provavelmente não pretende alterar esta.

*   **público-alvo**: O URI de redirecionamento do portal.

> [!NOTE]
> Reverta as chaves intervalos frequentes. Lembre-se de que é sempre puxe a partir do URL de "openid_keys" e que a aplicação pode aceder à Internet.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11: adicionar a configuração ao seu ficheiro Server.js
A aplicação tem de ler os valores do ficheiro de configuração que acabou de criar. Adicione o ficheiro. config como um recurso necessário na sua aplicação. Defina as variáveis globais para aqueles que estão em Config.js.

1.  Na linha de comandos, altere o diretório para **azuread**:

    `cd azuread`

2.  No editor, abra Server.js. Adicione as seguintes informações:

    ```Javascript
    var config = require('./config');
    ```

3.  Adicione uma nova secção para Server.js:

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12: adicionar as informações de esquema e modelo MongoDB utilizando o Mongoose
Em seguida, ligar estes três ficheiros num serviço REST API.

Neste artigo, utilizamos MongoDB para armazenar o nosso tarefas. Vamos discutir isto em *passo 4*.

O ficheiro de Config.js que criou no passo 11, a base de dados é designado *tasklist*. Que era o que colocou no final do seu URL de ligação mongoose_auth_local. Não precisa de criar esta base de dados previamente no MongoDB. A base de dados é criado na primeira execução da aplicação de servidor (assumindo que a base de dados ainda não existir).

Tiver disse o servidor de base de dados de MongoDB a utilizar. Em seguida, terá de escrever alguns códigos adicionais para criar o modelo e o esquema para tarefas do servidor.

### <a name="the-model"></a>O modelo
O modelo de esquema é muito básico. Pode expandi-lo se for necessário. 

O modelo de esquema tem estes valores:

*   **NOME**. A pessoa que está atribuída à tarefa. Este é um **cadeia** valor.
*   **TAREFA**. O nome da tarefa. Este é um **cadeia** valor.
*   **DATA**. A data em que a tarefa deve ser concluída. Este é um **datetime** valor.
*   **CONCLUIR**. Indica se a tarefa é concluída. Este é um **booleano** valor.

### <a name="create-the-schema-in-the-code"></a>Criar o esquema no código
1.  Numa linha de comandos, altere o diretório para **azuread**:

    `cd azuread`

2.  No seu editor, abra Server.js. Abaixo da entrada de configuração, adicione as seguintes informações:

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Este código liga-se ao servidor MongoDB. Também devolve um objeto de esquema.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>Utilizando o esquema, criar o seu modelo no código
Abaixo do código anterior, adicione o seguinte código:

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

Como pode descobrir do código, primeiro, crie o esquema. Em seguida, crie um objeto de modelo. Utilize o objecto de modelo para armazenar os dados em todo o código ao definir o **rotas**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13: adicionar as rotas para o servidor de REST API de tarefas
Agora que tem um modelo de base de dados para trabalhar com, adicione as rotas que irá utilizar para o servidor de REST API.

### <a name="about-routes-in-restify"></a>Sobre rotas no restify
Rotas no restify trabalho exatamente da mesma forma que fazer quando utiliza a pilha Express. Definem-se rotas utilizando o URI que se espera que as aplicações de cliente chamem. Normalmente, é possível definir as rotas num ficheiro separado. Neste tutorial, colocarmos os nossas rotas no Server.js. Para utilização em produção, recomendamos que fator rotas num ficheiro próprio.

Um padrão normal para uma rota do restify é:

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Este é o padrão no nível mais básico. O restify (e Express) fornecer funcionalidades muito mais aprofundada, como a capacidade de definir os tipos de aplicações e o encaminhamento complexos para diferentes pontos finais.

#### <a name="add-default-routes-to-your-server"></a>Adicionar rotas predefinidas ao seu servidor
Adicionar as rotas CRUD básicas: **criar**, **obter**, **atualizar**, e **eliminar**.

1.  Numa linha de comandos, altere o diretório para **azuread**:

    `cd azuread`

2.  No editor, abra Server.js. Abaixo as entradas de base de dados que efetuou anteriormente, adicione as seguintes informações:

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
    next(new MissingTaskError());
    return;
    }
    _task.owner = owner;
    _task.task = req.params.task;
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
    // Delete a task by name.
    function removeTask(req, res, next) {
    Task.remove({
    task: req.params.task,
    owner: owner
    }, function(err) {
    if (err) {
    req.log.warn(err,
    'removeTask: unable to delete %s',
    req.params.task);
    next(err);
    } else {
    log.info('Deleted task:', req.params.task);
    res.send(204);
    next();
    }
    });
    }
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
    function getTask(req, res, next) {
    log.info('getTask was called for: ', owner);
    Task.find({
    owner: owner
    }, function(err, data) {
    if (err) {
    req.log.warn(err, 'get: unable to read %s', owner);
    next(err);
    return;
    }
    res.json(data);
    });
    return next();
    }
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
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
    log.warn(err, "There are no tasks in the database. Add one!");
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

### <a name="add-error-handling-for-the-routes"></a>Adicionar tratamento de erro para as rotas
Adicione tratamento de erros pelo que pode comunicar novamente para o cliente sobre o problema que encontrou.

Adicione o seguinte código abaixo do código, que já escritos:

```Javascript
///--- Errors for communicating something more information back to the client.
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


## <a name="14-create-your-server"></a>14: criar o seu servidor
A última coisa a fazer consiste em Adicionar a instância do servidor. A instância de servidor gere as chamadas.

O restify (e Express) ter personalização avançada que pode utilizar com um servidor de REST API. Neste tutorial, utilizamos o programa de configuração mais básico.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15: adicionar as rotas (sem autenticação, por agora)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-run-the-server"></a>16: executar o servidor
É uma boa ideia para testar o seu servidor antes de adicionar a autenticação.

A forma mais fácil para testar o seu servidor é utilizando curl numa linha de comandos. Para tal, precisa de um utilitário simples que pode utilizar para analisar o resultado como JSON. 

1.  Instale a ferramenta JSON que utilizamos nos exemplos seguintes:

    `$npm install -g jsontool`

    Esta ação instala a ferramenta JSON globalmente.

2.  Certifique-se de que a instância do MongoDB está em execução:

    `$sudo mongod`

3.  Altere o diretório para **azuread**, e, em seguida, execute o curl:

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4.  Para adicionar uma tarefa:

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

    A resposta deve ser:

    ```Shell
    HTTP/1.1 201 Created
    Connection: close
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Headers: X-Requested-With
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 5
    Date: Tue, 04 Feb 2014 01:02:26 GMT
    Hello
    ```

5.  Lista as tarefas do Brandon:

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se todos os estes comandos são executados sem erros, está pronto para adicionar o OAuth ao servidor de REST API.

*Tem agora um servidor de REST API com MongoDB!*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17: Adicionar autenticação ao seu servidor de REST API
Agora que tem uma API de REST em execução, configure-o para utilizá-lo com o Azure AD.

Numa linha de comandos, altere o diretório para **azuread**:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>Utilize o oidcbearerstrategy que incluiu com o passport-azure-ad
Até ao momento, criou um servidor TODO de REST normal sem nenhum tipo de autorização. Agora, adicione a autenticação.

Em primeiro lugar, indica que pretende utilizar o Passport. Colocar este direito após a configuração do servidor anterior:

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> Quando escreve APIs, é boa ideia ligar sempre os dados a algo especial do token que o utilizador não pode falsificar. Quando este servidor armazena itens TODO, armazena-os com base no ID de subscrição de utilizador no token (chamado através de token.sub). Coloque o token.sub no campo "proprietário". Isto assegura que apenas esse utilizador pode aceder a TODOs os utilizadores. Ninguém mais pode aceder as TODOs que foram introduzidos. Não há nenhuma exposição na API de "proprietário". Um utilizador externo pode pedir TODOs de outros utilizadores, mesmo que estejam autenticados.
> 
> 

Em seguida, utilize a estratégia de abrir ID Connect portador que vem com `passport-azure-ad`. Coloque isto depois do que pode colar anteriormente:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

O Passport utiliza um padrão semelhante para todas as respetivas estratégias (Twitter, Facebook e assim sucessivamente). Todos os escritores aderem padrão. A estratégia de passar uma `function()` que utiliza um token e `done` como parâmetros. A estratégia é devolvida após faz todos os respetivo trabalho. Armazene o utilizador e guarde o token, por isso não terá de voltar a perguntá-lo.

> [!IMPORTANT]
> O código anterior entra qualquer utilizador que pode autenticar-se ao seu servidor. Isto é conhecido como registo automático. Num servidor de produção, não pretender permitir que qualquer pessoa sem primeiro passá-los através de um processo de registo que escolher. Isto é, normalmente, o padrão normal que vemos nas aplicações de consumidor. A aplicação pode permitir-lhe registar com o Facebook, mas, em seguida, pede-lhe para introduzir informações adicionais. Se não utilizar um programa da linha de comandos para este tutorial, foi possível extrair o e-mail do objeto do token que é devolvido. Em seguida, pode pedir ao utilizador para introduzir informações adicionais. Porque se trata de um servidor de teste, adicione o utilizador diretamente para a base de dados em memória.
> 
> 

### <a name="protect-endpoints"></a>Proteger pontos finais
Proteger pontos finais, especificando o **passport.authenticate()** chamar com o protocolo que pretende utilizar.

Pode editar a rota no código de servidor para uma opção mais avançada:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18: executar novamente a aplicação de servidor
Utilize curl novamente para ver se houver proteção de OAuth 2.0 nos seus pontos finais. Efetuar este procedimento antes de executar qualquer um dos SDKs de cliente em relação a este ponto final. Os cabeçalhos devolvidos devem informar se a autenticação está a funcionar corretamente.

1.  Certifique-se de que o seu isntance do MongoDB está em execução:

    `$sudo mongod`

2.  Mude para o **azuread** diretório e, em seguida, utilizar curl:

    `$ cd azuread`

    `$ node server.js`

3.  Tente um POST básico:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Uma resposta 401 indica que a camada do Passport está a tentar redirecionar para o ponto final de autorização, que é exatamente o que pretende.

*Tem agora um serviço de REST API que utiliza o OAuth 2.0!*

Foi removido como pode fazer com este servidor sem utilizar um cliente do OAuth 2.0 compatível. Para tal, terá de consultar um tutorial adicional.

## <a name="next-steps"></a>Passos seguintes
Para referência, o exemplo concluído (sem os valores de configuração) é fornecido tal como [um ficheiro. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). Também pode cloná-lo a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Agora, pode passar a tópicos mais avançados. Pode querer tente [proteger uma aplicação de web do Node.js utilizando o ponto final v 2.0](active-directory-v2-devquickstarts-node-web.md).

Seguem-se alguns recursos adicionais:

* [Guia para programadores do Azure AD v 2.0](active-directory-appmodel-v2-overview.md)
* [Etiqueta de "azure-active-directory" capacidade excedida da pilha](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança dos nossos produtos
Aconselhamo-lo para se inscrever ser notificado quando ocorrem incidentes de segurança. No [as notificações de segurança técnica Microsoft](https://technet.microsoft.com/security/dd252948) página, Subscrever alertas Advisories de segurança.

