---
title: "Enlaces de aplicações móveis para as funções do Azure"
description: "Compreenda como utilizar os enlaces de Mobile Apps do Azure das funções do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 3c29c43f88608760cc6d5f19f27f692c8448ebd9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Enlaces de aplicações móveis para as funções do Azure 

Este artigo explica como trabalhar com [Mobile Apps do Azure](../app-service-mobile/app-service-mobile-value-prop.md) enlaces de funções do Azure. Funções do Azure suporta entrada e saída enlaces para Mobile Apps.

Os enlaces de aplicações móveis permitem-lhe ler e atualizar as tabelas de dados em aplicações móveis.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="input"></a>Input

O enlace de entrada de Mobile Apps carrega um registo a partir de um ponto final de tabela móveis e passa-o para a sua função. Em c# e F # funções, todas as alterações efetuadas ao registo são automaticamente enviadas voltar à tabela quando a função sai com sucesso.

## <a name="input---example"></a>Entrada - exemplo

Veja o exemplo de específicas do idioma:

<!-- * [Precompiled C#](#input---c-example)-->
* [Script do c#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Entrada - exemplo de script do c#

O exemplo seguinte mostra um enlace de entrada de aplicações móveis num *function.json* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função é acionada por uma mensagem de fila tem um identificador de registo. A função lê o registo especificado e modifica o respetivo `Text` propriedade.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```
O [configuração](#input---configuration) secção explica estas propriedades.

Eis o código de script do c#:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>Entrada - JavaScript

O exemplo seguinte mostra um enlace de entrada de aplicações móveis num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. A função é acionada por uma mensagem de fila tem um identificador de registo. A função lê o registo especificado e modifica o respetivo `Text` propriedade.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```
O [configuração](#input---configuration) secção explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Entrada - atributos

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize o [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atributo, que está definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps).

Para obter informações sobre as propriedades de atributo que pode configurar, consulte [a secção de configuração seguintes](#input---configuration).

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `MobileTable` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo**|| Tem de ser definido como "mobileTable"|
| **direção**||Tem de ser definido como "em"|
| **nome**|| Nome do parâmetro de entrada na assinatura da função.|
|**tableName** |**TableName**|Nome da tabela de dados da aplicação móvel|
| **ID**| **ID** | O identificador do registo para obter. Pode ser estático ou com base no acionador que invoca a função. Por exemplo, se utilizar um acionador de fila para a sua função, em seguida, `"id": "{queueTrigger}"` utiliza o valor da cadeia da mensagem de fila como o ID do registo para obter.|
|**ligação**|**Ligação**|O nome de uma definição de aplicação que tenha o URL da aplicação móvel. A função utiliza este URL para construir as operações necessárias de REST em relação a sua aplicação móvel. Crie uma definição de aplicação na sua aplicação de função que contém o URL da aplicação móvel, em seguida, especifique o nome da definição de aplicação no `connection` propriedade de enlace de entrada. O URL do aspeto `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|O nome de uma definição de aplicação que tenha a chave de API da sua aplicação móvel. Forneça a caso da chave de API [uma chave de API de implementar na sua aplicação móvel do Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), ou [uma chave de API de implementar na sua aplicação móvel .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma definição de aplicação na sua aplicação de função que contém a chave de API, em seguida, adicione o `apiKey` propriedade no seu enlace de entrada com o nome da definição de aplicação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não partilhe a chave de API com os clientes de aplicação móvel. Este deve apenas ser distribuído em segurança para os clientes do lado do serviço, como as funções do Azure. As funções do Azure armazena as informações de ligação e chaves de API como as definições de aplicação para que estes não são verificados para o repositório de controlo de origem. Esta salvaguarda as suas informações confidenciais.

## <a name="input---usage"></a>Entrada - utilização

C# funções, quando o registo com o ID especificado encontra-se, ser transmitido para o nomeado [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parâmetro. Quando não for encontrado o registo, o valor do parâmetro é `null`. 

Nas funções de JavaScript, o registo é transmitido a `context.bindings.<name>` objeto. Quando não for encontrado o registo, o valor do parâmetro é `null`. 

Nas funções de c# e F #, quaisquer alterações efetuadas a entrada de registo (o parâmetro de entrada) são automaticamente enviados voltar à tabela quando a função sai com sucesso. Não é possível modificar um registo na funções JavaScript.

## <a name="output"></a>Saída

Utilize a saída de Mobile Apps do enlace para escrever um novo registo de uma tabela de aplicações móveis.  

## <a name="output---example"></a>De saída - exemplo

Veja o exemplo de específicas do idioma:

* [Pré-compilada c#](#output---c-example)
* [Script do c#](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - c# exemplo

O seguinte exemplo mostra um [pré-compilada c# função](functions-dotnet-class-library.md) que é acionado por uma mensagem de fila e cria um registo de uma tabela de aplicação móvel.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script do c#

O exemplo seguinte mostra uma saída de Mobile Apps enlace num *function.json* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função é acionada por uma mensagem de fila e cria um novo registo com o valor hard-coded para o `Text` propriedade.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

O [configuração](#output---configuration) secção explica estas propriedades.

Eis o código de script do c#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Saída - exemplo de JavaScript

O exemplo seguinte mostra uma saída de Mobile Apps enlace num *function.json* ficheiro e uma [JavaScript função](functions-reference-node.md) que utiliza o enlace. A função é acionada por uma mensagem de fila e cria um novo registo com o valor hard-coded para o `Text` propriedade.

Segue-se os dados do enlace *function.json* ficheiro:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

O [configuração](#output---configuration) secção explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Saída - atributos

Para [pré-compilada c#](functions-dotnet-class-library.md) funções, utilize o [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) atributo, que está definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps).

Para obter informações sobre as propriedades de atributo que pode configurar, consulte [de saída - configuração](#output---configuration). Eis um `MobileTable` exemplo de atributo na assinatura do método:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Para obter um exemplo completado, consulte [resultado - pré-compilada c# exemplo](#output---c-example).

## <a name="output---configuration"></a>De saída - configuração

A tabela seguinte explica as propriedades de configuração de enlace que definir no *function.json* ficheiros e o `MobileTable` atributo.

|propriedade de Function.JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
| **tipo**|| Tem de ser definido como "mobileTable"|
| **direção**||Tem de ser definido como "out"|
| **nome**|| Nome do parâmetro de saída na assinatura da função.|
|**tableName** |**TableName**|Nome da tabela de dados da aplicação móvel|
|**ligação**|**MobileAppUriSetting**|O nome de uma definição de aplicação que tenha o URL da aplicação móvel. A função utiliza este URL para construir as operações necessárias de REST em relação a sua aplicação móvel. Crie uma definição de aplicação na sua aplicação de função que contém o URL da aplicação móvel, em seguida, especifique o nome da definição de aplicação no `connection` propriedade de enlace de entrada. O URL do aspeto `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|O nome de uma definição de aplicação que tenha a chave de API da sua aplicação móvel. Forneça a caso da chave de API [implementar uma chave de API no seu back-end da aplicação móvel de Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), ou [implementar uma chave de API no seu back-end da aplicação móvel de .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para fornecer a chave, crie uma definição de aplicação na sua aplicação de função que contém a chave de API, em seguida, adicione o `apiKey` propriedade no seu enlace de entrada com o nome da definição de aplicação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Não partilhe a chave de API com os clientes de aplicação móvel. Este deve apenas ser distribuído em segurança para os clientes do lado do serviço, como as funções do Azure. As funções do Azure armazena as informações de ligação e chaves de API como as definições de aplicação para que estes não são verificados para o repositório de controlo de origem. Esta salvaguarda as suas informações confidenciais.

## <a name="output---usage"></a>Saída - utilização

Em c# funções de script, utilize um parâmetro de saída com o nome do tipo `out object` para aceder ao registo de saída. Pré-compilada c# funções, o `MobileTable` atributo pode ser utilizado com qualquer um dos seguintes tipos:

* `ICollector<T>`ou `IAsyncCollector<T>`, onde `T` está `JObject` ou de qualquer tipo com um `public string Id` propriedade.
* `out JObject`
* `out T`ou `out T[]`, onde `T` é qualquer tipo com um `public string Id` propriedade.

Nas funções de Node.js, utilize `context.bindings.<name>` para aceder ao registo de saída.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
