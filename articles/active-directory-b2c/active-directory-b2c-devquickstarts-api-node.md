<properties
    pageTitle="Pré-visualização do B2C: Proteger uma API Web utilizando Node.js | Microsoft Azure"
    description="Como criar uma API Web de Node.js que aceita tokens de um inquilino do B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="brandwe"/>

# Pré-visualização do B2C: Proteger uma API Web utilizando Node.js

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] Este artigo não aborda como implementar o início de sessão, inscrição e gestão de perfis com o Azure AD B2C. Concentra-se nas chamadas das APIs da Web depois de o utilizador ser autenticado. Se ainda não o fez, deve começar com o [Tutorial de introdução à aplicação Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure Active Directory B2C.


> [AZURE.NOTE]  Este exemplo foi escrito para fazer uma ligação utilizando a nossa [aplicação de exemplo B2C para iOS](active-directory-b2c-devquickstarts-ios.md). Primeiro, explore esta passagem e, em seguida, acompanhe com esse exemplo.

**Passport** é o middleware de autenticação do Node.js. Extremamente flexível e modular, o Passport pode ser instalado discretamente em qualquer aplicação Web baseada em Express ou Restify. Um conjunto abrangente de estratégias suporta a autenticação utilizando um nome de utilizador e palavra-passe, o Facebook, o Twitter e muito mais. Desenvolvemos uma estratégia para o Azure Active Directory (Azure AD). Instala este módulo e, em seguida, adiciona o plug-in `passport-azure-ad` do Azure AD.

Para efetuar este procedimento, precisa de:

1. Registar uma aplicação com o Azure AD.
2. Configurar a aplicação para utilizar o plug-in `azure-ad-passport` do Passport.
3. Configure uma aplicação de cliente para chamar a API Web da “lista de ações a fazer”.

O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). Para acompanhar, pode [transferir a estrutura da aplicação como um ficheiro.zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

A aplicação concluída é fornecida no final deste tutorial.

> [AZURE.WARNING]   Para esta pré-visualização do B2C, tem de utilizar a mesma **ID de cliente**/**ID da aplicação** e políticas para o servidor de tarefas da API Web e para o cliente que se liga ao mesmo. Isto também se aplica aos nossos tutoriais de iOS e Android. Se anteriormente tiver criado uma aplicação em qualquer um desses Inícios Rápidos, utilize esses valores; não crie valores novos.


## Obter um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino.  Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc.  Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de continuar.

## Criar uma aplicação

Em seguida, precisa de criar uma aplicação no seu diretório do B2C que dá ao Azure AD algumas informações necessárias para comunicar de forma segura com a aplicação. Neste caso, a aplicação cliente e a API Web serão representadas por uma única **ID de Aplicação** porque constituem uma aplicação lógica. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

- Incluir uma **aplicação Web/API Web** na aplicação
- Introduzir `http://localhost/TodoListService` como um **URL de resposta**. É o URL predefinido para este exemplo de código.
- Criar um **Segredo de aplicação** para a aplicação e copiá-lo. Precisará dele mais tarde. Precisará dele em breve. Tenha em atenção que este valor de ser [XML de escape](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de o utilizar.
- Copiar a **ID da Aplicação** atribuída à aplicação. Também irá precisar deste mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar as políticas

No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém três experiências de identidade: inscrição, início de sessão e início de sessão utilizando o Facebook. Tem de criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  Quando criar as três políticas, não se esqueça de:

- Escolher o **Nome a apresentar** e os atributos de inscrição na política de inscrição.
- Escolher as afirmações de aplicação **Nome a apresentar** e **ID de objeto** em cada política.  Também pode escolher outras afirmações.
- Copie o **Nome** de cada política depois de criá-la. Deve ter o prefixo `b2c_1_`.  Precisará destes nomes mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, está pronto para criar a aplicação.

Tenha em atenção que este artigo não aborda como utilizar as políticas que acabou de criar. Para saber mais sobre como funcionam as políticas no Azure AD B2C, comece com o [Tutorial de introdução da aplicação Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Transferir Node.js para a plataforma

Para utilizar este exemplo com êxito, tem de ter uma instalação do Node.js em execução.

Instalar Node.js de [nodejs.org](http://nodejs.org).

## Instalar MongoDB para a plataforma

Para utilizar este exemplo com êxito, tem de ter também uma instalação do MongoDB em execução. Utilizará o MongoDB para tornar a API REST persistente em várias instâncias de servidor.

Instalar MongoDB de [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Esta passagem parte do princípio de que irá utilizar os pontos finais do servidor e a instalação predefinida para o MongoDB, que no momento desta redação é `mongodb://localhost`.

## Instalar os módulos Restify na API Web

Utilizará o Restify para construir a API REST. O Restify é uma estrutura de aplicação Node.js mínima e flexível derivada do Express. Tem um conjunto robusto de funcionalidades para a criação de APIs REST por cima do Connect.

### Instalação do Restify

Na linha de comandos, altere o diretório para `azuread`. Se o diretório `azuread` não existe, crie-o.

`cd azuread` ou `mkdir azuread;`

Introduza o seguinte comando:

`npm install restify`

Este comando instala o Restify.

#### Obteve um erro?

Em alguns sistemas operativos, quando utiliza `npm`, poderá receber o erro `Error: EPERM, chmod '/usr/local/bin/..'` e um pedido para que execute a conta como administrador. Se isto acontecer, utilize o comando `sudo` para executar `npm` a um nível de privilégio mais elevado.

#### Obteve um erro de DTrace?

Poderá ver assim ao instalar o Restify:

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

O resultado do comando deve ter um aspeto semelhante a isto:

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

## Instalar o Passport na API Web

[Passport](http://passportjs.org/) é o middleware de autenticação do Node.js. Extremamente flexível e modular, o Passport pode ser instalado discretamente em qualquer aplicação Web baseada em Express ou Restify. Um conjunto abrangente de estratégias suporta a autenticação utilizando um nome de utilizador e palavra-passe, o Facebook, o Twitter e muito mais. Desenvolvemos uma estratégia para o Azure AD. Instalará este módulo e, em seguida, adicionará o plug-in de estratégia do Azure AD.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí.

Introduza o comando seguinte para instalar o Passport:

`npm install passport`

O resultado do comando deve ser semelhante a isto:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## Adicionar passport-azuread à API Web 

Em seguida, adicione a estratégia de OAuth utilizando `passport-azuread`, um conjunto de estratégias que ligam o Azure AD com o Passport. Utilize esta estratégia para os tokens de portador no exemplo API REST.

> [AZURE.NOTE] Embora o OAuth2 forneça uma estrutura onde pode ser emitido qualquer tipo de token conhecido, apenas alguns tipos de tokens passaram a ser amplamente utilizados. Os tokens para proteger os pontos finais são tokens de portador. Estes são o tipo de tokens mais emitido no OAuth2. Várias implementações partem do princípio de que os tokens de portador são o único tipo de token emitido.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí.

Introduza o comando seguinte para instalar o módulo `passport-azure-ad` do Passport:

`npm install passport-azure-ad`

O resultado do comando deve ser semelhante a isto:

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

## Adicione módulos do MongoDB à API Web

Utilizará o MongoDB como arquivo de dados. Por esse motivo, precisa de instalar o Mongoose, um plug-in amplamente utilizado na gestão de modelos e esquemas, e o controlador de base de dados para o MongoDB, também designado por MongoDB.

* `npm install mongoose`
* `npm install mongodb`

## Instalar módulos adicionais

Em seguida, instale os restantes módulos necessários.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Introduza os comandos seguintes para instalar os módulos no seu diretório `node_modules`:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## Crie um ficheiro de server.js com as suas dependências

O ficheiro `server.js` proporcionará a maior parte das funcionalidades do seu servidor de API Web. Adicionará a maior parte do nosso código a este ficheiro. Para produção, deve refatorar a funcionalidade em ficheiros mais pequenos, como rotas separadas e controladores. Para este tutorial, utilize server.js para esta funcionalidade.

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

Guarde o ficheiro. Regressará ao mesmo mais tarde.

## Criar um ficheiro de config.js para armazenar as suas definições do Azure AD

Este ficheiro de código transmite os parâmetros de configuração a partir do Portal do Azure AD para o ficheiro `Passport.js`. Criou estes valores de configuração quando adicionou a API Web ao portal na primeira parte da passagem. Vamos explicar o que colocar os valores destes parâmetros depois de copiar o código.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Crie um ficheiro `config.js` num editor. Adicione as seguintes informações:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Valores necessários

`IdentityMetadata`: Aqui é onde `passport-azure-ad` vai procurar os seus dados de configuração para o fornecedor de identidade. Também irá procurar aqui as chaves para validar os tokens Web JSON. É provável que não pretenda alterar esta opção se utilizar o Azure AD.

`audience`: O uniform resource identifier (URI) do portal do que identifica o seu serviço. O nosso exemplo utiliza `http://localhost/TodoListService`.

`tenantName`: O nome do seu inquilino (por exemplo, **contoso.onmicrosoft.com**).

`policyName`: A política que pretende que valide os tokens recebidos pelo seu servidor. Deve ser a mesma política utilizada na aplicação cliente no início de sessão.

> [AZURE.NOTE] Para a nossa pré-visualização do B2C, utilize as mesmas políticas para configurar o cliente e o servidor. Se já tiver concluído uma passagem e criado estas políticas, não terá de fazê-lo novamente. Uma vez concluída a passagem, não deverá ser necessário configurar novas políticas para passagens de cliente no site.

## Adicionar configuração ao seu ficheiro server.js

Precisa de ler os valores do ficheiro `config.js` que acabou de criar na aplicação. Para tal, adicione o ficheiro `.config` como um recurso necessário à aplicação e, em seguida, defina as variáveis globais às existentes no documento `config.js`.

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
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Adicionar as informações de esquema e modelo MongoDB utilizando o Mongoose

A preparação prévia recompensará quando juntar estes três ficheiros num serviço API REST.

Para esta passagem, utilize o MongoDB para armazenar as suas tarefas, como mencionado anteriormente.

No ficheiro `config.js` que criou, chamou a base de dados **tasklist**. Isso foi também o que colocou no final da URL da ligação `mongoose_auth_local`. Não precisa de criar esta base de dados previamente no MongoDB. A base de dados será criada por si na primeira execução da aplicação de servidor, caso ainda não exista.

Depois de indicar ao servidor qual a base de dados de MongoDB a utilizar, precisa de escrever alguns códigos adicionais para criar o modelo e o esquema das suas tarefas de servidor.

### Expandir o modelo

Este modelo de esquema é muito simples. Pode expandi-lo conforme necessário.

`name`: Quem é atribuído à tarefa. Isto é uma **cadeia**.

`task`: A tarefa propriamente dita. Isto é uma **cadeia**.

`date`: A data em que a tarefa deve ser concluída. Isto é um **datetime**.

`completed`: Se a tarefa está ou não concluída. Isto é um **Booleano**.

### Criar o esquema no código

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Crie o ficheiro `server.js` num editor. Adicione a informação seguinte abaixo da entrada de configuração:

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Este procedimento irá ligar ao servidor MongoDB e devolver um objeto de esquema.

### Utilize o esquema para criar o modelo no código

Abaixo do código que escreveu acima, adicione o seguinte código:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Crie primeiro o esquema e, em seguida, crie um objeto de modelo que utilizará para armazenar os dados em todo o código, quando definir as suas **rotas**.

## Adicionar rotas ao seu servidor de tarefas da API REST

Agora que tem um modelo de base de dados com o qual trabalhar, adicione as rotas a utilizar com o servidor da API REST.

### Sobre rotas no Restify

As rotas funcionam no Restify exatamente da mesma maneira que funcionam com a pilha Express. Definem-se rotas utilizando o URI que se espera que as aplicações de cliente chamem. Na maioria dos casos, deve definir as rotas num ficheiro separado. Para este tutorial, irá colocar as rotas no ficheiro `server.js`. Para fins de produção, recomendamos que sejam colocadas num ficheiro próprio.

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

Este é o padrão no nível mais básico. O Restify e o Express podem fornecer funcionalidades muito mais aprofundada, tais como, definir tipos de aplicações e efetuar encaminhamentos complexos para diferentes pontos finais. Para este tutorial, manteremos estas rotas simples.

#### Adicionar rotas predefinidas ao seu servidor

Agora, irá adicionar as rotas CRUD básicas de **criar**, **obter**, **atualizar** e **eliminar**.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

Crie o ficheiro `server.js` num editor. Adicione as informações seguintes abaixo da entradas de base de dados que criou acima:

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
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
// Delete a task by name
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
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
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
/// Simple returns the list of TODOs that were loaded.
function listTasks(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
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

#### Adicionar tratamento de erro para as rotas

Deve adicionar tratamento de erros para que possa comunicar quaisquer problemas que encontre de forma a que o cliente possa compreender.

Adicione o seguinte código abaixo do código que escreveu acima:

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


## Criar o seu servidor

Agora definiu a base de dados e colocou as rotas. A última coisa que tem de fazer consiste em adicionar a instância de servidor que irá gerir as suas chamadas.

O Restify e o Express fornecem personalização avançada para um servidor de API REST, mas aqui utilizará a configuração mais básica.

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
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
}));
```
## Adicionar as rotas ao servidor (sem autenticação)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
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
## Executar o servidor antes de adicionar suporte a OAuth 

Deve testar o seu servidor antes de adicionar a autenticação.

A forma mais fácil de fazê-lo é utilizar `curl` numa linha de comandos. Mas antes, precisa de um utilitário simples que lhe permita analisar o resultado como JSON. Em primeiro lugar, instale a ferramenta JSON.

`$npm install -g jsontool`

Esta ação instala a ferramenta JSON globalmente. Depois de instalar a ferramenta JSON, teste o servidor:

Certifique-se de que a instância do MongoDB está em execução.

`$sudo mongodb`

Mude para o diretório `azuread` e utilize `curl`.

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
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

Adicione uma tarefa:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

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
Pode listar as tarefas para o utilizador “Brandon” desta forma:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se isto funcionar, está pronto para adicionar o OAuth ao servidor de API REST.

Tem um servidor de API REST com MongoDB.

## Adicionar autenticação ao seu servidor de API REST

Agora que um servidor de API REST está em execução, pode torná-lo útil em relação ao Azure AD.

Na linha de comandos, altere o diretório para `azuread`, caso ainda não esteja aí:

`cd azuread`

### Utilize o OIDCBearerStrategy que está incluído no passport-azure-ad

Até agora, criou um servidor ToDo de REST normal sem nenhum tipo de autorização. Agora pode começar a juntar a autorização.

Em primeiro lugar, precisa de indicar que pretende utilizar o Passport. Adicione-o diretamente abaixo da configuração de servidor:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Quando escreve APIs, deve ligar sempre os dados a algo especial do token que o utilizador não pode falsificar. Quando o servidor armazena itens ToDo, fá-lo com base na **ID de objeto** de utilizador no token (chamado através de token.oid) que é incluído no campo “proprietário”. Isto garante que apenas o utilizador pode aceder aos seus itens ToDo e que ninguém mais pode aceder aos itens ToDo que foram introduzidos. Não existe nenhuma exposição na API de “proprietário”, de modo que um utilizador externo pode pedir itens ToDo de outras pessoas, mesmo que estejam autenticados.

Em seguida, utilize a estratégia de portador que vem com `passport-azure-ad`. (Por agora vamos apenas analisar o código). Coloque isto a seguir ao que escreveu acima:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
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
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
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

O Passport utiliza um padrão para todas as estratégias (incluindo Twitter e Facebook) que é semelhante às estratégias a que todos os escritores aderem. Passe-o como uma `function()` que tem `token` e `done` como parâmetros. A estratégia é-lhe devolvida depois de fazer o seu trabalho. Em seguida, deve armazenar o utilizador e guardar o token para que não seja necessário voltar a pedi-lo.

> [AZURE.IMPORTANT]
O código acima aceita qualquer utilizador que se autentique com o seu servidor. Este processo é conhecido como auto-registo. Em servidores de produção, não quereria que entrassem utilizadores sem primeiro passá-los por um processo de registo que tenha escolhido. Este é o padrão normal que vemos nas aplicações de consumidor que lhe permitem registar utilizando o Facebook, mas que em seguida lhe pedem para preencher informações adicionais. Se não fosse um programa de linha de comandos, poderíamos extrair o email do objeto do token que é devolvido e, em seguida, pedir aos utilizadores para preencher informações adicionais. Dado que se trata de um servidor de teste, adicionamos simplesmente utilizadores à base de dados em memória.

### Proteger pontos finais

Os pontos finais são protegidos ao especificar a chamada `passport.authenticate()` utilizando o protocolo que pretende utilizar.

Pode editar a rota no código de servidor para fazer algo mais interessante:

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## Executar a aplicação de servidor novamente para verificar se a rejeita

Pode utilizar novamente `curl` para ver se tem agora proteção do OAuth2 em relação aos pontos finais. Faça-o antes de executar qualquer uma das nossas SDKs de cliente em relação a este ponto final. Os cabeçalhos devolvidos devem ser suficientes para lhe indicar que está no caminho certo.

Certifique-se de que a instância do MongoDB está em execução:

    $sudo mongodb

Mude para o diretório e utilize `curl`:

    $ cd azuread
    $ node server.js

Tente um POST básico:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

A resposta pretendida é um erro 401. Indica que a camada do Passport está a tentar redirecionar para o ponto final de autorização.


## Agora tem um serviço de API REST que utiliza o OAuth2

Fez tudo o que podia com este servidor sem utilizar um cliente compatível com o OAuth2. Para isso, precisará de uma passagem adicional.

Se desenha apenas obter informações sobre como implementar uma API REST utilizando o Restify e OAuth2, tem agora código suficiente para que possa continuar a desenvolver o seu serviço e a criar com base neste exemplo.

Para sua referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). Também pode clonar a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Passos seguintes

Agora pode aceder a tópicos mais avançados, como:

[Ligar a uma API Web utilizando o iOS com o B2C](active-directory-b2c-devquickstarts-ios.md)



<!--HONumber=Jun16_HO2-->


