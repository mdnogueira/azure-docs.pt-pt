---
title: "Enlaces de funções de HTTP e webhook do Azure"
description: "Compreenda como utilizar o HTTP e webhook acionadores e enlaces das funções do Azure."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, eventos de processamento, webhooks, dinâmicos de computação, arquitetura sem servidor, HTTP, API, REST"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: mahender
ms.openlocfilehash: 6b3da498a613d63515ecb624b87496cf536c0ebf
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Enlaces de funções de HTTP e webhook do Azure

Este artigo explica como trabalhar com os enlaces de HTTP das funções do Azure. Acionadores de HTTP de suporta funções do Azure e os enlaces de saída.

Um acionador HTTP pode ser personalizado para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook). Um acionador de webhook aceita apenas um payload JSON e valida o JSON. Existem versões especiais do acionador webhook que facilitam a processar webhooks de determinados fornecedores, tais como GitHub e Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="trigger"></a>Acionador

O acionador HTTP permite invocar uma função com um pedido de HTTP. Pode utilizar um acionador HTTP para criar APIs sem servidor e responder a webhooks. 

Por predefinição, um acionador HTTP responde ao pedido com um código de estado HTTP 200 OK e um corpo vazio. Para modificar a resposta, configure um [vínculo de saída HTTP](#http-output-binding).

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#trigger---c-example)
* [Script do c#](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Acionador - c# exemplo

O seguinte exemplo mostra um [pré-compilada c# função](functions-dotnet-class-library.md) que procuram um `name` parâmetro ou a cadeia de consulta ou o corpo do pedido HTTP.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>Acionador - exemplo de script do c#

O exemplo seguinte mostra um enlace de Acionador num *function.json* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função procura um `name` parâmetro ou a cadeia de consulta ou o corpo do pedido HTTP.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o script código c# que está vinculado a `HttpRequestMessage`:

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

É possível vincular a um objeto personalizado em vez de `HttpRequestMessage`. Este objeto é criado a partir do corpo do pedido, analisado como JSON. Da mesma forma, um tipo pode ser transmitido para a resposta HTTP de saída do enlace e devolvido como o corpo da resposta, juntamente com um código de 200 estado.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

### <a name="trigger---f-example"></a>Acionador - F # exemplo

O exemplo seguinte mostra um enlace de Acionador num *function.json* ficheiro e uma [F # função](functions-reference-fsharp.md) que utiliza o enlace. A função procura um `name` parâmetro ou a cadeia de consulta ou o corpo do pedido HTTP.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o código F #:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Precisa de um `project.json` ficheiro que utiliza o NuGet para referenciar o `FSharp.Interop.Dynamic` e `Dynamitey` assemblagens, conforme mostrado no exemplo seguinte:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>Acionador - exemplo de JavaScript

O exemplo seguinte mostra um enlace de Acionador num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. A função procura um `name` parâmetro ou a cadeia de consulta ou o corpo do pedido HTTP.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
## <a name="trigger---webhook-example"></a>Acionador - exemplo de webhook

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#webhook---c-example)
* [Script do c#](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhook - c# exemplo

O seguinte exemplo mostra um [pré-compilada c# função](functions-dotnet-class-library.md) que envia uma HTTP 200 em resposta a um pedido JSON genérico.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhook - exemplo de script do c#

O exemplo seguinte mostra um acionador de webhook enlace num *function.json* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função regista comentários de problema do GitHub.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o código de script do c#:

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>Webhook - F # exemplo

O exemplo seguinte mostra um acionador de webhook enlace num *function.json* ficheiro e uma [F # função](functions-reference-fsharp.md) que utiliza o enlace. A função regista comentários de problema do GitHub.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o código F #:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>Webhook - exemplo de JavaScript

O exemplo seguinte mostra um acionador de webhook enlace num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. A função regista comentários de problema do GitHub.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

O [configuração](#trigger---configuration) secção explica estas propriedades.

Eis o código JavaScript:

```javascript
```

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Acionador - atributos

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize o [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) atributo, definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http).

Pode definir a autorização nível e os métodos HTTP nos parâmetros do construtor de atributo, e existem propriedades para o modelo de tipo e a rota do webhook. Para obter mais informações sobre estas definições, consulte [acionador - configuração](#trigger---configuration). Eis um `HttpTrigger` atributo na assinatura do método:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Para obter um exemplo completado, consulte [acionador - pré-compilada c# exemplo](#trigger---c-example).

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `HttpTrigger` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo** | n/d| Necessário - tem de ser definido como `httpTrigger`. |
| **direção** | n/d| Necessário - tem de ser definido como `in`. |
| **nome** | n/d| Necessário - o nome da variável utilizado no código de função para o corpo do pedido ou o pedido. |
| **authLevel** |  **AuthLevel** |Determina o chaves, se existirem, precisam de estar presente no pedido para invocar a função. O nível de autorização pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;Não é necessária nenhuma chave de API.</li><li><code>function</code>&mdash;É necessária uma chave de API de específicas. Este é o valor predefinido se não for fornecido nenhum.</li><li><code>admin</code>&mdash;Não é necessária a chave mestra.</li></ul> Para obter mais informações, consulte a secção [chaves de autorização](#authorization-keys). |
| **métodos** |**Métodos** | Uma matriz dos métodos HTTP para os quais a função responde. Se não for especificado, a função de responde a todos os métodos HTTP. Consulte [personalizar o ponto final de http](#trigger---customize-the-http-endpoint). |
| **rota** | **Rota** | Define o modelo de rota, controlar ao qual pedido URLs responde a sua função. O valor predefinido se não for fornecido nenhum é `<functionname>`. Para obter mais informações, consulte [personalizar o ponto final de http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Configura o acionador HTTP para atuar como um [webhook](https://en.wikipedia.org/wiki/Webhook) recetor para o fornecedor especificado. Não defina a `methods` propriedade se definir esta propriedade. O tipo de webhook pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto final de webhook para fins gerais sem lógica para um fornecedor específico. Esta definição limita os pedidos para apenas os utilizando o HTTP POST e com o `application/json` tipo de conteúdo.</li><li><code>github</code>&mdash;A função responde a [GitHub webhooks](https://developer.github.com/webhooks/). Não utilize o _authLevel_ propriedade com o GitHub webhooks. Para obter mais informações, consulte a secção de webhooks GitHub neste artigo.</li><li><code>slack</code>&mdash;A função responde a [Slack webhooks](https://api.slack.com/outgoing-webhooks). Não utilize o _authLevel_ propriedade com Slack webhooks. Para obter mais informações, consulte a secção de Slack webhooks neste artigo.</li></ul>|

## <a name="trigger---usage"></a>Acionador - utilização

Para as funções de c# e F #, podem declarar o tipo do acionador de entrada seja `HttpRequestMessage` ou um tipo personalizado. Se optar por `HttpRequestMessage`, obter acesso total para o objeto pedido. Para um tipo personalizado, funções tentar analisar o corpo do pedido JSON para definir as propriedades do objeto. 

Para funções de JavaScript, o tempo de execução de funções fornece o corpo do pedido em vez do objeto pedido. Para obter mais informações, consulte o [exemplo de Acionador de JavaScript](#trigger---javascript-example).

### <a name="github-webhooks"></a>GitHub webhooks

Para responder a webhooks do GitHub, primeiro criar a sua função com um acionador de HTTP e defina o **webHookType** propriedade `github`. Em seguida, copie o URL e API a chave para o **adicionar webhook** página do seu repositório do GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Por exemplo, consulte [criar uma função acionada por um webhook do GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Slack webhooks

O webhook Slack gera um token para si em vez de permitindo-lhe especificar, pelo que tem de configurar uma chave de específicas com o token de Slack. Consulte [chaves de autorização](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Personalizar o ponto final de HTTP

Por predefinição quando cria uma função de um acionador HTTP ou o WebHook, a função é endereçável com uma rota com o formato:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Pode personalizar esta rota utilizando a opção `route` propriedade no acionador HTTP de entrada de enlace. Por exemplo, o seguinte *function.json* ficheiro define um `route` propriedade para um acionador HTTP:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Utilizando esta configuração, a função é agora endereçável com a rota seguinte em vez da rota original.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Isto permite que o código de função suportar dois parâmetros, o endereço de _categoria_ e _id_. Pode utilizar qualquer [Web API rota restrição](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) com os parâmetros. O seguinte código de função do c# utilizam ambos os parâmetros.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Eis o código de função de Node.js que utiliza os mesmos parâmetros de rota.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

Por predefinição, todas as rotas de função são adicionado o prefixo *api*. Também pode personalizar ou remover o prefixo utilizando o `http.routePrefix` propriedade no seu [host.json](functions-host-json.md) ficheiro. O exemplo a seguir remove o *api* prefixo de rota através de uma cadeia vazia para o prefixo no *host.json* ficheiro.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Chaves de autorização

Acionadores HTTP permitem-lhe utilizar as chaves para segurança adicional. Um acionador HTTP padrão pode utilizá-las como uma chave de API, que requerem a chave de estar presente no pedido. Webhooks pode utilizar as chaves para autorizar os pedidos de diversas formas, consoante o fornecedor suporta.

As chaves são armazenadas como parte da sua aplicação de função no Azure e são encriptadas em pausa. Para ver as suas chaves, criar novos, ou reverta as chaves para novos valores, navegue para uma das suas funções no portal e selecione "Gerir". 

Existem dois tipos de chaves:

- **Das chaves de anfitrião**: estas chaves são partilhadas por todas as funções dentro da aplicação de função. Quando utilizado como uma chave de API, estes cmdlets permitem acesso para qualquer função dentro da aplicação de função.
- **As chaves de função**: estas chaves são aplicadas apenas a funções específicas em que estão definidos. Quando utilizado como uma chave de API, estes apenas permitem o acesso a essa função.

Cada chave com o nome de referência e existe uma chave de predefinição (denominada "predefinido") ao nível da função e o anfitrião. Teclas de função têm precedência sobre as chaves de anfitrião. Quando duas chaves são definidas com o mesmo nome, a tecla de função é sempre utilizada.

O **chave mestra** é uma chave de anfitrião predefinido com o nome "_master" que está definido para cada aplicação de função. Esta chave não pode ser revogada. Fornece acesso administrativo para o tempo de execução APIs. Utilizar `"authLevel": "admin"` no enlace JSON requer esta chave a ser apresentados no pedido; qualquer outra chave resulta numa falha de autorização.

> [!IMPORTANT]  
> Devido a permissões elevadas concedidas pela chave mestra, não deve partilhar esta chave com terceiros nem distribui-lo em aplicações de cliente nativo. Tenha cuidado quando escolher o nível de autorização de administrador.

### <a name="api-key-authorization"></a>Autorização da chave de API

Por predefinição, um acionador HTTP requer uma chave de API do pedido de HTTP. Por isso, o pedido HTTP normalmente o seguinte aspeto:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

A chave pode ser incluída numa variável de cadeia de consulta com o nome `code`, como acima, ou pode ser incluído num `x-functions-key` cabeçalho de HTTP. O valor da chave pode ser qualquer tecla de função definido para a função ou qualquer chave de anfitrião.

Pode permitir pedidos anónimos, o que não necessitam de chaves. Também pode exigir que a chave mestra utilizada. Alterar o nível de autorização predefinido utilizando o `authLevel` propriedade no enlace JSON. Para obter mais informações, consulte [acionador - configuração](#trigger---configuration).

### <a name="keys-and-webhooks"></a>As chaves e webhooks

Autorização de Webhook é processada pelo componente de recetor de webhook, parte do acionador HTTP, e o mecanismo de varia consoante o tipo de webhook. Cada mecanismo, o Contudo dependem de uma chave. Por predefinição, é utilizada a tecla de função com o nome "predefinida". Para utilizar uma chave diferente, configure o fornecedor de webhook para enviar o nome da chave com o pedido de uma das seguintes formas:

- **Cadeia de consulta**: O fornecedor transmite o nome da chave no `clientid` consultar o parâmetro de cadeia, tal como `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Cabeçalho de pedido**: O fornecedor transmite o nome da chave no `x-functions-clientid` cabeçalho.

## <a name="trigger---hostjson-properties"></a>Acionador - host.json propriedades

O [host.json](functions-host-json.md) ficheiro contém definições que controlam o comportamento de Acionador HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Saída

Utilize a saída HTTP de enlace para responder ao remetente de pedido HTTP. Este enlace necessita de um acionador HTTP e permite-lhe personalizar a resposta associada pedido o acionador. Se o enlace de saída de um HTTP não for fornecido, um acionador HTTP devolve HTTP 200 OK com uma mensagem vazia. 

## <a name="output---configuration"></a>De saída - configuração

Para pré-compilada c#, existem sem propriedades de configuração de enlace de saída específicas. Para enviar uma resposta HTTP, se a função de tipo de retorno `HttpResponseMessage` ou `Task<HttpResponseMessage>`.

Para outros idiomas, um HTTP de saída do enlace está definida como um objeto JSON no `bindings` matriz de function.json, conforme mostrado no exemplo seguinte:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiro.

|Propriedade  |Descrição  |
|---------|---------|
| **tipo** |tem de ser definido como `http`. |
| **direção** | tem de ser definido como `out`. |
|**nome** | O nome da variável utilizado no código de função para a resposta. |

## <a name="output---usage"></a>Saída - utilização

Pode utilizar o parâmetro de saída para responder a chamador HTTP ou o webhook. Também pode utilizar os padrões de resposta de padrão de idioma. Por exemplo respostas, consulte o [exemplo acionador](#trigger---example) e [webhook exemplo](#trigger---webhook-example).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
