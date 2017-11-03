---
title: Adicione o conector do Yammer nas suas Azure Logic Apps | Microsoft Docs
description: "Descrição geral do conector do Yammer com parâmetros de REST API"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: c7a213343b4fb2b5a89a5052a459061b404a431c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-yammer-connector"></a>Começar a utilizar o conector do Yammer
Ligue-se ao Yammer para conversações de acesso na sua rede empresarial. Com o Yammer, pode:

* Crie o fluxo de negócio com base nos dados que aproveitar Yammer. 
* Utilize aciona para quando existe uma nova mensagem de um grupo ou um feed do seguinte.
* Utilize ações para publicar uma mensagem, obter todas as mensagens e muito mais. Estas ações obter uma resposta e, em seguida, torne a saída disponível para outras ações. Por exemplo, quando uma nova mensagem for apresentada, pode enviar uma mensagem de e-mail utilizando o Office 365.

Começar através da criação de uma aplicação lógica agora; consulte [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-yammer"></a>Criar uma ligação ao Yammer
Para utilizar o conector do Yammer, tem primeiro de criar um **ligação** , em seguida, forneça os detalhes para estas propriedades: 

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| Token |Sim |Forneça as credenciais do Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/yammer/).

## <a name="more-connectors"></a>Mais conectores
Volte à [lista APIs](apis-list.md).