---
title: "Enlaces de funções de HTTP e webhook do Azure | Microsoft Docs"
description: "Compreenda como utilizar o HTTP e webhook acionadores e enlaces das funções do Azure."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, eventos de processamento, webhooks, dinâmicos de computação, arquitetura sem servidor, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: mahender
ms.openlocfilehash: 3c3247592cbe2bc382d220264b0c646ee566b8a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Enlaces de funções de HTTP e webhook do Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e trabalhar com HTTP acionadores e enlaces de funções do Azure.
Com estas, pode utilizar as funções do Azure para criar APIs sem servidor e responder a webhooks.

As funções do Azure fornece os enlaces do seguintes:
- Um [acionador HTTP](#httptrigger) permite invocar uma função com um pedido de HTTP. Isto pode ser personalizado para responder a [webhooks](#hooktrigger).
- Um [vínculo de saída HTTP](#output) permite-lhe responder ao pedido.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>Acionador HTTP
O acionador HTTP executa a função em resposta a um pedido HTTP. Pode personalizar para responder a um determinado URL ou um conjunto de métodos de HTTP. Um acionador HTTP também pode ser configurado para responder a webhooks. 

Se utilizar o portal de funções, pode também começar a utilizar imediato utilizando um modelo de pré-efetuado. Selecione **nova função** e escolha "API e Webhooks" entre a **cenário** pendente. Selecione um dos modelos e clique em **criar**.

Por predefinição, um acionador HTTP responde ao pedido com um código de estado HTTP 200 OK e um corpo vazio. Para modificar a resposta, configure um [enlace HTTP de saída](#output)

### <a name="configuring-an-http-trigger"></a>Configurar um acionador HTTP
Um acionador HTTP é definido por um objeto JSON no `bindings` matriz de function.json, conforme mostrado no exemplo seguinte:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
O enlace suporta as seguintes propriedades:

|Propriedade  |Descrição  |
|---------|---------|
| **nome** | Necessário - o nome da variável utilizado no código de função para o corpo do pedido ou o pedido. Consulte [trabalhar com um acionador HTTP a partir do código](#httptriggerusage). |
| **tipo** | Necessário - tem de ser definido como `httpTrigger`. |
| **direção** | Necessário - tem de ser definido como `in`. |
| **authLevel** | Determina o chaves, se existirem, precisam de estar presente no pedido para invocar a função. O valor pode ser um dos seguintes valores: <ul><li><code>anonymous</code>&mdash;Não é necessária nenhuma chave de API.</li><li><code>function</code>&mdash;É necessária uma chave de API de específicas. Este é o valor predefinido se não for fornecido nenhum.</li><li><code>admin</code>&mdash;Não é necessária a chave mestra.</li></ul> Para obter mais informações, consulte [trabalhar com as chaves](#keys). |
| **métodos** | Uma matriz dos métodos HTTP para os quais a função responde. Se não for especificado, a função de responde a todos os métodos HTTP. Consulte [personalizar o ponto final de HTTP](#url). |
| **rota** | Define o modelo de rota, controlar ao qual pedido URLs responde a sua função. O valor predefinido se não for fornecido nenhum é `<functionname>`. Para obter mais informações, consulte [personalizar o ponto final de HTTP](#url). |
| **webHookType** | Configura o acionador HTTP para atuar como um recetor de webhook para o fornecedor especificado. Não utilize o _métodos_ propriedade quando utilizar esta definição. O valor pode ser um dos seguintes valores:<ul><li><code>genericJson</code>&mdash;Um ponto final de webhook para fins gerais sem lógica para um fornecedor específico.</li><li><code>github</code>&mdash;A função responde aos webhooks do GitHub. Não utilize o _authLevel_ propriedade quando utilizar este valor.</li><li><code>slack</code>&mdash;A função responde aos Slack webhooks. Não utilize o _authLevel_ propriedade quando utilizar este valor.</li></ul> Para obter mais informações, consulte [a responder aos webhooks](#hooktrigger). |

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Trabalhar com um acionador HTTP a partir do código
Para as funções de c# e F #, podem declarar o tipo do acionador de entrada seja `HttpRequestMessage` ou um tipo .NET personalizado. Se optar por `HttpRequestMessage`, obter acesso total para o objeto pedido. Para um tipo .NET personalizado, funções tentar analisar o corpo do pedido JSON para definir as propriedades do objeto. 

Para funções de Node.js, o tempo de execução de funções fornece o corpo do pedido em vez do objeto pedido. Para obter mais informações, consulte [amostras de Acionador HTTP](#httptriggersample).


<a name="output"></a>
## <a name="http-response-output-binding"></a>Enlace de saída de resposta de HTTP
Utilize a saída HTTP de enlace para responder ao remetente de pedido HTTP. Este enlace necessita de um acionador HTTP e permite-lhe personalizar a resposta associada pedido o acionador. Se o enlace de saída de um HTTP não for fornecido, um acionador HTTP devolve HTTP 200 OK com uma mensagem vazia. 

### <a name="configuring-an-http-output-binding"></a>Configurar um HTTP de saída do enlace
Um HTTP de saída do enlace está definido um objeto JSON no `bindings` matriz de function.json, conforme mostrado no exemplo seguinte:

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
O enlace suporta as seguintes propriedades necessárias:

|Propriedade  |Descrição  |
|---------|---------|
|**nome** | O nome da variável utilizado no código de função para a resposta. Consulte [enlace a partir do código de saída de trabalhar com um HTTP](#outputusage). |
| **tipo** |tem de ser definido como `http`. |
| **direção** | tem de ser definido como `out`. |

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Trabalhar com um HTTP enlace a partir do código de saída
Pode utilizar o parâmetro de saída para responder a chamador http ou o webhook. Também pode utilizar os padrões de resposta de padrão de idioma. Por exemplo respostas, consulte [amostras de Acionador HTTP](#httptriggersample) e [amostras de Acionador de Webhook](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Responder a webhooks
Um acionador HTTP com o _webHookType_ propriedade está configurada para responder a [webhooks](https://en.wikipedia.org/wiki/Webhook). A configuração básica utiliza a definição de "genericJson". Isto restringe pedidos apenas aos utilizando o HTTP POST e com o `application/json` tipo de conteúdo.

O acionador pode além disso permite adaptar a um fornecedor de webhook específicas, tais como [GitHub](https://developer.github.com/webhooks/) ou [Slack](https://api.slack.com/outgoing-webhooks). Quando é especificado um fornecedor, o tempo de execução de funções consegue processar a lógica de validação de fornecedor por si.  

### <a name="configuring-github-as-a-webhook-provider"></a>Configuração de GitHub como um fornecedor de webhook
Para responder a webhooks do GitHub, primeiro criar a sua função com um acionador de HTTP e defina o **webHookType** propriedade `github`. Em seguida, copie o [URL](#url) e [chave de API](#keys) para o **adicionar webhook** página do seu repositório do GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Por exemplo, consulte [criar uma função acionada por um webhook do GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="configuring-slack-as-a-webhook-provider"></a>Configurar Slack como um fornecedor de webhook
O webhook Slack gera um token para si em vez de permitindo-lhe especificar, pelo que tem de configurar uma chave de específicas com o token de Slack. Consulte [trabalhar com as chaves](#keys).

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>Personalizar o ponto final de HTTP
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

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

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

Eis o código de função Node.js a utilizar os mesmos parâmetros de rota.

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

Por predefinição, todas as rotas de função são adicionado o prefixo *api*. Também pode personalizar ou remover o prefixo utilizando o `http.routePrefix` propriedade no seu *host.json* ficheiro. O exemplo a seguir remove o *api* prefixo de rota através de uma cadeia vazia para o prefixo no *host.json* ficheiro.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

Para obter informações detalhadas sobre como atualizar o *host.json* ficheiro para a função, consulte [como atualizar os ficheiros de aplicação de função](functions-reference.md#fileupdate). 

Para obter informações sobre outras propriedades que pode configurar na sua *host.json* de ficheiros, consulte [host.json referência](functions-host-json.md).


<a name="keys"></a>
## <a name="working-with-keys"></a>Trabalhar com as chaves
Acionadores HTTP permitem-lhe utilizar as chaves para segurança adicional. Um acionador HTTP padrão pode utilizá-las como uma chave de API, que requerem a chave de estar presente no pedido. Webhooks pode utilizar as chaves para autorizar os pedidos de diversas formas, consoante o fornecedor suporta.

As chaves são armazenadas como parte da sua aplicação de função no Azure e são encriptadas em pausa. Para ver as suas chaves, crie novos, ou reverta as chaves para novos valores, navegue para uma das suas funções no portal e selecione "Gerir". 

Existem dois tipos de chaves:
- **Das chaves de anfitrião**: estas chaves são partilhadas por todas as funções dentro da aplicação de função. Quando utilizado como uma chave de API, estes cmdlets permitem acesso para qualquer função dentro da aplicação de função.
- **As chaves de função**: estas chaves são aplicadas apenas a funções específicas em que estão definidos. Quando utilizado como uma chave de API, estes apenas permitem o acesso a essa função.

Cada chave com o nome de referência e existe uma chave de predefinição (denominada "predefinido") ao nível da função e o anfitrião. O **chave mestra** é uma chave de anfitrião predefinido com o nome "_master" que está definido para cada aplicação de função. Esta chave não pode ser revogada. Fornece acesso administrativo para o tempo de execução APIs. Utilizar `"authLevel": "admin"` no enlace JSON requer esta chave a ser apresentados no pedido; qualquer outra chave resulta numa falha de autorização.

> [!IMPORTANT]  
> Devido a permissões elevadas concedidas pela chave mestra, não deve partilhar esta chave com terceiros nem distribui-lo em aplicações de cliente nativo. Tenha cuidado quando escolher o nível de autorização de administrador.

### <a name="api-key-authorization"></a>Autorização da chave de API
Por predefinição, um acionador HTTP requer uma chave de API do pedido de HTTP. Por isso, o pedido HTTP normalmente o seguinte aspeto:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

A chave pode ser incluída numa variável de cadeia de consulta com o nome `code`, como acima, ou pode ser incluído num `x-functions-key` cabeçalho de HTTP. O valor da chave pode ser qualquer tecla de função definido para a função ou qualquer chave de anfitrião.

Pode permitir pedidos anónimos, o que não necessitam de chaves. Também pode exigir que a chave mestra utilizada. Alterar o nível de autorização predefinido utilizando o `authLevel` propriedade no enlace JSON. Para obter mais informações, consulte [acionador HTTP](#httptrigger).

### <a name="keys-and-webhooks"></a>As chaves e webhooks
Autorização de Webhook é processada pelo componente de recetor de webhook, parte do acionador HTTP, e o mecanismo de varia consoante o tipo de webhook. Cada mecanismo, o Contudo dependem de uma chave. Por predefinição, é utilizada a tecla de função com o nome "predefinida". Para utilizar uma chave diferente, configure o fornecedor de webhook para enviar o nome da chave com o pedido de uma das seguintes formas:

- **Cadeia de consulta**: O fornecedor transmite o nome da chave no `clientid` consultar o parâmetro de cadeia, tal como `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Cabeçalho de pedido**: O fornecedor transmite o nome da chave no `x-functions-clientid` cabeçalho.

> [!NOTE]
> Teclas de função têm precedência sobre as chaves de anfitrião. Quando duas chaves são definidas com o mesmo nome, a tecla de função é sempre utilizada.


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>Exemplos de Acionador HTTP
Suponha que tem o seguinte acionador HTTP a `bindings` matriz de function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Consulte o exemplo de específicas do idioma que procura um `name` parâmetro ou a cadeia de consulta ou o corpo do pedido HTTP.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.js](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>Exemplo de Acionador HTTP em c# #
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

É também possível vincular a um objeto de .NET em vez de **HttpRequestMessage**. Este objeto é criado a partir do corpo do pedido, analisado como JSON. Da mesma forma, um tipo pode ser transmitido para a resposta HTTP de saída do enlace e devolvido como o corpo da resposta, juntamente com um código de 200 estado.

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

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>Exemplo de Acionador HTTP no F # #
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

Precisa de um `project.json` ficheiro que utiliza o NuGet para referenciar o `FSharp.Interop.Dynamic` e `Dynamitey` assemblagens, semelhante ao seguinte:

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

Esta utiliza NuGet para obter as suas dependências e referencia-las no script.

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Exemplo de Acionador HTTP no Node.JS
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
     
<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Amostras do Webhook
Suponha que tem o acionador de webhook seguintes o `bindings` matriz de function.json:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Consulte o exemplo de específicas do idioma que os registos de comentários de problema do GitHub.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.js](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>Exemplo de Webhook em c# #
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

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>Exemplo de Webhook no F # #
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

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Exemplo de Webhook no Node.JS
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

