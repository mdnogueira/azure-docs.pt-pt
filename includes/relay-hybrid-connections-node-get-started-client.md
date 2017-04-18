### <a name="create-a-nodejs-application"></a>Criar uma aplicação Node.js
* Criar um novo ficheiro JavaScript denominado `sender.js`.

### <a name="add-the-relay-npm-package"></a>Adicionar o pacote NPM de Reencaminhamento
* Execute `npm install hyco-ws` a partir de uma linha de comandos do Nó na sua pasta do projeto.

### <a name="write-some-code-to-send-messages"></a>Escrever alguns códigos para enviar mensagens
1. Adicione o seguinte `constants` à parte superior do ficheiro `sender.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Adicione o Reencaminhamento seguinte `constants` ao `sender.js`, para obter os detalhes da ligação da Ligação Híbrida. Substitua os marcadores de posição entre parênteses retos pelos valores adequados que foram obtidos ao criar a Ligação Híbrida.
   
   1. `const ns` - O espaço de nomes de Reencaminhamento (utilize o FQDN - por exemplo, `{namespace}.servicebus.windows.net`)
   2. `const path` - O nome da Ligação Híbrida
   3. `const keyrule` - O nome da chave SAS
   4. `const key` - O valor da chave SAS
3. Adicione o seguinte código ao ficheiro `sender.js`:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    O ficheiro listener.js deve ter o seguinte aspeto:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

