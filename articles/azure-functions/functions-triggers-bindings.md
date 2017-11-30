---
title: "Trabalhar com acionadores e enlaces de funções do Azure"
description: "Saiba como utilizar acionadores e enlaces das funções do Azure para ligar a execução do código para serviços baseados na nuvem e eventos online."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: e3413c9e1055ca9198dae4a467bcf47372ad4ecb
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceitos de enlaces e acionadores de funções do Azure
As funções do Azure permite-lhe escrever código em resposta a eventos no Azure e outros serviços, através de *acionadores* e *enlaces*. Este artigo é uma descrição geral conceptual dos acionadores e enlaces para todos os suportados linguagens de programação. As funcionalidades que são comuns a todos os enlaces são descritas aqui.

## <a name="overview"></a>Descrição geral

Acionadores e enlaces são uma forma declarativa de definir a forma como uma função é invocada e o que funciona com dados. A *acionador* define a forma como uma função é invocada. Uma função tem de ter exatamente um acionador. Acionadores associou dados, o que é normalmente o payload que acionou a função.

Entrada e saída *enlaces* proporcionam uma forma declarativa para ligar a dados a partir de dentro do seu código. Semelhante ao acionadores, especificar as cadeias de ligação e outras propriedades na configuração da função. Enlaces são opcionais e uma função pode ter vários de entrada e saída enlaces. 

Utilizar acionadores e enlaces, pode escrever código que é mais genérico e não não que codifique os detalhes dos serviços com que interage. Dados provenientes de valores de entrada de serviços simplesmente tornar-se para o seu código de função. A saída de dados para outro serviço (como criar uma nova linha no Table Storage do Azure), utilize o valor devolvido do método. Ou, se precisar de vários valores de saída, utilize um objeto de programa auxiliar. Acionadores e enlaces têm um **nome** propriedade, o que é um identificador que utiliza no seu código para aceder o enlace.

Pode configurar acionadores e enlaces no **integrar** separador no portal das funções do Azure. Nos bastidores, a IU modifica um ficheiro chamado *function.json* ficheiro no diretório de função. Pode editar este ficheiro pela alteração para o **editor avançada**.

## <a name="supported-bindings"></a>Enlaces suportados

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre os quais os enlaces estão na pré-visualização ou estão aprovados para utilização em produção, consulte [idiomas suportados](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Exemplo: acionador de filas e tabela de saída do enlace

Suponha que pretende escrever uma nova linha no Table Storage do Azure, sempre que uma nova mensagem é apresentada no armazenamento de filas do Azure. Este cenário pode ser implementado através de uma fila do Azure acionador e um Table Storage do Azure de saída do enlace. 

Um acionador de armazenamento de filas do Azure requer as seguintes informações no **integrar** separador:

* O nome da definição de aplicação que contém a cadeia de ligação de conta do Storage do Azure para o armazenamento de filas do Azure
* O nome da fila
* O identificador no código para ler o conteúdo da mensagem de fila, tais como `order`.

Escrever Table Storage do Azure, utilize um enlace de saída com os seguintes detalhes:

* O nome da definição de aplicação que contém a cadeia de ligação de conta do Storage do Azure para o Table Storage do Azure
* O nome da tabela
* O identificador no seu código para criar itens de saída, ou o valor de retorno da função.

Enlaces utilizam cadeias de ligação de valores armazenados nas definições da aplicação para impor as melhores práticas que *function.json* não contêm segredos de serviço e em vez disso, contém apenas os nomes das definições da aplicação.

Em seguida, utilize os identificadores que forneceu para integrar com o Storage do Azure no seu código.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

Eis o *function.json* que corresponde ao código anterior. Tenha em atenção que a mesma configuração pode ser utilizada, independentemente do idioma da implementação da função.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```
Para ver e editar o conteúdo do *function.json* no portal do Azure, clique em de **editor avançada** opção o **integrar** separador da sua função.

Para obter mais exemplos de código e detalhes sobre a integração com o Storage do Azure, consulte [acionadores de funções do Azure e vínculos para armazenamento do Azure](functions-bindings-storage.md).

## <a name="binding-direction"></a>Direção de enlace

Todos os acionadores e enlaces de tem um `direction` propriedade no *function.json* ficheiro:

- Para acionadores, a direção é sempre`in`
- Utilizam enlaces de entrada e de saída `in` e`out`
- Alguns enlaces suportam uma direção especial `inout`. Se utilizar `inout`, apenas o **editor avançada** está disponível no **integrar** separador.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Utilizar o tipo de retorno da função para devolver um resultado único

O exemplo anterior mostra como utilizar o valor de retorno da função para fornecer a saída para um enlace, o que é conseguido utilizando o parâmetro de nome especial `$return`. (Esta é apenas suportada em idiomas de que tem um valor de retorno, como c#, JavaScript e F #.) Se uma função tem múltiplos enlaces de resultados, utilize `$return` para apenas um dos enlaces de saída. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Os exemplos abaixo mostram como devolvem tipos são utilizados com enlaces de saída em c#, JavaScript e F #.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>Propriedade de tipo de dados de enlace

No .NET, utilize os tipos para definir o tipo de dados para dados de entrada. Por exemplo, utilize `string` ao vincular ao texto de um acionador de fila, uma matriz de bytes para leitura como binário e um tipo personalizado para anular a serialização para um objeto de POCO.

Para os idiomas que são escritos dinamicamente, como JavaScript, utilize o `dataType` propriedade na definição do enlace. Por exemplo, para ler o conteúdo de um pedido HTTP no formato binário, utilize o tipo de `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Outras opções para `dataType` são `stream` e `string`.

## <a name="resolving-app-settings"></a>Resolver as definições de aplicação
Como melhor prática, os segredos e cadeias de ligação devem ser geridas utilizando as definições de aplicação, em vez de ficheiros de configuração. Isto limita o acesso a estes segredos e torna seguro armazenar *function.json* num repositório de controlo de origem público.

As definições de aplicações também são úteis sempre que pretender alterar a configuração com base no ambiente. Por exemplo, num ambiente de teste, pode querer monitorizar um contentor de armazenamento de fila ou blob diferente.

São resolvidas as definições de aplicação sempre que um valor está entre símbolos de percentagem inicia, tais como `%MyAppSetting%`. Tenha em atenção que o `connection` propriedade de acionadores e enlaces é num caso especial e resolve automaticamente os valores das definições de aplicação. 

O exemplo seguinte é um acionador de armazenamento de filas do Azure que utiliza uma definição de aplicação `%input-queue-name%` para definir a fila para acionar.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

## <a name="trigger-metadata-properties"></a>Propriedades de metadados do acionador

Para além do payload de dados fornecido por um acionador (por exemplo, a mensagem da fila que acionou uma função), acionadores muitos forneça valores de metadados adicionais. Estes valores podem ser utilizados como parâmetros de entrada em c# e F # ou propriedades no `context.bindings` objeto em JavaScript. 

Por exemplo, um acionador de fila de armazenamento do Azure suporta as seguintes propriedades:

* QueueTrigger - acionar o conteúdo da mensagem se uma cadeia válida
* DequeueCount
* expirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Detalhes das propriedades de metadados para cada acionador são descritos no tópico de referência correspondente. Também está disponível na documentação sobre o **integrar** separador do portal, no **documentação** secção abaixo da área de configuração do enlace.  

Por exemplo, uma vez que os acionadores de blob têm alguns atrasos, pode utilizar um acionador de fila para executar a função (consulte [acionador de armazenamento de BLOBs](functions-bindings-storage-blob.md#trigger)). A mensagem da fila iria conter o nome de ficheiro do blob para acionar. Utilizar o `queueTrigger` propriedade de metadados, pode especificar este comportamento todos na sua configuração, em vez de código.

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Propriedades de metadados de um acionador também podem ser utilizadas num *expressão de enlace* para outro enlace, conforme descrito na secção seguinte.

## <a name="binding-expressions-and-patterns"></a>Expressões de enlace e padrões

Uma das funcionalidades mais poderosas de acionadores e enlaces é *expressões de enlace*. No seu enlace, pode definir as expressões de padrão que, em seguida, podem ser utilizadas noutros enlaces ou o seu código. Também podem ser utilizado o acionador metadados no enlace expressões, como mostra o exemplo na secção anterior.

Por exemplo, suponha que pretende redimensionar imagens no contentor de armazenamento de blob específico, semelhante a **Resizer de imagem** modelo no **nova função** página. Aceda a **nova função** -> idioma **c#** -> cenário **amostras** -> **ImageResizer CSharp**. 

Eis o *function.json* definição:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Tenha em atenção que o `filename` parâmetro é utilizado no tanto a definição do acionador de blob, bem como o blob de saída do enlace. Este parâmetro também pode ser utilizado no código da função.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->


### <a name="random-guids"></a>GUIDs aleatórios
As funções do Azure fornece uma sintaxe de conveniência para gerar GUIDs dos enlaces, através de `{rand-guid}` expressão de enlace. O exemplo seguinte utiliza-o para gerar um nome exclusivo de blob: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Hora atual

Pode utilizar a expressão de enlace `DateTime`, que é resolvido para `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>Vincular a propriedades personalizadas de entrada numa expressão de enlace

Expressões de enlace também podem referenciar propriedades que são definidas no payload do acionador em si. Por exemplo, poderá pretender dinamicamente vincular a um ficheiro de armazenamento de BLOBs a partir de um nome de ficheiro fornecido num webhook.

Por exemplo, o seguinte *function.json* utiliza uma propriedade denominada `BlobName` do payload do acionador:

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Para tal em c# e F #, tem de definir um POCO que define os campos que serão possível anular a serialização no payload do acionador.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

Em JavaScript, a desserialização de JSON é automaticamente executada e pode utilizar as propriedades diretamente.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

## <a name="configuring-binding-data-at-runtime"></a>Configuração de enlace de dados em tempo de execução

Em c# e outras linguagens .NET, pode utilizar um padrão de enlace imperativo, por oposição os enlaces declarativos no *function.json*. Enlace imperativo é útil quando os parâmetros de enlace tem de ser calculada ao tempo de tempo de execução, em vez de design. Para obter mais informações, consulte [enlace no tempo de execução através dos enlaces imperativo](functions-reference-csharp.md#imperative-bindings) a referência de programador do c#.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre um enlace específico, consulte os artigos seguintes:

- [HTTP e webhooks](functions-bindings-http-webhook.md)
- [Temporizador](functions-bindings-timer.md)
- [Armazenamento de filas](functions-bindings-storage-queue.md)
- [Armazenamento de blobs](functions-bindings-storage-blob.md)
- [Armazenamento de tabelas](functions-bindings-storage-table.md)
- [Hub de Eventos](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [BD do Cosmos para o Azure](functions-bindings-documentdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Hubs de Notificação](functions-bindings-notification-hubs.md)
- [Aplicações Móveis](functions-bindings-mobile-apps.md)
- [Ficheiro externo](functions-bindings-external-file.md)
