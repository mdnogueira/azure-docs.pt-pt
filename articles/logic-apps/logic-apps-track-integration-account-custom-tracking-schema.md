---
title: "Esquemas de controlo personalizado para a monitorização do B2B - Azure Logic Apps | Microsoft Docs"
description: "Crie esquemas de controlo personalizado para monitorizar as mensagens B2B de transações na sua conta de integração do Azure."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b71a4938dde2a71f1ce29403af7aa9101358d64c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-tracking-to-monitor-your-complete-workflow-end-to-end"></a>Ativar o controlo monitorizar o seu fluxo de trabalho completado, ponto a ponto
Não há incorporadas que pode ativar para diferentes partes da sua empresa-empresa de fluxo de trabalho, tais como controlo AS2 ou X12 mensagens de controlo. Quando criar fluxos de trabalho que inclui uma aplicação lógica, BizTalk Server, SQL Server ou qualquer outra camada, em seguida, pode ativar o controlo personalizado que os registos de eventos a partir do início ao fim do fluxo de trabalho. 

Este tópico fornece código personalizado que pode utilizar as camadas fora da sua aplicação lógica. 

## <a name="custom-tracking-schema"></a>Esquema de controlo personalizado
````java

        {
            "sourceType": "",
            "source": {

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| SourceType |   | Tipo da origem de execução. Valores permitidos são **Microsoft.Logic/workflows** e **personalizado**. (Obrigatório) |
| Origem |   | Se o tipo de origem for **Microsoft.Logic/workflows**, as informações de origem tem de seguir neste esquema. Se o tipo de origem for **personalizado**, o esquema é um JToken. (Obrigatório) |
| systemId | Cadeia | ID de sistema da aplicação lógica. (Obrigatório) |
| runId | Cadeia | Aplicação de lógica executar ID. (Obrigatório) |
| operationName | Cadeia | Nome da operação (por exemplo, ação ou acionador). (Obrigatório) |
| repeatItemScopeName | Cadeia | Repita o nome do item se a ação está dentro de um `foreach` / `until` ciclo. (Obrigatório) |
| repeatItemIndex | Número inteiro | Se a ação está dentro de um `foreach` / `until` ciclo. Indica o índice do item repetido. (Obrigatório) |
| trackingId | Cadeia | ID de controlo, para correlacionar as mensagens. (Opcional) |
| correlationId | Cadeia | ID de correlação para correlacionar as mensagens. (Opcional) |
| clientRequestId | Cadeia | Cliente pode preenchê-lo para correlacionar mensagens. (Opcional) |
| eventLevel |   | Nível do evento. (Obrigatório) |
| eventTime |   | Hora do evento, em formato UTC aaaa-MM-DDTHH:MM:SS.00000Z. (Obrigatório) |
| recordType |   | Tipo de registo de controlar. Permitido é de valor **personalizado**. (Obrigatório) |
| registo |   | Tipo de registo personalizado. O formato permitido é JToken. (Obrigatório) |

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de controlo de protocolo de B2B
Para obter informações sobre o protocolo de B2B esquemas de controlo, consulte:
* [Esquemas de controlo de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Esquemas de controlo de X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [monitorização mensagens B2B](logic-apps-monitor-b2b-message.md).   
* Saiba mais sobre [controlo mensagens B2B no portal do Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Saiba mais sobre o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
