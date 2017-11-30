---
title: "Referência para programadores de JavaScript para as funções do Azure | Microsoft Docs"
description: "Compreenda como desenvolver funções utilizando o JavaScript."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: tdykstra
ms.openlocfilehash: f613e480f6699b323c18402f01873e565768f10f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="azure-functions-javascript-developer-guide"></a>Guia para programadores do JavaScript de funções do Azure
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

A experiência de JavaScript para as funções do Azure torna mais fácil exportar uma função, o que é transmitida como um `context` objeto para comunicar com o tempo de execução e para receber e enviar dados através dos enlaces.

Este artigo pressupõe que já leu o [referência para programadores de funções do Azure](functions-reference.md).

## <a name="exporting-a-function"></a>Exportar uma função
Todas as funções de JavaScript tem de exportar um único `function` através de `module.exports` para o tempo de execução localizar a função e executá-la. Esta função tem de incluir sempre um `context` objeto.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Enlaces de `direction === "in"` são transferidas como argumentos de função, o que significa que pode utilizar [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) dinamicamente processar entradas novo (por exemplo, utilizando `arguments.length` para iterar sobre os parâmetros e). Esta funcionalidade é conveniente quando tem apenas um acionador e não existem instruções adicionais, uma vez previsibilidade podem aceder os dados de Acionador sem a referenciar o `context` objeto.

Os argumentos são sempre transferidos para a função na ordem que ocorrem no *function.json*, mesmo se não especificá-los na sua declaração de exportações. Por exemplo, se tiver `function(context, a, b)` e altere-o para `function(context, a)`, ainda pode obter o valor da `b` no código de função ao referir-se para `arguments[2]`.

Todos os enlaces, independentemente de direção, também são transmitidos ao longo de `context` objeto (consulte o seguinte script). 

## <a name="context-object"></a>objeto de contexto
O tempo de execução utiliza um `context` objeto para transmitir dados para e da sua função e permitir-lhe comunicar com o tempo de execução.

O `context` objeto é sempre o primeiro parâmetro para uma função e tem de ser incluído porque tem métodos tais como `context.done` e `context.log`, que são necessários para utilizar o runtime corretamente. Pode atribuir o nome do objeto que gostaria de (por exemplo, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>propriedade de Context.Bindings

```
context.bindings
```
Devolve um objeto com o nome que contém todos os seus dados de entrada e de saída. Por exemplo, a seguinte definição de enlace no seu *function.json* permite aceder o conteúdo da fila do `context.bindings.myInput` objeto. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### <a name="contextdone-method"></a>método de Context.Done
```
context.done([err],[propertyBag])
```

Informa o tempo de execução que terminou o seu código. Tem de chamar `context.done`, ou caso contrário o tempo de execução sabe nunca que sua função é concluída, e a execução do tempo limite. 

O `context.done` método permite-lhe transmitir novamente ambos um erro definidas pelo utilizador para o tempo de execução e de uma matriz de propriedades de propriedades que substituir as propriedades no `context.bindings` objeto.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>método de Context.log  

```
context.log(message)
```
Permite-lhe escrever os registos de consola transmissão em fluxo no nível de rastreio predefinido. No `context.log`, métodos de registo adicionais estão disponíveis, que permitem-lhe escrever no registo de consola em outros níveis de rastreio:


| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **erro (_mensagem_)**   | Escreve o nível de erro registo ou inferior.   |
| **avisar (_mensagem_)**    | Escreve o nível de aviso de registo ou inferior. |
| **as informações (_mensagem_)**    | Escreve informações nível de registo, ou inferior.    |
| **verboso (_mensagem_)** | Escreve o registo ao nível verboso.           |

O exemplo seguinte escritas para a consola ao nível de rastreio do aviso:

```javascript
context.log.warn("Something has happened."); 
```
Pode definir o limiar de nível de rastreio para registo no ficheiro host.json ou desativá-la.  Para obter mais informações sobre como escrever nos registos, consulte a secção seguinte.

## <a name="binding-data-type"></a>Tipo de enlace de dados

Para definir o tipo de dados para um enlace de entrada, utilize o `dataType` propriedade na definição do enlace. Por exemplo, para ler o conteúdo de um pedido HTTP no formato binário, utilize o tipo de `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Outras opções para `dataType` são `stream` e `string`.

## <a name="writing-trace-output-to-the-console"></a>Resultados de rastreio de escrita para a consola do 

Nas funções, utilize o `context.log` métodos escrever os resultados de rastreio para a consola. Neste momento, não é possível utilizar `console.log` para escrever na consola.

Quando chamar `context.log()`, é escrita da mensagem para a consola no nível de rastreio predefinido, que é o _informações_ nível de rastreio. Escreve o seguinte código para a consola ao nível de rastreio de informações:

```javascript
context.log({hello: 'world'});  
```

O código anterior é equivalente ao seguinte código:

```javascript
context.log.info({hello: 'world'});  
```

Escreve o seguinte código para a consola ao nível do erro:

```javascript
context.log.error("An error has occurred.");  
```

Porque _erro_ é o rastreio mais elevado nível, este rastreio é escrito à saída de todos os níveis de rastreio, desde que o registo está ativado.  


Todos os `context.log` métodos suportam o mesmo formato de parâmetro que é suportado pelo Node.js [util.format método](https://nodejs.org/api/util.html#util_util_format_format). Considere o seguinte código, que escreve para a consola utilizando o nível de rastreio predefinido:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Também pode escrever o mesmo código no seguinte formato:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurar o nível de rastreio para o registo de consola

As funções permite-lhe definir o nível de rastreio de limiar para escrever na consola, o que torna mais fácil para controlar os rastreios de forma são escritos na consola das suas funções. Para definir o limiar para todos os rastreios escritos na consola, utilize o `tracing.consoleLevel` propriedade no ficheiro host.json. Esta definição aplica-se a todas as funções na sua aplicação de função. O exemplo a seguir define o limiar de rastreio para ativar o registo verboso:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Os valores de **consoleLevel** correspondem aos nomes do `context.log` métodos. Para desativar o registo de rastreio de todas as na consola, defina **consoleLevel** para _desativar_. Para obter mais informações, consulte [host.json referência](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>HTTP de acionadores e enlaces

HTTP e acionadores de webhook e HTTP de saída vínculos utilizar objetos de pedido e resposta para representar a mensagens de HTTP.  

### <a name="request-object"></a>Objeto de pedido

O `request` objeto tem as seguintes propriedades:

| Propriedade      | Descrição                                                    |
| ------------- | -------------------------------------------------------------- |
| _corpo_        | Um objeto que contém o corpo do pedido.               |
| _cabeçalhos_     | Um objeto que contém os cabeçalhos do pedido.                   |
| _método_      | O método HTTP do pedido.                                |
| _originalUrl_ | O URL do pedido.                                        |
| _parâmetros_      | Um objeto que contém os parâmetros de encaminhamento do pedido. |
| _consulta_       | Um objeto que contém os parâmetros de consulta.                  |
| _rawBody_     | O corpo da mensagem como uma cadeia.                           |


### <a name="response-object"></a>Objeto de resposta

O `response` objeto tem as seguintes propriedades:

| Propriedade  | Descrição                                               |
| --------- | --------------------------------------------------------- |
| _corpo_    | Um objeto que contém o corpo da resposta.         |
| _cabeçalhos_ | Um objeto que contém os cabeçalhos de resposta.             |
| _isRaw_   | Indica que a formatação é ignorada para a resposta.    |
| _Estado_  | O código de estado HTTP da resposta.                     |

### <a name="accessing-the-request-and-response"></a>Aceder a pedido e resposta 

Quando trabalha com os acionadores HTTP, pode aceder os objetos de pedido e resposta HTTP em qualquer um dos três formas:

+ De entrada com o nome e de enlaces de saída. Desta forma, o acionador HTTP e os enlaces funcionam da mesma como qualquer outro enlace. O exemplo a seguir define o objecto de resposta utilizando um nomeado `response` enlace: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ De `req` e `res` propriedades no `context` objeto. Desta forma, pode utilizar o padrão convencional para dados de acesso HTTP do objeto de contexto, em vez de ter de utilizar o completo `context.bindings.name` padrão. O exemplo seguinte mostra como pode aceder a `req` e `res` objetos no `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Ao chamar `context.done()`. Um tipo especial de enlace de HTTP devolve a resposta que é transferida para o `context.done()` método. A seguinte HTTP de saída enlace define um `$return` parâmetro de saída:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    Este enlace de saída espera lhe fornecer a resposta ao chamar `done()`, da seguinte forma:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Gestão de versão e o pacote de nó
A versão do nó está bloqueada atualmente `6.5.0`. Iremos estiver a investigar adicionar suporte para versões mais e tornando configurável.

Os passos seguintes permitem incluir pacotes na sua aplicação de função: 

1. Aceda a `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **consola de depuração** > **CMD**.

3. Aceda a `D:\home\site\wwwroot`e, em seguida, arraste o ficheiro Package. JSON para o **wwwroot** pasta na metade superior da página.  
    Também pode carregar ficheiros para a sua aplicação de função de outras formas. Para obter mais informações, consulte [como atualizar os ficheiros de aplicação de função](functions-reference.md#fileupdate). 

4. Depois do ficheiro Package. JSON é carregado, execute o `npm install` no **consola de execução remota do Kudu**.  
    Esta ação transfere os pacotes indicados no ficheiro Package. JSON e reinicia a aplicação de função.

Depois de instalar os pacotes precisa, importá-los para a função ao chamar `require('packagename')`, como no exemplo seguinte:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Deve definir um `package.json` ficheiro na raiz da sua aplicação de função. Definir o ficheiro permite que todas as funções na aplicação partilham os mesmos pacotes e em cache, o que lhe oferece o melhor desempenho. Se for um conflito de versões, possa resolver adicionando um `package.json` ficheiro na pasta de uma função específica.  

## <a name="environment-variables"></a>Variáveis de ambiente
Para obter uma variável de ambiente ou uma valor de definição de aplicação, utilize `process.env`, conforme mostrado no exemplo de código seguinte:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## <a name="considerations-for-javascript-functions"></a>Considerações para funções de JavaScript

Quando trabalha com funções JavaScript, lembre-se de que as considerações em duas secções seguintes.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolha único vCPU planos de serviços aplicacionais

Quando cria uma aplicação de função que utiliza o plano do App Service, recomendamos que selecione um plano do single-vCPU, em vez de um plano com vários vCPUs. Hoje em dia, funções é executado funções JavaScript de forma mais eficiente em VMs único vCPU, e utilizar VMs maior não produz os melhoramentos de desempenho esperadas. Quando for necessário, manualmente pode aumentar horizontalmente adicionando mais instâncias VM única vCPU, ou pode ativar o dimensionamento automático. Para obter mais informações, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Suporte typeScript e CoffeeScript
Porque o suporte direto ainda não existe para compilar o automática TypeScript ou CoffeeScript através de tempo de execução, esse suporte tem de ser processada fora do tempo de execução, no momento da implementação. 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte os seguintes recursos:

* [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
* [Referência para programadores das Funções do Azure](functions-reference.md)
* [Acionadores de funções do Azure e os enlaces](functions-triggers-bindings.md)

