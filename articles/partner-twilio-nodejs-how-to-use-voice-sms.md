---
title: Utilizar Twilio de voz, VoIP e mensagens de SMS no Azure
description: "Saiba como efetuar uma chamada telefónica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos de código escrito no Node.js."
services: 
documentationcenter: nodejs
author: devinrader
manager: wpickett
editor: 
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: wpickett
ms.openlocfilehash: f347540c78be712fc46d1d36b47ca4e23a62e28a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Utilizar Twilio de voz, VoIP e mensagens de SMS no Azure
Este guia demonstra como criar aplicações que comunicam com Twilio e node.js no Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>O que é Twilio?
Twilio é uma plataforma de API que torna mais fácil para os programadores tornar receber chamadas telefónicas, enviar e receber mensagens de texto e incorporar VoIP chamar em aplicações móveis baseada no browser e nativas. Vamos brevemente passam como isto funciona antes diving.

### <a name="receiving-calls-and-text-messages"></a>Receber chamadas e as mensagens de texto
Twilio permite aos programadores [comprar números de telefone programável] [ purchase_phone] que podem ser utilizadas para enviar e receber chamadas e as mensagens de texto. Quando um número de Twilio recebe uma chamada de entrada ou de texto, Twilio enviará a aplicação web um HTTP POST ou GET pedido, pedimos-lhe para obter instruções sobre como lidar com o texto ou chamada. O servidor irá responder ao pedido HTTP do Twilio com [TwiML][twiml], um conjunto simple de etiquetas XML que contém instruções sobre como processar uma chamada ou texto. Exemplos de TwiML veremos aparecer apenas num determinado momento.

### <a name="making-calls-and-sending-text-messages"></a>Efetuar chamadas e enviar mensagens de texto
Ao efetuar pedidos de HTTP para a API de serviço web do Twilio, os programadores podem enviar mensagens de texto ou iniciar saídas chamadas telefónicas. Para chamadas de saída, o programador deve também especificar um URL que devolve TwiML instruções sobre como processar a chamada de saída, uma vez que está ligado.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Ao incorporar VoIP capacidades no código de IU (JavaScript, iOS ou Android)
Twilio fornece um SDK do lado do cliente que pode ativar qualquer browser da web de ambiente de trabalho, a aplicação iOS ou a aplicação Android num telefone VoIP. Neste artigo, iremos focar-nos sobre como utilizar VoIP chamar no browser. Para além de *Twilio JavaScript SDK* em execução no browser, uma aplicação do lado do servidor (nossa aplicação node.js) tem de ser utilizada para emitir um token"capacidade" para o cliente de JavaScript. Pode ler mais sobre como utilizar VoIP com o node.js [no blogue de desenvolvimento do Twilio][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Inscrever-se no Twilio (Microsoft desconto)
Antes de utilizar os serviços do Twilio, deve primeiro [inscrever-se para uma conta][signup]. Os clientes do Microsoft Azure recebem um desconto especial - [não se esqueça de iniciar sessão aqui][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Criar e implementar um Web site do Azure de node.js
Em seguida, terá de criar um Web site do node.js em execução no Azure. [A documentação oficial para fazer isto está localizada aqui][azure_new_site]. Um nível elevado, que irá ser fazer o seguinte:

* Inscrever-se numa conta do Azure, se ainda não tiver um
* Utilizar a consola de administração do Azure para criar um novo Web site
* Adição de suporte de controlo de origem (iremos assumir que utilizou o git)
* Criar um ficheiro `server.js` com uma aplicação web node.js simples
* Implementar esta aplicação simple no Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Configurar o módulo Twilio
Em seguida, vamos começar a escrever uma aplicação node.js simples que utiliza a API do Twilio. Antes de Vamos começar, precisamos de configurar o nosso credenciais de conta do Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurar credenciais do Twilio nas variáveis de ambiente de sistema
Para efetuar pedidos autenticados contra o back-end do Twilio, precisamos de nossa conta SID e um token de autenticação, que funcione como o nome de utilizador e palavra-passe definida para a nossa conta do Twilio. É a forma mais segura de configurá-los para utilização com o módulo de nó no Azure através de variáveis de ambiente de sistema, que pode configurar diretamente na consola de administração do Azure.

Selecione o seu Web site node.js e clique na ligação "Configurar".  Se se deslocar para baixo um bit, verá uma área onde pode definir propriedades de configuração para a sua aplicação.  Introduza as suas credenciais de conta do Twilio ([encontrado na consola do Twilio][twilio_console]) conforme mostrado - certificar-se de que nome `TWILIO_ACCOUNT_SID` e `TWILIO_AUTH_TOKEN`, respetivamente:

![Consola de administração do Azure][azure-admin-console]

Assim que tiver configurado estas variáveis, reinicie a aplicação na consola do Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Declarar o módulo Twilio no Package. JSON
Em seguida, temos de criar um Package. JSON para gerir o nosso dependências do módulo de nó através do [npm]. No mesmo nível, como o `server.js` de ficheiros que criou no *Azure/node.js* tutorial, crie um ficheiro denominado `package.json`.  Dentro deste ficheiro, coloque o seguinte:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Isto declara o módulo twilio como uma dependência, bem como o populares [Express estrutura web] [ express] e o motor de modelo EJS.  Okay, agora vamos está tudo pronto - vamos escrever alguns códigos!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Efetuar uma chamada de saída
Vamos criar um formulário simple que irá colocar uma chamada para um número, que vamos escolher. Abra `server.js`e introduza o seguinte código. Tenha em atenção onde diz "CHANGE_ME" - Coloque o nome do seu site do azure não existe:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Em seguida, crie um diretório denominado `views` - dentro neste diretório, crie um ficheiro denominado `index.ejs` com o seguinte conteúdo:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Agora, implemente o seu Web site para o Azure e abrir a home page. Deverá conseguir introduzir o número de telefone no campo de texto e receber uma chamada a partir do seu número de Twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Envie uma mensagem SMS
Agora, vamos configurar uma interface de utilizador e o formulário processar lógica para enviar uma mensagem de texto. Abra `server.js`e adicione o seguinte código após a última chamada para `app.post`:

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

No `views/index.ejs`, adicione outro formulário dos primeiro para submeter um número e uma mensagem de texto:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Voltar a implementar a aplicação no Azure e, agora deve conseguir submeter que formam e enviar por si (ou qualquer um dos seus amigos mais próximos) de uma mensagem de texto!

<a id="nextsteps"/>

## <a name="next-steps"></a>Passos Seguintes
Agora que tem aprendeu as noções básicas do node.js e Twilio a utilizar para criar aplicações que comunicarem. Mas estes exemplos barely scratch a superfície, o que é possível com Twilio e node.js. Para obter mais informações através do Twilio com o node.js, consulte os seguintes recursos:

* [Docs oficial do módulo][docs]
* [Tutorial de VoIP com aplicações node.js][voipnode]
* [Votr - um voto de aplicação com o node.js e CouchDB (três partes) de SMS em tempo real][votr]
* [Programação de par no browser com o node.js][pair]

Esperamos que adoram pirataria node.js e Twilio no Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
