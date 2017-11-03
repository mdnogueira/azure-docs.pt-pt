---
title: "Enlaces de funções Mobile Apps do Azure | Microsoft Docs"
description: "Compreenda como utilizar os enlaces de Mobile Apps do Azure das funções do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
ms.openlocfilehash: d2c0e4e233761584bad2df05a8e702e4fc77e84f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-mobile-apps-bindings"></a>Enlaces de funções Mobile Apps do Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e code [Mobile Apps do Azure](../app-service-mobile/app-service-mobile-value-prop.md) enlaces de funções do Azure. Funções do Azure suporta entrada e saída enlaces para Mobile Apps.

As Mobile Apps de entrada e saída enlaces permitem-lhe [leem e escrevem para tabelas de dados](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) na sua aplicação móvel.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Enlace de entrada de aplicações móveis
O enlace de entrada de Mobile Apps carrega um registo a partir de um ponto final de tabela móveis e passa-o para a sua função. Num c# e F # funções, todas as alterações efetuadas ao registo são automaticamente enviadas voltar à tabela quando a função sai com sucesso.

A entrada de Mobile Apps para uma função utiliza o seguinte objeto JSON no `bindings` matriz de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

Tenha em atenção o seguinte:

* `id`pode ser estático ou pode ser baseado no acionador que invoca a função. Por exemplo, se utilizar um [acionador de fila]() para a sua função, em seguida, `"id": "{queueTrigger}"` utiliza o valor da cadeia da mensagem de fila como o ID do registo para obter.
* `connection`deve conter o nome de uma definição de aplicação na sua aplicação de função, o que por sua vez contém o URL da sua aplicação móvel. A função utiliza este URL para construir as operações necessárias de REST em relação a sua aplicação móvel. [Criar uma definição de aplicação na sua aplicação de função]() que contém o URL da aplicação móvel (que se parece com `http://<appname>.azurewebsites.net`), em seguida, especifique o nome da definição de aplicação no `connection` propriedade de enlace de entrada. 
* Tem de especificar `apiKey` se lhe [implementar uma chave de API no seu back-end da aplicação móvel de Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), ou [implementar uma chave de API no seu back-end da aplicação móvel de .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para tal, [criar uma definição de aplicação na sua aplicação de função]() que contém a chave de API, em seguida, adicione o `apiKey` propriedade no seu enlace de entrada com o nome da definição de aplicação. 
  
  > [!IMPORTANT]
  > Esta chave de API não tem de ser partilhada com os clientes de aplicação móvel. Este deve apenas ser distribuído em segurança para os clientes do lado do serviço, como as funções do Azure. 
  > 
  > [!NOTE]
  > As funções do Azure armazena as informações de ligação e chaves de API como as definições de aplicação para que estes não são verificados para o repositório de controlo de origem. Esta salvaguarda as suas informações confidenciais.
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>Utilização de entrada
Esta secção mostra como utilizar o enlace de entrada de aplicações móveis no seu código de função. 

Quando o registo com o ID de registo e tabela especificada for encontrado, é passada para o nome [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) parâmetro (ou, no Node.js, é transmitido a `context.bindings.<name>` objeto). Quando não for encontrado o registo, o parâmetro é `null`. 

Nas funções de c# e F #, quaisquer alterações efetuadas a entrada de registo (o parâmetro de entrada) é enviado automaticamente a voltar à tabela das Mobile Apps quando a função sai com sucesso. Nas funções de Node.js, utilize `context.bindings.<name>` para aceder ao registo de entrada. Não é possível modificar um registo no Node.js.

<a name="inputsample"></a>

## <a name="input-sample"></a>Exemplo de entrada
Suponha que tem o seguinte function.json, que obtém um registo de tabela de aplicação móvel com o id da mensagem de Acionador de fila:

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

Consulte o exemplo de específicas do idioma que utiliza o registo de entrada do enlace. Os exemplos de c# e F # também modificar o registo `text` propriedade.

* [C#](#inputcsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Exemplo de entrada em c# #

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

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Exemplo de entrada no Node.js

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Enlace de saída de aplicações móveis
Utilize a saída de Mobile Apps do enlace para escrever um novo registo para um ponto final de tabela de aplicações móveis.  

As Mobile Apps para uma função utiliza o seguinte objeto JSON de saída a `bindings` matriz de function.json:

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

Tenha em atenção o seguinte:

* `connection`deve conter o nome de uma definição de aplicação na sua aplicação de função, o que por sua vez contém o URL da sua aplicação móvel. A função utiliza este URL para construir as operações necessárias de REST em relação a sua aplicação móvel. [Criar uma definição de aplicação na sua aplicação de função]() que contém o URL da aplicação móvel (que se parece com `http://<appname>.azurewebsites.net`), em seguida, especifique o nome da definição de aplicação no `connection` propriedade de enlace de entrada. 
* Tem de especificar `apiKey` se lhe [implementar uma chave de API no seu back-end da aplicação móvel de Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), ou [implementar uma chave de API no seu back-end da aplicação móvel de .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Para tal, [criar uma definição de aplicação na sua aplicação de função]() que contém a chave de API, em seguida, adicione o `apiKey` propriedade no seu enlace de entrada com o nome da definição de aplicação. 
  
  > [!IMPORTANT]
  > Esta chave de API não tem de ser partilhada com os clientes de aplicação móvel. Este deve apenas ser distribuído em segurança para os clientes do lado do serviço, como as funções do Azure. 
  > 
  > [!NOTE]
  > As funções do Azure armazena as informações de ligação e chaves de API como as definições de aplicação para que estes não são verificados para o repositório de controlo de origem. Esta salvaguarda as suas informações confidenciais.
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilização de saída
Esta secção mostra como utilizar a saída de Mobile Apps enlace no código da função. 

C# funções, utilizar um parâmetro de saída com o nome do tipo `out object` para aceder ao registo de saída. Nas funções de Node.js, utilize `context.bindings.<name>` para aceder ao registo de saída.

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemplo de saída
Suponha que tem o seguinte function.json, que define um acionador de fila e um resultado de Mobile Apps:

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

Consulte o exemplo de específicas do idioma que cria um registo no ponto final de tabela de aplicações móveis com o conteúdo da mensagem de fila.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemplo de saída em c# #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Exemplo de saída no Node.js

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

