---
title: "Código personalizado para o Azure Logic Apps com as funções do Azure | Microsoft Docs"
description: "Criar e executar código personalizado para o Azure Logic Apps com as funções do Azure"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 18442c87b049200fac5ed41cc7034ba7a848b8d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>Adicionar e executar código personalizado para aplicações lógicas através das funções do Azure

Para executar fragmentos personalizados de c# ou node.js as logic apps, pode criar funções personalizadas através das funções do Azure. 
[As funções do Azure](../azure-functions/functions-overview.md) oferece livre de servidor de computação no Microsoft Azure e são úteis para efetuar estas tarefas:

* Avançadas formatação ou de computação de campos nas logic apps
* Efetue cálculos num fluxo de trabalho.
* Expandir a funcionalidade de aplicações lógicas com funções que são suportadas em c# ou node.js

## <a name="create-custom-functions-for-your-logic-apps"></a>Criar funções personalizadas para as logic apps

Recomendamos que crie uma função no portal das funções do Azure, a partir de **genérico Webhook - nó** ou **Webhook genérico - c#** modelos. O resultado cria um preenchido automaticamente um modelo que aceita `application/json` de uma aplicação lógica. Funções que criam destes modelos são automaticamente detetadas e aparecer no Designer de aplicação lógica em **das funções do Azure na minha região.**

No portal do Azure, no **integrar** painel para a sua função, o modelo deve mostrar que **modo** definido como **Webhook** e **Webhook tipo** está definido como **JSON genérico**. 

Funções de Webhook aceitar um pedido e transmita-o para o método através de um `data` variável. Pode aceder as propriedades do payload utilizando a notação de pontos como `data.function-name`. Por exemplo, uma função de JavaScript simple que converte um valor DateTime uma cadeia de data aspeto semelhante ao seguinte exemplo:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>Chamar as funções do Azure a partir das logic apps

Para listar os contentores na sua subscrição e selecione a função que pretende ligar, no Designer de aplicação lógica, clique em de **ações** menu e selecione uma das **das funções do Azure na minha região**.

Depois de selecionar a função, é-lhe pedido para especificar um objeto de payload de entrada. Este objeto é a mensagem que a aplicação lógica envia para a função e tem de ser um objeto JSON. Por exemplo, se pretender passar a **última modificação** data a partir de um acionador do Salesforce, o payload de função pode ter o seguinte aspeto neste exemplo:

![Data da última modificação][1]

## <a name="trigger-logic-apps-from-a-function"></a>Aplicações de lógica de Acionador de uma função

Pode acionar uma aplicação de lógica de dentro de uma função. Consulte [as Logic apps, como pontos finais possível chamar EndRead](logic-apps-http-endpoint.md). Criar uma aplicação lógica que tem um acionador manual, em seguida, de dentro da função, gerar um HTTP POST para o acionador manual URL com o payload de que pretende que sejam enviado para a aplicação lógica.

### <a name="create-a-function-from-logic-app-designer"></a>Criar uma função a partir do Designer de aplicação lógica

Também pode criar uma função de webhook do node.js a partir do designer. Em primeiro lugar, selecione **das funções do Azure na minha região** e, em seguida, escolha um contentor para a sua função. Se ainda não tiver um contentor, terá de criar um o [portal das funções do Azure](https://functions.azure.com/signin). Em seguida, selecione **criar novo**.  

Para gerar um modelo baseado nos dados que pretende que a computação, especifique o objeto de contexto que pretende passar para uma função. Este objeto tem de ser um objeto JSON. Por exemplo, se passa o conteúdo do ficheiro de uma ação de FTP, o payload de contexto aspeto neste exemplo:

![Payload de contexto][2]

> [!NOTE]
> Uma vez que este objecto não foi convertido como uma cadeia, o conteúdo é adicionar diretamente para o payload JSON. No entanto, ocorre um erro se o objeto não é um token JSON (ou seja, uma cadeia ou um JSON/matriz object). Converter o objeto como uma cadeia, adicione as aspas, conforme mostrado na ilustração primeiro neste artigo.
> 

O estruturador de, em seguida, gera um modelo de função que pode criar inline. As variáveis são previamente criadas com base no contexto de que pretende passar para a função.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png
