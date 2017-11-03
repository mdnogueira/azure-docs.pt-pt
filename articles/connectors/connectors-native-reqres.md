---
title: "Utilize ações de pedido e resposta | Microsoft Docs"
description: "Descrição geral do pedido e resposta acionador e ação numa aplicação lógica do Azure"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: e45b07d709927af64cfba28dfb0d8ee9cb8893b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-request-and-response-components"></a>Começar com os componentes de pedido e resposta
Com os componentes de pedido e resposta numa aplicação lógica, pode responder em tempo real a eventos.

Pode, por exemplo:

* Responda a um pedido HTTP com dados de uma base de dados no local através de uma aplicação lógica.
* Acione uma aplicação de lógica de um evento de webhook externo.
* Chame uma aplicação lógica com uma ação de pedido e resposta a partir de outra aplicação de lógica.

Para começar a utilizar as ações de pedido e resposta numa aplicação lógica, consulte o artigo [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-http-request-trigger"></a>Utilize o acionador de pedido de HTTP
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho que está definido uma aplicação lógica. [Saiba mais sobre acionadores](connectors-overview.md).

Eis um exemplo de sequência de como configurar um pedido HTTP no Designer de aplicação lógica.

1. Adicionar o acionador **pedido - pedido de HTTP um quando é recebido** na sua aplicação lógica. Opcionalmente, pode fornecer um esquema JSON (utilizando uma ferramenta como o [JSONSchema.net](http://jsonschema.net)) para o corpo do pedido. Isto permite que o estruturador gerar tokens para propriedades do pedido de HTTP.
2. Adicione outra ação para que o pode guardar a aplicação lógica.
3. Depois de guardar a aplicação lógica, pode obter o URL de pedido HTTP do cartão pedido.
4. Um HTTP POST (pode utilizar uma ferramenta como o [Postman](https://www.getpostman.com/)) para o URL aciona a aplicação lógica.

> [!NOTE]
> Se não definir uma ação de resposta, uma `202 ACCEPTED` resposta imediatamente é devolvida ao autor da chamada. Pode utilizar a ação de resposta para personalizar uma resposta.
> 
> 

![Acionador de resposta](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Utilize a ação de resposta de HTTP
A ação de resposta de HTTP só é válida quando utilizá-lo num fluxo de trabalho que é acionado por um pedido de HTTP. Se não definir uma ação de resposta, uma `202 ACCEPTED` resposta imediatamente é devolvida ao autor da chamada.  Pode adicionar uma ação de resposta em qualquer passo no fluxo de trabalho. A aplicação lógica apenas mantém o pedido de entrada aberta para um minuto para uma resposta.  Após um minuto, se nenhuma resposta foi enviada do fluxo de trabalho (e existe uma ação de resposta na definição do), um `504 GATEWAY TIMEOUT` é devolvida ao autor da chamada.

Eis como adicionar uma ação de resposta de HTTP:

1. Selecione o **novo passo** botão.
2. Escolha **adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **resposta** para listar a ação de resposta.
   
    ![Selecione a ação de resposta](./media/connectors-native-reqres/using-action-1.png)
4. Adicione quaisquer parâmetros que são necessários para a mensagem de resposta HTTP.
   
    ![Concluir a ação de resposta](./media/connectors-native-reqres/using-action-2.png)
5. Clique em do canto superior esquerdo da barra de ferramentas para guardar e a aplicação de lógica irá guardar e publicar (ativar).

## <a name="request-trigger"></a>Pedido de Acionador
Seguem-se os detalhes para o acionador que suporte este conector. Não há um acionador único pedido.

| Acionador | Descrição |
| --- | --- |
| Pedir |Ocorre quando é recebido um pedido de HTTP |

## <a name="response-action"></a>Ação de resposta
Seguem-se os detalhes para a ação que este conector suporta. Há uma ação de resposta única só pode ser utilizada quando acompanhado por um acionador pedido.

| Ação | Descrição |
| --- | --- |
| Resposta |Devolve uma resposta ao pedido de HTTP correlacionado |

### <a name="trigger-and-action-details"></a>Detalhes de trigger e action
As tabelas seguintes descrevem os campos de entrada para o acionador e ação e correspondente Detalhes de saída.

#### <a name="request-trigger"></a>Pedido de Acionador
Segue-se um campo de entrada para o acionador de um pedido HTTP recebido.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Esquema JSON |Esquema |O esquema JSON de corpo do pedido HTTP |

<br>

**Detalhes de saída**

Seguem-se detalhes de resultado para o pedido.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Cabeçalhos |objeto |Cabeçalhos de pedido |
| Corpo |objeto |Objeto de pedido |

#### <a name="response-action"></a>Ação de resposta
Seguem-se os campos de entrada para a ação de resposta de HTTP. A * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Código de estado * |statusCode |O código de estado HTTP |
| Cabeçalhos |Cabeçalhos |Um objeto JSON qualquer de cabeçalhos de resposta incluir |
| Corpo |Corpo |O corpo da resposta |

## <a name="next-steps"></a>Passos seguintes
Agora, experimente a plataforma e [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Pode explorar os outros conectores disponíveis nas logic apps observando nosso [lista APIs](apis-list.md).

