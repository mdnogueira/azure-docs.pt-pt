---
title: "Extensões de OpenAPI para conectores personalizados - Azure Logic Apps | Microsoft Docs"
description: "Expandir o OpenAPI seu conector personalizado com funcionalidades avançadas"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>Expandir o OpenAPI seu conector personalizado com funcionalidades avançadas

Para criar conectores personalizados para o Azure Logic Apps, Microsoft Flow ou Microsoft PowerApps, tem de fornecer um ficheiro de definição OpenAPI, que é um documento machine-readable linguagem que descrevem a API operações e parâmetros. Juntamente com a funcionalidade de out of box do OpenAPI, também pode incluir estas extensões OpenAPI quando criar conectores personalizados para as Logic Apps e fluxo:

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

Seguem-se mais detalhes sobre estas extensões:

<a name="summary"></a>

## <a name="summary"></a>Resumo

Especifica o título para a ação (operação). </br>
Aplica-se a: operações </br>
Recomendado: Utilizar *o frase caso* para `summary`. </br>
Exemplo: "quando é adicionado um evento ao calendário" ou "Enviar mensagens de correio eletrónico"

!["Resumo" para cada operação](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-resumo

Especifica o título de uma entidade. </br>
Aplica-se a: parâmetros, o esquema de resposta </br>
Recomendado: Utilizar *title caso* para `x-ms-summary`. </br>
Exemplo: "ID de calendário", "Requerente", "Descrição do evento" e assim sucessivamente

!["x-ms-Resumo" para cada entidade](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>descrição

Especifica uma explicação sobre a funcionalidade da operação ou uma entidade formato e a função verbosa. </br>
Aplica-se a: esquema de resposta de operações, parâmetros, </br>
Recomendado: Utilizar *o frase caso* para `description`. </br>
Exemplo: "Esta operação é acionado quando é adicionado um novo evento para o calendário", "Especifique o assunto do correio", e assim sucessivamente

!["Descrição" para cada operação ou entidade](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-visibilidade

Especifica a visibilidade destinada ao utilizador para uma entidade. </br>
Os valores possíveis: `important`, `advanced`, e`internal` </br>
Aplica-se a: esquemas de operações, parâmetros,

* `important`operações e os parâmetros são sempre apresentados ao utilizador primeiro.
* `advanced`operações e os parâmetros estão ocultos nas sob um menu adicional.
* `internal`operações e os parâmetros estão ocultos do utilizador.

> [!NOTE] 
> Para os parâmetros que são `internal` e `required`, **tem** fornecer valores predefinidos para estes parâmetros.

Exemplo: O **ver mais** menu e **Mostrar opções avançadas** menu são ocultar `advanced` operações e parâmetros.

!["x-ms-visibilidade" para mostrar ou ocultar as operações e parâmetros](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>x-ms-dinâmico-valores

Mostra uma lista povoada para o utilizador, pelo que podem selecionar os parâmetros de entrada para uma operação. </br>
Aplica-se a: parâmetros </br>
Como utilizar: adicionar o `x-ms-dynamic-values` objeto à definição do parâmetro. Por exemplo, consulte este [OpenAPI exemplo](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

!["x-ms-dinâmico-valores" para mostrar listas](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>Propriedades de x-ms-dinâmico-valores

| Nome | Necessária ou opcional | Descrição | 
| ---- | -------------------- | ----------- | 
| **operationID** | Necessário | A chamada para preencher a lista da operação | 
| **caminho de valor** | Necessário | Uma cadeia de caminho no objeto dentro `value-collection` que refere-se o valor do parâmetro. </br>Se `value-collection` não está especificado, a resposta é avaliada como uma matriz. | 
| **título do valor** | Opcional | Uma cadeia de caminho no objeto dentro `value-collection` que refere-se a descrição do valor. </br>Se `value-collection` não está especificado, a resposta é avaliada como uma matriz. | 
| **coleção de valor** | Opcional | Uma cadeia de caminho que for avaliada como uma matriz de objetos no payload de resposta | 
| **parâmetros** | Opcional | Um objeto cujas propriedades especificar os parâmetros de entrada necessários para invocar uma operação dinâmica valores | 
|||| 

Eis um exemplo que mostra as propriedades no `x-ms-dynamic-values`:

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>Exemplo: Todas as OpenAPI extensões até este ponto

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dinâmico-schema

Indica que o esquema para o parâmetro atual ou a resposta é dinâmico. Este objeto pode invocar uma operação que está definida pelo valor neste campo, dinamicamente detetar o esquema e apresentar a IU adequada para a recolha de entradas do utilizador ou mostrar campos disponíveis. 

Aplica-se a: parâmetros, a resposta

Como utilizar: adicionar o `x-ms-dynamic-schema` objeto para uma definição de corpo de resposta ou parâmetro pedido. Por exemplo, consulte este [OpenAPI exemplo](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

Este exemplo mostra como o formulário de entrada for alterada, com base no item que o utilizador seleciona na lista pendente:

!["x-ms-dinâmico-schema" para os parâmetros dinâmicos](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

E este exemplo mostra como alteram as saídas, com base no item que o utilizador seleciona na lista pendente. Nesta versão, o utilizador seleciona "Carros":

!["x-ms-dinâmico-esquema-resposta" para o item selecionado "Carros"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

Nesta versão, o utilizador seleciona "Prato":

!["x-ms-dinâmico-esquema-resposta" para o item selecionado "Prato"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>Propriedades de x-ms-dinâmico-schema

| Nome | Necessária ou opcional | Descrição | 
| ---- | -------------------- | ----------- | 
| **operationID** | Necessário | A chamada para obter o esquema da operação | 
| **parâmetros** | Necessário | Um objeto cujas propriedades especificar os parâmetros de entrada necessários para invocar uma operação de esquema dinâmico | 
| **caminho de valor** |Opcional | Uma cadeia de caminho que refere-se para a propriedade com o esquema. </br>Se não for especificado, a resposta é assumida que contém o esquema nas propriedades do objeto de raiz. | 
|||| 

Eis um exemplo de um parâmetro dinâmico:

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

Eis um exemplo de uma resposta dinâmica:

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Descrevem as APIs e conectores personalizado](../logic-apps/custom-connector-api-postman-collection.md)
* [As Logic Apps: Registar o conector](../logic-apps/logic-apps-custom-connector-register.md)
* [Fluxo: Registar o conector](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)