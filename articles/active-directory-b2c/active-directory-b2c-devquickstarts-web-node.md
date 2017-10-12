---
title: "Adicionar início de sessão a uma aplicação Web Node.js para o Azure B2C | Microsoft Docs"
description: "Como criar uma aplicação Web Node.js que inicia sessão dos utilizadores através da utilização de um inquilino do B2C."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: db97f84a-1f24-447b-b6d2-0265c6896b27
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 03/10/2017
ms.author: xerners
ms.openlocfilehash: c85b8f8434d1e837ac96ac63b9b37f990677ed6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: Adicionar início de sessão a uma aplicação Web Node.js

**Passport** é o middleware de autenticação do Node.js. Extremamente flexível e modular, o Passport pode ser instalado discretamente em qualquer aplicação Web baseada em Express ou Restify. Um conjunto abrangente de estratégias suporta a autenticação utilizando um nome de utilizador e palavra-passe, o Facebook, o Twitter e muito mais.

Desenvolvemos uma estratégia para o Azure Active Directory (Azure AD). Instala este módulo e, em seguida, adiciona o plug-in `passport-azure-ad` do Azure AD.

Para efetuar este procedimento, tem de:

1. Registar uma aplicação utilizando o Azure AD.
2. Configurar a sua aplicação para utilizar o plug-in `passport-azure-ad`.
3. Utilizar o Passport para emitir pedidos de início de sessão e fim de sessão para o Azure AD.
4. Imprimir os dados de utilizador.

O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Para acompanhar, pode [transferir a estrutura da aplicação como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip). Também pode clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

A aplicação concluída é fornecida no final deste tutorial.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório do Azure AD B2C

Para poder utilizar o Azure AD B2C, tem de criar um diretório ou inquilino.  Um diretório é um contentor para todos os seus utilizadores, aplicações, grupos, etc. Se ainda não tiver um, [crie um diretório do B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## <a name="create-an-application"></a>Criar uma aplicação

Em seguida, precisa de criar uma aplicação no diretório do B2C. Isto proporciona informações do Azure AD de que necessita para comunicar de forma segura com a sua aplicação. A aplicação de cliente e a API Web serão representadas por um único **ID da aplicação**, uma vez que compõem uma aplicação lógica. Para criar uma aplicação, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário:

- Inclui uma **aplicação Web**/**API Web** na aplicação.
- Introduzir `http://localhost:3000/auth/openid/return` como um **URL de resposta**. É o URL predefinido para este exemplo de código.
- Criar um **Segredo de aplicação** para a aplicação e copiá-lo. Precisará dele mais tarde. Tenha em atenção que este valor de ser [XML de escape](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de o utilizar.
- Copiar a **ID da Aplicação** atribuída à aplicação. Também irá precisar deste mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar as políticas

No Azure AD B2C, cada experiência de utilizador é definida por uma [política](active-directory-b2c-reference-policies.md). Esta aplicação contém três experiências de identidade: inscrição, início de sessão e início de sessão utilizando o Facebook. Tem de criar esta política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Quando criar as três políticas, não se esqueça de:

- Escolher o **Nome a apresentar** e os atributos de inscrição na política de inscrição.
- Escolher as afirmações de aplicação **Nome a apresentar** e **ID de objeto** em cada política. Também pode escolher outras afirmações.
- Copiar o **Nome** de cada política depois de a criar. Deve ter o prefixo `b2c_1_`.  Precisará destes nomes de políticas mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, está pronto para criar a sua aplicação.

Tenha em atenção que este artigo não abrange como utilizar as políticas que acabou de criar. Para saber mais sobre como funcionam as políticas no Azure AD B2C, comece com o [Tutorial de introdução da aplicação Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="add-prerequisites-to-your-directory"></a>Adicionar pré-requisitos ao diretório

Na linha de comandos, altere os diretórios para a pasta raiz, caso ainda não exista. Execute os seguintes comandos:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

Além disso, utilizámos `passport-azure-ad` para a nossa pré-visualização na estrutura do Início Rápido.

- `npm install passport-azure-ad`

Esta ação irá instalar as bibliotecas de que `passport-azure-ad` depende.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Configurar a sua aplicação para utilizar a estratégia Passport-Node.js
Configure o middleware Express para utilizar o protocolo de autenticação OpenID Connect. O Passport será utilizado para emitir pedidos de início de sessão e fim de sessão, gerir sessões de utilizador e obter informações sobre utilizadores, entre outras ações.

Abra o ficheiro `config.js` na raiz do projeto e introduza os valores de configuração da aplicação na secção `exports.creds`.
- `clientID`: O **ID da aplicação** atribuído à sua aplicação no portal de registo.
- `returnURL`: O **URI de redirecionamento** que introduziu no portal.
- `tenantName`: O nome do inquilino da sua aplicação, por exemplo, **contoso.onmicrosoft.com**.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Abra o ficheiro `app.js` na raiz do projeto. Adicione a chamada seguinte para invocar a estratégia `OIDCStrategy` que vem com `passport-azure-ad`.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Utilize a estratégia de acabou de referenciar para processar pedidos de início de sessão.

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
O Passport utiliza um padrão semelhante para todos as respetivas estratégias (incluindo o Twitter e o Facebook). Todos os escritores de estratégias cumprem este padrão. Quando observa a estratégia, pode ver que transmite um `function()` que tem um token e um `done` como os parâmetros. A estratégia é-lhe devolvida depois de fazer todo o trabalho. Armazene o utilizador e guarde o token de modo a que não é necessário voltar a pedi-lo.

> [!IMPORTANT]
O código anterior entra em todos os utilizadores a quem o servidor autentica. Este é registo automático. Quando utilizar servidores de produção, não quer permitir a entrada de utilizadores, a menos que tenham passado por um processo de registo que tiver configurado. Muitas vezes, pode ver este padrão nas aplicações de consumidor. Estas permitem-lhe registar utilizando o Facebook, mas, em seguida, solicitam-lhe o preenchimento de informações adicionais. Se a nossa aplicação não era uma amostra, poderíamos extrair um endereço de e-mail a partir do objeto token devolvido e, em seguida, pedir ao utilizador para preencher as informações adicionais. Porque se trata de um servidor de teste, simplesmente adicionamos utilizadores à base de dados em memória.

Adicione os métodos que lhe permitem controlar utilizadores que tenham iniciado sessão, conforme requerido pelo Passport. Isto inclui informações de utilizador de serialização e não serialização:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

Adicione o código para carregar o motor Express. A seguir, pode ver que utilizamos a predefinição `/views` e o padrão `/routes` que o Express fornece.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Adicione as rotas `POST` que entregam os pedidos reais de início de sessão ao motor `passport-azure-ad`:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Utilizar o Passport para emitir pedidos de início de sessão e fim de sessão para o Azure AD

A aplicação agora está corretamente configurada para comunicar com o ponto final v2.0, utilizando o protocolo de autenticação OpenID Connect. `passport-azure-ad` tem tratado dos detalhes das mensagens de autenticação de composição, tokens de validação do Azure AD e manutenção de sessão do utilizador. Tudo o falta fazer é conceder aos seus utilizadores uma forma para iniciar sessão e terminar sessão, e recolher informações adicionais sobre os utilizadores que têm sessão iniciada.

Primeiro, adicione os métodos predefinição, início de sessão, conta e fim de sessão ao ficheiro `app.js`:

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

Para rever estes métodos em detalhe:
- A rota `/` redireciona para a vista `index.ejs` transferindo o utilizador no pedido (se existir).
- A rota `/account` verifica primeiro se está autenticado (a implementação vem a seguir). Em seguida, transfere o utilizador no pedido de modo a que possa obter informações adicionais sobre o utilizador.
- A rota `/login` chama o autenticador `azuread-openidconnect` de `passport-azure-ad`. Se este não for bem sucedido, a rota redireciona o utilizador de volta a `/login`.
- `logout.ejs` chama simplesmente `/logout` (e a respetiva rota). Isto limpa cookies e, em seguida, devolve o utilizador a `index.ejs`.


Para a última parte do `app.js`, adicione o `EnsureAuthenticated` método que é utilizado na rota `/account`.

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Por fim, crie o próprio servidor no `app.js`.

```JavaScript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>Criar as vistas e rotas no Express para chamar as políticas

O `app.js` está agora concluído. Precisa apenas de adicionar as rotas e vistas que lhe permitem chamar as políticas de início de sessão e inscrição. Estas também processar as rotas `/logout` e `/login` que criou.

Crie a rota `/routes/index.js` no diretório de raiz.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Crie a rota `/routes/user.js` no diretório de raiz.

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Estas rotas simples transferem pedidos para as vistas. Incluem o utilizador, se algum estiver presente.

Crie a vista `/views/index.ejs` no diretório de raiz. Esta é uma página simples que chama as políticas para o início de sessão e fim de sessão. Também pode utilizá-la para captar informações da conta. Tenha em atenção que pode utilizar o `if (!user)` condicional quando o utilizador é transferido no pedido para fornecer provas de que o utilizador tem sessão iniciada.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Crie a vista `/views/account.ejs` no diretório de raiz, para que possa visualizar informações adicionais que `passport-azure-ad` colocam no pedido de utilizador.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Agora pode criar e executar a aplicação.

Executar `node app.js` e navegar para `http://localhost:3000`


Inscreva-se ou inicie sessão na aplicação utilizando o correio eletrónico ou o Facebook. Termine sessão e inicie sessão novamente como um utilizador diferente.

##<a name="next-steps"></a>Passos seguintes

Para sua referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um ficheiro .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Também pode clonar a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Agora pode aceder a tópicos mais avançados. Pode experimentar:

[Proteger uma API Web utilizando o modelo de B2C no Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
