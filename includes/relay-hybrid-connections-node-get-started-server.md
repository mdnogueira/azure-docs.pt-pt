### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
* Criar um novo ficheiro JavaScript denominado `listener.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de Reencaminhamento
* Execute `npm install hyco-ws` a partir de uma linha de comandos do Nó na sua pasta do projeto.

### <a name="write-some-code-to-receive-messages"></a>Escrever alguns códigos para receber mensagens
1. Adicione o seguinte `constant` à parte superior do ficheiro `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Adicione o Reencaminhamento seguinte `constants` ao `listener.js`, para obter os detalhes da ligação da Ligação Híbrida. Substitua os marcadores de posição entre parênteses retos pelos valores adequados que foram obtidos ao criar a Ligação Híbrida.
   
   1. `const ns` - O espaço de nomes do Reencaminhamento
   2. `const path` - O nome da Ligação Híbrida
   3. `const keyrule` - O nome da chave SAS
   4. `const key` - O valor da chave SAS
3. Adicione o seguinte código ao ficheiro `listener.js`:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    O ficheiro listener.js deve ter o seguinte aspeto:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```



<!--HONumber=Nov16_HO2-->


