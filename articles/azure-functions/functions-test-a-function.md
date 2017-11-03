---
title: "Testar as funções do Azure | Microsoft Docs"
description: "Teste as suas funções do Azure utilizando o Postman, cURL e Node.js."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, teste"
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41796a8cdde0756e5157ba276463a56b07679d04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Estratégias para testar o seu código das funções do Azure

Este tópico demonstra as várias formas para testar as funções, incluindo a utilizar as seguintes abordagens gerais:

+ Ferramentas baseadas em HTTP, como o cURL, Postman e até mesmo um web browser para acionadores baseada na web
+ Explorador de armazenamento do Azure, para testar acionadores baseado no Storage do Azure
+ Separador de teste no portal das funções do Azure
+ Função de acionada por temporizador
+ Testar a aplicação ou framework

Todos estes métodos de teste de utilizar uma função de Acionador HTTP que aceita entrada através de um parâmetro de cadeia de consulta ou o corpo do pedido. Esta função, criar na primeira secção.

## <a name="create-a-function-for-testing"></a>Criar uma função para fins de teste
Para a maior parte deste tutorial, utilizamos uma versão ligeiramente modificada do modelo de função de HttpTrigger JavaScript que está disponível quando criar uma função. Se precisar de obter ajuda na criação de uma função, reveja este [tutorial](functions-create-first-azure-function.md). Escolha o **HttpTrigger - JavaScript** modelo quando criar a função de teste no [portal do Azure].

O modelo de função predefinido é basicamente uma função de "Olá mundo" echoes volta o nome do parâmetro pedido consulta ou de corpo de cadeia, `name=<your name>`.  Iremos atualizá o código de também poder fornecer o nome e um endereço como conteúdo JSON no corpo do pedido. Em seguida, a função echoes estes anterior para o cliente, quando disponível.   

Atualize a função com o código seguinte, que iremos utilizar para fins de teste:

```javascript
module.exports = function (context, req) {
    context.log("HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
    context.log("Request Headers = " + JSON.stringify(req.headers));
    var res;

    if (req.query.name || (req.body && req.body.name)) {
        if (typeof req.query.name != "undefined") {
            context.log("Name was provided as a query string param...");
            res = ProcessNewUserInformation(context, req.query.name);
        }
        else {
            context.log("Processing user info from request body...");
            res = ProcessNewUserInformation(context, req.body.name, req.body.address);
        }
    }
    else {
        res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done(null, res);
};
function ProcessNewUserInformation(context, name, address) {
    context.log("Processing user information...");
    context.log("name = " + name);
    var echoString = "Hello " + name;
    var res;

    if (typeof address != "undefined") {
        echoString += "\n" + "The address you provided is " + address;
        context.log("address = " + address);
    }
    res = {
        // status: 200, /* Defaults to 200 */
        body: echoString
    };
    return res;
}
```

## <a name="test-a-function-with-tools"></a>Uma função com ferramentas de teste
Fora do portal do Azure, existem várias ferramentas que pode utilizar para acionar as suas funções para fins de teste. Estes incluem HTTP testar mesmo um simples browser, ferramentas de acesso de armazenamento do Azure e ferramentas (baseadas na IU e comando linha).

### <a name="test-with-a-browser"></a>Testar com um browser
O browser web é uma forma simples de funções de Acionador através de HTTP. Pode utilizar um browser para os pedidos GET que não necessitam de um payload de corpo e que utilize apenas a consultar os parâmetros string.

Para testar a função definido anteriormente, copie o **Url de função** do portal. Tem o seguinte formato:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Acrescentar o `name` parâmetro para a cadeia de consulta. Utilizar um nome real para o `<Enter a name here>` marcador de posição.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Cole o URL do seu browser e deve obter uma resposta semelhante ao seguinte.

![Separador do browser de captura de ecrã do Chrome com a resposta do teste](./media/functions-test-a-function/browser-test.png)

Neste exemplo é o browser Chrome, que encapsula num wrapper a cadeia devolvida em XML. Outros browsers apresentam apenas o valor de cadeia.

No portal do **registos** janela, semelhante à seguinte tiver sessão iniciada ao executar a função de saída:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Testar com Postman
A ferramenta recomendada para testar a maior parte das suas funções é Postman, que se integra com o browser Chrome. Para instalar o Postman, consulte [obter Postman](https://www.getpostman.com/). Postman fornece controlo sobre muitos mais atributos de um pedido HTTP.

> [!TIP]
> Utilize a ferramenta que é mais confortável com a testar de HTTP. Seguem-se algumas alternativas para Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Paw](https://luckymarmot.com/paw)  
>
>

Para testar a função com um corpo do pedido em Postman:

1. Iniciar o Postman do **aplicações** botão no canto superior esquerdo da janela do browser Chrome.
2. Copiar o **Url de função**e cole-o Postman. Inclui o parâmetro de cadeia de consulta do código de acesso.
3. Alterar o método HTTP para **POST**.
4. Clique em **corpo** > **em bruto**, e adicione um corpo do pedido JSON semelhante ao seguinte:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Clique em **enviar**.

A imagem seguinte mostra o exemplo de função de eco simples de teste neste tutorial.

![Interface de utilizador de captura de ecrã do Postman](./media/functions-test-a-function/postman-test.png)

No portal do **registos** janela, semelhante à seguinte tiver sessão iniciada ao executar a função de saída:

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>Testar com cURL na linha de comandos
Muitas vezes, quando estiver a testar software, não é necessário consultar quaisquer mais do que a linha de comandos para o ajudar a depurar a aplicação. Isto é não diferente com as funções de teste. Tenha em atenção que o cURL está disponível por predefinição em sistemas baseados em Linux. No Windows, tem primeiro de transferir e instalar o [cURL ferramenta](https://curl.haxx.se/).

Para testar a função que definido anteriormente, copie o **URL de função** do portal. Tem o seguinte formato:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Este é o URL para acionar a sua função. Testar isto utilizando o comando cURL na linha de comandos para efetuar uma ação obter (`-G` ou `--get`) pedido contra a função:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Este exemplo em concreto requer um parâmetro de cadeia de consulta, que pode ser transmitido como dados (`-d`) no comando cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Execute o comando e, consulte o seguinte resultado da função na linha de comandos:

![Saída de captura de ecrã da linha de comandos](./media/functions-test-a-function/curl-test.png)

No portal do **registos** janela, semelhante à seguinte tiver sessão iniciada ao executar a função de saída:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Testar um acionador de blob utilizando o Explorador de armazenamento
Pode testar uma função de Acionador de blob utilizando [Explorador de armazenamento do Azure](http://storageexplorer.com/).

1. No [portal do Azure] para a sua aplicação de função, criar uma função de Acionador de blob c#, F # ou JavaScript. Defina o caminho para monitorizar com o nome do contentor de blob. Por exemplo:

        files
2. Clique em de  **+**  botão para selecionar ou criar a conta de armazenamento que pretende utilizar. Em seguida, clique em **Criar**.
3. Crie um ficheiro de texto com o seguinte texto e guarde-o:

        A text file for blob trigger function testing.
4. Executar [Explorador de armazenamento do Azure](http://storageexplorer.com/)e ligar ao contentor do blob na conta de armazenamento que está a ser monitorizado.
5. Clique em **carregar** para carregar o ficheiro de texto.

    ![Captura de ecrã do Explorador de armazenamento](./media/functions-test-a-function/azure-storage-explorer-test.png)

O código de função de Acionador de blob predefinido reporta o processamento do blob nos registos:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Testar uma função de funções
As funções de temporizador acionado e as funções do Azure portal foi concebido para permitem-lhe testar HTTP. Também pode criar funções para acionar a outras funções que está a testar.

### <a name="test-with-the-functions-portal-run-button"></a>Testar com o botão de execução de portal de funções
O portal fornece um **executar** botão que pode utilizar para efetuar algumas limitada de teste. Pode fornecer um corpo do pedido, com o botão, mas não é possível fornecer parâmetros de cadeia de consulta ou atualizar os cabeçalhos do pedido.

Testar a função de Acionador HTTP foi criada anteriormente ao adicionar uma cadeia JSON semelhante à seguinte no **corpo do pedido** campo. Em seguida, clique o **executar** botão.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

No portal do **registos** janela, semelhante à seguinte tiver sessão iniciada ao executar a função de saída:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Testar com um acionador de temporizador
Algumas funções não podem ser testadas adequadamente com as ferramentas de mencionado anteriormente. Por exemplo, considere uma função de Acionador de fila é executado quando uma mensagem foi removida para [armazenamento de filas do Azure](../storage/queues/storage-dotnet-how-to-use-queues.md). Pode sempre escrever código para remover uma mensagem na sua fila e um exemplo desta situação num projeto de consola é fornecido neste artigo. No entanto, há outra abordagem que pode utilizar para testes funções diretamente.  

Pode utilizar um acionador de temporizador configurado com uma fila de saída do enlace. Esse código de Acionador de temporizador, em seguida, pode escrever as mensagens de teste para a fila. Esta secção explica-se um exemplo.

Para mais informações aprofundadas sobre a utilização de enlaces com as funções do Azure, consulte o [referência para programadores de funções do Azure](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Criar um acionador de fila para fins de teste
Para demonstrar esta abordagem, iremos criar primeiro uma função de Acionador de fila que queremos testá para uma fila com o nome `queue-newusers`. Esta função processa as informações de nome e endereço largadas no armazenamento de filas para um novo utilizador.

> [!NOTE]
> Se utilizar um nome de fila diferente, certifique-se o nome que utiliza está em conformidade com a [nomenclatura de filas e metadados](https://msdn.microsoft.com/library/dd179349.aspx) regras. Caso contrário, receberá um erro.
>
>

1. No [portal do Azure] para a sua aplicação de função, clique em **nova função** > **QueueTrigger - c#**.
2. Introduza o nome da fila para ser monitorizada pela função fila:

        queue-newusers
3. Clique em de  **+**  botão para selecionar ou criar a conta de armazenamento que pretende utilizar. Em seguida, clique em **Criar**.
4. Deixe esta janela do portal browser aberta, pelo que pode monitorizar as entradas de registo para o código de modelo de função de fila de predefinição.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Criar um acionador de temporizador para remover uma mensagem na fila
1. Abra o [portal do Azure] numa nova janela do browser e navegue para a sua aplicação de função.
2. Clique em **nova função** > **TimerTrigger - c#**. Introduza uma expressão de cron para definir a frequência o código de temporizador testa a sua função de fila. Em seguida, clique em **Criar**. Se pretender que o teste para ser executada a cada 30 segundos, pode utilizar o seguinte [expressão CRON](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Clique em de **integrar** separador para o novo acionador de temporizador.
4. Em **saída**, clique em **+ nova saída**. Em seguida, clique em **fila** e **selecione**.
5. Tenha em atenção o nome que utiliza para o **objecto de mensagem de fila**. Utilize esta com o código de função do temporizador.

        myQueue
6. Introduza o nome da fila onde a mensagem é enviada:

        queue-newusers
7. Clique em de  **+**  botão para selecionar a conta de armazenamento que utilizou anteriormente com o acionador de fila. Em seguida, clique em **Guardar**.
8. Clique em de **desenvolver** separador para o acionador de temporizador.
9. Pode utilizar o seguinte código para a função c# temporizador, desde que tenha utilizado o mesmo nome de objeto de mensagem fila apresentado anteriormente. Em seguida, clique em **Guardar**.

    ```cs
    using System;

    public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
    {
        String newUser =
        "{\"name\":\"User testing from C# timer function\",\"address\":\"XYZ\"}";

        log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
        log.Verbose($"{newUser}");   

        myQueue = newUser;
    }
    ```

Neste momento, a função de temporizador do c# executa cada 30 segundos, se utilizou a expressão de cron de exemplo. Os registos para a função de temporizador reportam cada execução:

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

Na janela do browser para a função de fila, pode ver cada mensagem a ser processada:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>Uma função com o código de teste
Terá de criar uma aplicação externa ou framework para testar as suas funções.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Uma função de Acionador HTTP com o código de teste: Node.js
Pode utilizar uma aplicação Node.js para executar um pedido de HTTP para testar a sua função.
Certifique-se definir:

* O `host` nas opções do pedido para o anfitrião de aplicação de função.
* O nome de função no `path`.
* O código de acesso (`<your code>`) no `path`.

Exemplo de código:

```javascript
var http = require("http");

var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";

var nameBodyJSON = {
    name : "Wes testing with Node.JS code",
    address : "Dallas, T.X. 75201"
};

var bodyString = JSON.stringify(nameBodyJSON);

var options = {
  host: "functions841def78.azurewebsites.net",
  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
  method: "POST",
  headers : {
      "Content-Type":"application/json",
      "Content-Length": Buffer.byteLength(bodyString)
    }    
};

callback = function(response) {
  var str = ""
  response.on("data", function (chunk) {
    str += chunk;
  });

  response.on("end", function () {
    console.log(str);
  });
}

var req = http.request(options, callback);
console.log("*** Sending name and address in body ***");
console.log(bodyString);
req.end(bodyString);
```


Saída:

    C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
    *** Sending name and address in body ***
    {"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
    Hello Wes testing with Node.JS code
    The address you provided is Dallas, T.X. 75201

No portal do **registos** janela, semelhante à seguinte tiver sessão iniciada ao executar a função de saída:

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>Uma função de Acionador de fila com o código de teste: c# #
Foi mencionado anteriormente que pode testar um acionador de fila utilizando o código para remover uma mensagem na sua fila. O código de exemplo seguinte é baseado no código c# apresentado no [introdução ao armazenamento de filas do Azure](../storage/queues/storage-dotnet-how-to-use-queues.md) tutorial. Código para outros idiomas também está disponível a partir dessa ligação.

Para testar este código numa aplicação de consola, tem de:

* [Configurar a cadeia de ligação de armazenamento no ficheiro App. config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Transmita um `name` e `address` como parâmetros para a aplicação. Por exemplo, `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Este código aceita o nome e endereço para um novo utilizador como argumentos da linha de comandos durante o tempo de execução.)

Código de exemplo c#:

```cs
static void Main(string[] args)
{
    string name = null;
    string address = null;
    string queueName = "queue-newusers";
    string JSON = null;

    if (args.Length > 0)
    {
        name = args[0];
    }
    if (args.Length > 1)
    {
        address = args[1];
    }

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference(queueName);

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    if (name != null)
    {
        if (address != null)
            JSON = String.Format("{{\"name\":\"{0}\",\"address\":\"{1}\"}}", name, address);
        else
            JSON = String.Format("{{\"name\":\"{0}\"}}", name);
    }

    Console.WriteLine("Adding message to " + queueName + "...");
    Console.WriteLine(JSON);

    CloudQueueMessage message = new CloudQueueMessage(JSON);
    queue.AddMessage(message);
}
```

Na janela do browser para a função de fila, pode ver cada mensagem a ser processada:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[portal do Azure]: https://portal.azure.com
