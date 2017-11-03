---
title: "Processar tipos de conteúdo - Azure Logic Apps | Microsoft Docs"
description: "Forma como o Azure Logic Apps lida com tipos de conteúdo em tempo de execução e de design"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: ac67838344bbd10384299c086ff096fbe5dec6a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="handle-content-types-in-logic-apps"></a>Processar tipos de conteúdo nas logic apps

Vários tipos de conteúdo podem fluir através de uma aplicação lógica, incluindo JSON, XML, ficheiros simples e os dados binários. Enquanto o motor de aplicações lógicas suporta todos os tipos de conteúdo, algumas nativamente compreendidas pelo motor de aplicações lógicas. Outras pessoas poderão necessitar de conversão ou conversões conforme necessário. Este artigo descreve como o motor processa diferentes tipos de conteúdo e como processar corretamente estes tipos quando for necessário.

## <a name="content-type-header"></a>Cabeçalho de tipo de conteúdo

Para iniciar basicamente, vamos ver dois `Content-Types` que não necessitam de conversão ou conversão que pode utilizar uma aplicação lógica: `application/json` e `text/plain`.

## <a name="applicationjson"></a>Aplicação/JSON

O motor de fluxo de trabalho depende o `Content-Type` chama o cabeçalho de HTTP para determinar o processamento adequado. Qualquer pedido com o tipo de conteúdo `application/json` é armazenada e processada como um objeto JSON. Além disso, é possível analisar conteúdo JSON por predefinição sem necessitar de qualquer conversão. 

Por exemplo, foi possível analisar um pedido que tem o cabeçalho de tipo de conteúdo `application/json ` num fluxo de trabalho, utilizando uma expressão como `@body('myAction')['foo'][0]` para obter o valor `bar` neste caso:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

Não é necessária nenhuma conversão adicional. Se estiver a trabalhar com dados que é JSON, mas não tem um cabeçalho especificado, pode manualmente convertido-lo no JSON utilizando o `@json()` funcione, por exemplo: `@json(triggerBody())['foo']`.

### <a name="schema-and-schema-generator"></a>Esquema e o gerador de esquema

O acionador pedido permite-lhe para introduzir um esquema JSON para o payload de que espera para receber. Este esquema permite os tokens de gerar estruturador para que possa consumir o conteúdo do pedido. Se não tiver um esquema pronto, selecione **payload de exemplo de utilização para gerar o esquema**, por isso, pode gerar um esquema JSON a partir de um payload de exemplo.

![Esquema](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>Ação 'Analisar JSON'

O `Parse JSON` ação permite-lhe analisar o conteúdo JSON em tokens amigáveis para o consumo de aplicação lógica. Semelhante para o acionador pedido, esta ação permite-lhe introduzir ou gerar um esquema JSON para o conteúdo que pretende analisar. Esta ferramenta faz com que o consumo de dados do Service Bus, base de dados do Azure Cosmos e assim sucessivamente, muito mais fácil.

![Analisar JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>texto/plain

Semelhante ao `application/json`, mensagens de HTTP recebidas com o `Content-Type` cabeçalho `text/plain` são armazenadas num formato não processado. Além disso, se as mensagens são incluídas nos ações posteriores sem conversão, esses pedidos aceda com `Content-Type`: `text/plain` cabeçalho. Por exemplo, ao trabalhar com um ficheiro simples, poderá obter esta HTTP conteúdo como `text/plain`:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

Se a próxima ação, enviar o pedido como o corpo do pedido de outro (`@body('flatfile')`), o pedido teria um `text/plain` cabeçalho Content-Type. Se estiver a trabalhar com dados que é o texto simples, mas não tem um cabeçalho especificado, manualmente pode converter os dados de texto utilizando o `@string()` funcione, por exemplo: `@string(triggerBody())`.

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Aplicação/xml e as funções de Application/octet-stream e conversor

O motor de aplicações lógicas sempre preserva a `Content-Type` que foi recebido no pedido de HTTP ou resposta. Por isso, caso o motor recebe conteúdo com o `Content-Type` de `application/octet-stream`, e o inclui que conteúdo sem conversão de uma ação de subsequente, o pedido de saída tem `Content-Type`: `application/octet-stream`. Desta forma, o motor pode garantir a dados não estão perder-se ao mover através do fluxo de trabalho. No entanto, o estado da ação (entradas e saídas) é armazenado num objeto JSON como o estado passa pelo fluxo de trabalho. Para preservar alguns tipos de dados, o motor converte o conteúdo para uma cadeia codificada em base64 binário com os metadados adequados que preserva ambos `$content` e `$content-type`, que são automaticamente convertido. 

* `@json()`-casts dados`application/json`
* `@xml()`-casts dados`application/xml`
* `@binary()`-casts dados`application/octet-stream`
* `@string()`-casts dados`text/plain`
* `@base64()`-Converte o conteúdo para uma cadeia base64
* `@base64toString()`-Converte uma cadeia codificada em base64 para`text/plain`
* `@base64toBinary()`-Converte uma cadeia codificada em base64 para`application/octet-stream`
* `@encodeDataUri()`-codifica uma cadeia como uma matriz de bytes dataUri
* `@decodeDataUri()`-Descodifica um dataUri para uma matriz de bytes

Por exemplo, se recebeu um pedido HTTP com `Content-Type`: `application/xml`:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Foi convertido e utilizar mais tarde com um nome semelhante `@xml(triggerBody())`, ou de uma função, como `@xpath(xml(triggerBody()), '/CustomerName')`.

## <a name="other-content-types"></a>Outros tipos de conteúdo

Outros tipos de conteúdo são suportados e trabalhar com as logic apps, mas pode exigir manualmente ao obter o corpo da mensagem ao descodificar o `$content`. Por exemplo, suponha que acionam uma `application/x-www-url-formencoded` pedido onde `$content` é o payload codificado como uma cadeia base64 para preservar a todos os dados:

```
CustomerName=Frank&Address=123+Avenue
```

Porque o pedido não está em texto simples ou JSON, o pedido é armazenado na ação da seguinte forma:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Atualmente, não é uma função de nativa para dados do formulário, pelo que pode ainda utilizar estes dados num fluxo de trabalho acedendo manualmente os dados com uma função, como `@string(body('formdataAction'))`. Se pretendesse pedidos a enviar também tem o `application/x-www-url-formencoded` cabeçalho de tipo de conteúdo, pode adicionar o pedido para o corpo de ação sem qualquer conversão como `@body('formdataAction')`. No entanto, este método só funciona se o corpo é o parâmetro apenas o `body` entrada. Se tentar utilizar `@body('formdataAction')` num `application/json` pedir, receberá um erro de tempo de execução porque o corpo codificado é enviado.

