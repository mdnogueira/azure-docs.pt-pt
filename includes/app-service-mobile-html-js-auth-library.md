### <a name="a-nameserver-authahow-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>Como: autenticar com um fornecedor (Fluxo de Servidor)
Para que as Aplicações Móveis giram o processo de autenticação na sua aplicação, tem de registar a aplicação com o seu fornecedor de identidade. Em seguida, no seu Serviço de Aplicações do Azure, tem de configurar o ID da aplicação e o segredo fornecidos pelo seu fornecedor.
Para obter mais informações, consulte o tutorial [Add authentication to your app (Adicionar autenticação à sua aplicação)](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

Depois de registar o seu fornecedor de identidade, chame o método `.login()` com o nome do fornecedor. Por exemplo, para iniciar sessão com o Facebook utilize o seguinte código:

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Os valores válidos para o fornecedor são "aad", "facebook", "google", "microsoftaccount" e "twitter".

> [!NOTE]
> A Autenticação da Google não funciona atualmente através do Fluxo de Servidor.  Para se autenticar com o Google, tem de utilizar um [método de fluxo de cliente](#client-auth).

Neste caso, o Serviço de Aplicações do Azure gere o fluxo de autenticação OAuth 2.0.  Apresenta a página de início de sessão do fornecedor selecionado e gera um token de autenticação do Serviço de Aplicações após o início de sessão bem-sucedido com o fornecedor de identidade. A função de início de sessão, quando concluída, devolve um objeto JSON que expõe o ID de utilizador e o token de autenticação do Serviço de Aplicações nos campos userId e authenticationToken, respetivamente. Este token pode ser colocado em cache e reutilizado até expirar.

###<a name="a-nameclient-authahow-to-authenticate-with-a-provider-client-flow"></a><a name="client-auth"></a>Como: autenticar com um fornecedor (Fluxo de Cliente)

A sua aplicação também pode contactar o fornecedor de identidade independentemente e, em seguida, fornecer o token devolvido ao seu Serviço de Aplicações para autenticação. Este fluxo de cliente permite-lhe fornecer uma experiência de início de sessão único para os utilizadores ou para obter dados de utilizador adicionais do fornecedor de identidade.

#### <a name="social-authentication-basic-example"></a>Exemplo básico de Autenticação nas Redes Sociais

Este exemplo utiliza o SDK cliente do Facebook para autenticação:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
Este exemplo assume que o token fornecido pelo SDK do fornecedor respetivo é armazenado na variável do token.

#### <a name="microsoft-account-example"></a>Exemplo da Conta Microsoft

O exemplo seguinte utiliza o Live SDK, que suporta início de sessão único para aplicações da Loja Windows, através da Conta Microsoft:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});

```

Este exemplo obtém um token do Live Connect, que é fornecido ao seu Serviço de Aplicações, ao chamar a função de início de sessão.

###<a name="a-nameauth-getinfoahow-to-obtain-information-about-the-authenticated-user"></a><a name="auth-getinfo"></a>Como: obter informações sobre o utilizador autenticado

As informações de autenticação podem ser obtidas a partir do ponto final `/.auth/me`, ao utilizar uma chamada HTTP com qualquer biblioteca de AJAX.  Certifique-se de que definiu o cabeçalho `X-ZUMO-AUTH` para o token de autenticação.  O token de autenticação é armazenado no `client.currentUser.mobileServiceAuthenticationToken`.  Por exemplo, para utilizar a API de obtenção:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

A obtenção está disponível como [um pacote de npm](https://www.npmjs.com/package/whatwg-fetch) ou para transferência de browser a partir do [CDNJS](https://cdnjs.com/libraries/fetch). Pode também utilizar jQuery ou outra API de AJAX para obter as informações.  Os dados são recebidos como um objeto JSON.


<!--HONumber=Feb17_HO1-->


