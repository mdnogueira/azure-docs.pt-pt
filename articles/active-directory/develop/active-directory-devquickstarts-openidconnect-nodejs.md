---
title: "Introdução ao Azure AD início de sessão e fim de sessão com o Node.js | Microsoft Docs"
description: "Saiba como criar uma aplicação web Node.js Express MVC que se integra com o Azure AD para início de sessão."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 13317b016f9ff3955f376b858645c42668b0de42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="nodejs-web-app-sign-in-and-sign-out-with-azure-ad"></a>Aplicação web de node.js início de sessão e fim de sessão com o Azure AD
Aqui, utilizamos o Passport para:

* Sessão do utilizador para a aplicação no Azure Active Directory (Azure AD).
* Apresentar informações sobre o utilizador.
* O utilizador fora da aplicação de início de sessão.

Passport é o middleware de autenticação para Node.js. Flexível e modulares, Passport pode ser discretamente ignoradas para qualquer baseada em Express ou restify aplicação web. Um conjunto abrangente de estratégias suporta a autenticação, que utiliza um nome de utilizador e palavra-passe, Facebook, Twitter e muito mais. Desenvolvemos uma estratégia para o Microsoft Azure Active Directory. Estamos a instalar este módulo e, em seguida, adicione o Microsoft Azure Active Directory `passport-azure-ad` Plug-in.

Para tal, siga os passos seguintes:

1. Registe uma aplicação.
2. Configurar a sua aplicação para utilizar o `passport-azure-ad` estratégia.
3. Utilizar o Passport para emitir pedidos de início de sessão e fim de sessão para o Azure AD.
4. Dados sobre o utilizador de impressão.

O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Para acompanhar, pode [transferir a estrutura da aplicação como um ficheiro. zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) ou clonar a estrutura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

A aplicação concluída é fornecida no final deste tutorial bem.

## <a name="step-1-register-an-app"></a>Passo 1: Registar uma aplicação
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No menu na parte superior da página, selecione a sua conta. Sob o **diretório** lista, escolha o inquilino do Active Directory onde é necessário registar a sua aplicação.

3. Selecione **mais serviços** no menu no lado esquerdo do ecrã e, em seguida, selecione **do Azure Active Directory**.

4. Selecione **registos de aplicação**e, em seguida, selecione **adicionar**.

5. Siga as instruções para criar um **aplicação Web** e/ou **end WebAPI**.
  * O **nome** da aplicação descreve a aplicação aos utilizadores.

  * O **URL de início de sessão** é o URL de base da sua aplicação.  Predefinição a estrutura é ' http://localhost:3000/auth/openid/devolver '.

6. Depois de registar, do Azure AD atribui a aplicação um ID de aplicação único. É necessário que este valor nas secções seguintes, por isso, copie-o partir da página da aplicação.
7. Do **definições** -> **propriedades** página para a sua aplicação, atualize o URI de ID de aplicação. O **URI de ID de aplicação** é um identificador exclusivo para a sua aplicação. A Convenção de consiste em utilizar o formato `https://<tenant-domain>/<app-name>`, por exemplo: `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-add-prerequisites-to-your-directory"></a>Passo 2: Adicionar pré-requisitos ao diretório
1. Na linha de comandos, altere os diretórios para a pasta raiz se não estiver já existir, e, em seguida, execute os seguintes comandos:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Além disso, terá de `passport-azure-ad`:
    * `npm install passport-azure-ad`

Esta ação instala as bibliotecas que `passport-azure-ad` depende.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Passo 3: Configurar a sua aplicação para utilizar a estratégia de passport-nó-js
Aqui, iremos configurar Express para utilizar o protocolo de autenticação OpenID Connect.  Passport é utilizado para efetuar várias ações, incluindo pedidos de início de sessão e fim de sessão do problema, gerir a sessão do utilizador e obter informações sobre o utilizador.

1. Para começar, abra o `config.js` ficheiro na raiz do projeto e, em seguida, introduza valores de configuração da sua aplicação de `exports.creds` secção.

  * O `clientID` é o **Id da aplicação** atribuída à sua aplicação no portal de registo.

  * O `returnURL` é o **Uri de redirecionamento** que introduziu no portal.

  * O `clientSecret` é o segredo que gerou no portal.

2. Em seguida, abra o `app.js` ficheiro na raiz do projeto. Em seguida, adicione a seguinte chamada ao invocar o `OIDCStrategy` estratégia que vem com `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Depois disso, utilize a estratégia de que acabou de referenciar para processar os nossos pedidos de início de sessão.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
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
O Passport utiliza um padrão semelhante para todas as respetivas estratégias (Twitter, Facebook e assim sucessivamente) que todos os escritores aderem. Observar a estratégia, pode ver que podemos transmitir-uma função que tem um token e um efectuada como parâmetros. A estratégia de volta para nós depois de suportar o respetivo trabalho. Em seguida, queremos armazenar o utilizador e guarde o token, pelo que não temos a pedi-la.

> [!IMPORTANT]
O código anterior aceita qualquer utilizador que ocorre autenticar para o nosso servidor. Isto é conhecido como registo automático. Recomendamos que não permitem a qualquer pessoa autenticado num servidor de produção sem primeiro passá-los registar através de um processo que que opte por utilizar. Isto é, normalmente, o padrão normal que vemos nas aplicações de consumidor que lhe permitem registar com o Facebook, mas, em seguida, pedir-lhe fornecer informações adicionais. Se isto não foram uma aplicação de amostra, poderíamos extrair o endereço de correio eletrónico do utilizador do objeto do token que é devolvido e, em seguida, pedido ao utilizador para preencher informações adicionais. Porque se trata de um servidor de teste, iremos adicioná-los para a base de dados em memória.


4. Em seguida, vamos adicionar os métodos que nos permitam controlar os utilizadores com sessão iniciada como requerido pelo Passport. Estes métodos incluem serializar e anular a serialização as informações do utilizador.

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
            done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
            findByEmail(id, function (err, user) {
                done(err, user);
            });
            });

            // array to hold signed-in users
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

5.  Em seguida, vamos adicionar código para carregar o motor Express. Aqui, utilizamos o /views predefinido e padrão de /routes Express fornece.

    ```JavaScript

        // configure Express (section 2)

            var app = express();
            app.configure(function() {
          app.set('views', __dirname + '/views');
          app.set('view engine', 'ejs');
          app.use(express.logger());
          app.use(express.methodOverride());
          app.use(cookieParser());
          app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
          app.use(bodyParser.urlencoded({ extended : true }));
          // Initialize Passport!  Also use passport.session() middleware, to support
          // persistent login sessions (recommended).
          app.use(passport.initialize());
          app.use(passport.session());
          app.use(app.router);
          app.use(express.static(__dirname + '/../../public'));
        });

    ```

6. Por fim, vamos adicionar as rotas que entregam os pedidos reais início de sessão para o `passport-azure-ad` motor:


       ```JavaScript

        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

            // GET /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.get('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });

            // POST /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.post('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });
       ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Passo 4: Utilizar Passport para emitir pedidos de início de sessão e fim de sessão para o Azure AD
A aplicação agora está corretamente configurada para comunicar com o ponto final, utilizando o protocolo de autenticação OpenID Connect.  `passport-azure-ad`tem Tratado dos todos os detalhes das mensagens de autenticação de composição, tokens de validação do Azure AD e manutenção de sessões de utilizador. Tudo o que permanece é fornecer aos utilizadores uma forma de iniciar sessão e terminar sessão e recolha de informações adicionais sobre os utilizadores com sessão iniciada.

1. Em primeiro lugar, vamos adicionar o predefinido, início de sessão, conta e fim de sessão métodos para a nossa `app.js` ficheiro:

    ```JavaScript

        //Routes (section 4)

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

2.  Vamos rever destas partes detalhadamente:

  * O `/`rota redireciona para a vista de index.ejs, transmitir o utilizador no pedido (se existir).
  * O `/account` encaminhar primeiro *assegura que sejam autenticados* (implementamos que no exemplo seguinte) e, em seguida, transmite o utilizador no pedido de modo a que iremos pode obter informações adicionais sobre o utilizador.
  * O `/login` rota chama o autenticador nosso azuread openidconnect de `passport-azuread`. Se que não for bem sucedido, o utilizador será redirecionado novamente para /login.
  * O `/logout` encaminhar simplesmente chamadas logout.ejs (e rota), que limpa cookies e, em seguida, devolve o utilizador novamente para index.ejs.

3. Para a última parte do `app.js`, vamos adicionar o **EnsureAuthenticated** método que é utilizado no `/account`, conforme mostrado anteriormente.

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. Por fim, vamos criar o próprio servidor no `app.js`:

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Passo 5: Para apresentar o nosso utilizador no Web site, crie as vistas e rotas no Express
Agora `app.js` está concluída. Precisamos simplesmente de adicionar as rotas e vistas que apresentam informações vamos obter ao utilizador, bem como lidar com o `/logout` e `/login` rotas que foi criada.

1. Crie a rota `/routes/index.js` no diretório de raiz.

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. Crie a rota `/routes/user.js` no diretório de raiz.

                ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
                ```

 Estes transferem o pedido para o nosso vistas, incluindo o utilizador se estiver presente.

3. Crie a vista `/views/index.ejs` no diretório de raiz. Esta é uma página simple que chama a nossa métodos de início de sessão e fim de sessão e permite-nos captar informações da conta. Tenha em atenção que podemos utilizar política de acesso `if (!user)` como o utilizador que está a ser transmitido através do pedido é provas temos um utilizador com sessão iniciada.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. Criar o `/views/account.ejs` ver no diretório de raiz, para que iremos possa visualizar informações adicionais que `passport-azuread` colocou no pedido de utilizador.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Certifiquemo-este aspeto boa adicionando um esquema. Criar o ' / vista dos views/layout.ejs sob o diretório de raiz.

    ```HTML

    <!DOCTYPE html>
    <html>
        <head>
            <title>Passport-OpenID Example</title>
        </head>
        <body>
            <% if (!user) { %>
                <p>
                <a href="/">Home</a> |
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

##<a name="next-steps"></a>Passos seguintes
Por fim, crie e execute a sua aplicação. Executar `node app.js`e, em seguida, aceda a `http://localhost:3000`.

Inicie sessão com uma conta Microsoft pessoal ou uma conta escolar ou profissional e repare como a identidade do utilizador é refletida na lista /account. Tem agora uma aplicação web que está protegida com protocolos padrão da indústria que podem autenticar os utilizadores com ambas as respetivas contas pessoais e de trabalho/escola.

Para sua referência, o exemplo concluído (sem os valores de configuração) [é fornecido como um ficheiro .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Em alternativa, pode cloná-lo a partir do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Agora pode mover para tópicos mais avançados. Pode querer tente:

[Proteger uma API Web com o Azure AD](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
