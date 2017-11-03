---
title: "Descrição geral das APIs do nó de reencaminhamento do Azure | Microsoft Docs"
description: "Descrição geral da API de nó de reencaminhamento"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: f9b679f167952076db6df17711dc124931a0880c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Descrição geral da API de nó de ligações híbridas de reencaminhamento

## <a name="overview"></a>Descrição geral

O [ `hyco-ws` ](https://www.npmjs.com/package/hyco-ws) pacote do nó para as ligações híbridas do Azure reencaminhamento está incorporada no e expande o ['ws'](https://www.npmjs.com/package/ws) pacote NPM. Este pacote novamente Exporta todas as exportações esse pacote base e adiciona novas exportações que ativar a integração com a funcionalidade de ligações híbridas do serviço de reencaminhamento do Azure. 

As aplicações existentes que `require('ws')` pode utilizar este pacote com `require('hyco-ws')` em vez disso, que também lhe permite cenários híbridos, em que uma aplicação pode escutar ligações de WebSocket localmente "no interior da firewall" e através de ligações híbridas, todos num o mesmo tempo.
  
## <a name="documentation"></a>Documentação

As APIs são [documentados no pacote principal 'ws'](https://github.com/websockets/ws/blob/master/doc/ws.md). Este artigo descreve como este pacote difere de acordo com esse linha de base. 

As principais diferenças entre o pacote de base e este 'hyco-ws' é que adiciona uma nova classe de servidor, exportada através de `require('hyco-ws').RelayedServer`e alguns métodos de programa auxiliar.

### <a name="package-helper-methods"></a>Métodos de programa auxiliar do pacote

Existem vários métodos de utilitário disponíveis na exportação do pacote que pode referenciar da seguinte forma:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Os métodos de programa auxiliar são para utilização com este pacote, mas também podem ser utilizados por um servidor de nó para ativar os clientes web ou de dispositivo criar os serviços de escuta ou os remetentes. O servidor utiliza estes métodos, transferindo-os URIs incorpore tokens de curta duração. Estes URIs também pode ser utilizado com comuns pilhas de WebSocket não suportam cabeçalhos HTTP de definição para o handshake de WebSocket. Ao incorporar tokens de autorização para o URI é suportado principalmente para os cenários de utilização da biblioteca externa. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Cria uma escuta de reencaminhamento do Azure da ligação híbrida URI válida para o espaço de nomes especificado e o caminho. Este URI, em seguida, pode ser utilizado com a versão de reencaminhamento da classe WebSocketServer.

- `namespaceName`(obrigatório) - o nome qualificado de domínio do espaço de nomes de reencaminhamento do Azure a utilizar.
- `path`(obrigatório) - o nome de uma ligação híbrida de reencaminhamento do Azure existente nesse espaço de nomes.
- `token`(opcional) - um acesso de reencaminhamento emitido anteriormente token que está incorporada no serviço de escuta URI (consulte o exemplo seguinte).
- `id`(opcional) - um identificador de controlo que permite o controlo de ponto a ponto diagnósticos de pedidos.

O `token` valor é opcional e só deve ser utilizado quando não é possível enviar os cabeçalhos de HTTP, juntamente com o handshake de WebSocket, como é o caso com a pilha de W3C WebSocket.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Cria um envio de reencaminhamento do Azure da ligação híbrida URI válido para o espaço de nomes especificado e o caminho. Este URI pode ser utilizado com qualquer cliente de WebSocket.

- `namespaceName`(obrigatório) - o nome qualificado de domínio do espaço de nomes de reencaminhamento do Azure a utilizar.
- `path`(obrigatório) - o nome de uma ligação híbrida de reencaminhamento do Azure existente nesse espaço de nomes.
- `token`(opcional) - um acesso de reencaminhamento emitido anteriormente token que está incorporada no enviar URI (consulte o exemplo seguinte).
- `id`(opcional) - um identificador de controlo que permite o controlo de ponto a ponto diagnósticos de pedidos.

O `token` valor é opcional e só deve ser utilizado quando não é possível enviar os cabeçalhos de HTTP, juntamente com o handshake de WebSocket, como é o caso com a pilha de W3C WebSocket.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Cria um token de assinatura de acesso partilhado do Azure reencaminhamento (SAS) para o URI de destino especificado, a regra SAS e a regra da chave SAS que é válida para o número de segundos especificado ou para uma hora da instantâneas atual se o argumento de expiração for omitido.

- `uri`(obrigatório) - URI para o qual o token é ao ser emitido. O URI é normalizado para utilizar o esquema HTTP e as informações da cadeia de consulta são eliminadas.
- `ruleName`(obrigatório) - SAS nome da regra para a entidade é representado pelo URI especificado ou para o espaço de nomes representado por parte do anfitrião URI.
- `key`(obrigatório) - chave válida para a regra SAS. 
- `expirationSeconds`(opcional) - o número de segundos até que o token gerado deve expirar. Se não for especificado, a predefinição é 1 hora (3600).

O token emitido confers dos direitos associados a regra SAS especificada para a duração especificada.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Este método é funcionalmente equivalente ao `createRelayToken` método documentado anteriormente, mas devolve o token anexado corretamente para o URI de entrada.

### <a name="class-wsrelayedserver"></a>Classe ws. RelayedServer

O `hycows.RelayedServer` classe é uma alternativa ao `ws.Server` classe que não escutam na rede local, mas os delegados à escuta para o serviço de reencaminhamento do Azure.

Duas classes são principalmente contrato compatível, o que significa que uma aplicação existente utilizando o `ws.Server` classe pode ser alterada facilmente para utilizar a versão retransmitida. As principais diferenças são no construtor e as opções disponíveis.

#### <a name="constructor"></a>Construtor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

O `RelayedServer` construtor suporta um conjunto diferente de argumentos que o `Server`porque não é um serviço de escuta autónomo, ou pode ser incorporado para uma arquitetura de serviço de escuta HTTP existente. Também existem menos opções disponíveis, uma vez que a gestão de WebSocket em grande medida é delegada para o serviço de reencaminhamento.

Argumentos de construtor:

- `server`o URI completamente qualificado para um nome da ligação híbrida no qual a escutar, (obrigatório) - normalmente, construída com o método de programa auxiliar WebSocket.createRelayListenUri().
- `token`Este argumento (obrigatório) - contém uma cadeia de token emitida anteriormente ou uma função de chamada de retorno que pode ser chamada para obter este tipo uma cadeia de token. A opção de chamada de retorno é preferencial, tal como permite-renovação de token.

#### <a name="events"></a>Eventos

`RelayedServer`instâncias emitir três eventos que permitem-lhe processar os pedidos recebidos, estabelecer ligações e, detetar condições de erro. Tem de subscrever o `connect` eventos para processar mensagens. 

##### <a name="headers"></a>Cabeçalhos

```JavaScript 
function(headers)
```

O `headers` evento é desencadeado imediatamente antes de uma ligação recebida é aceite, a ativação da modificação dos cabeçalhos a enviar para o cliente. 

##### <a name="connection"></a>ligação

```JavaScript
function(socket)
```

Emitidos quando uma nova ligação de WebSocket foi aceite. O objeto é do tipo `ws.WebSocket`, igual ao pacote de base.


##### <a name="error"></a>erro

```JavaScript
function(error)
```

Se o servidor subjacente emite um erro, este é reencaminhado aqui.  

#### <a name="helpers"></a>Programas de ajuda

Para simplificar a partir de um servidor retransmitido e subscrever imediatamente para ligações de entrada, o pacote expõe uma função de programa auxiliar simples, que também é utilizada nos exemplos, da seguinte forma:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Este método chama o construtor para criar uma nova instância do RelayedServer e, em seguida, subscreve a chamada de retorno fornecida para o evento 'ligação'.
 
##### <a name="relayedconnect"></a>relayedConnect

Espelhamento simplesmente o `createRelayedServer` auxiliar na função, `relayedConnect` cria uma ligação de cliente e subscreve o evento 'open' no socket resultante.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o reencaminhamento do Azure, visite estas ligações:
* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [APIs de reencaminhamento disponíveis](relay-api-overview.md)
