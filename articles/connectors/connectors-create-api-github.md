---
title: Conector de GitHub no Azure Logic Apps | Microsoft Docs
description: "Crie aplicações lógicas com o App service do Azure. GitHub é um repositório de Git baseada na web que aloja o serviço. Oferece todos os a revisão distribuída origem e de controlo de código (SCM) funcionalidade de gestão de Git, bem como adicionar as suas próprias funcionalidades."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: d4614b0ceff0ec0d36dbb1a136551f985f2fc1a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-github-connector"></a>Começar a utilizar o conector do GitHub
GitHub é um repositório de Git baseada na web que aloja o serviço. Oferece todos os a revisão distribuída origem e de controlo de código (SCM) funcionalidade de gestão de Git, bem como adicionar as suas próprias funcionalidades.

Pode começar por criar uma aplicação lógica agora, consulte [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-github"></a>Criar uma ligação ao GitHub
Para criar aplicações lógicas com o GitHub, primeiro tem de criar um **ligação** , em seguida, forneça os detalhes para as seguintes propriedades: 

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| Token |Sim |Forneça as credenciais do GitHub |

Depois de criar a ligação, pode utilizá-lo para executar as ações e escutar os acionadores descritos neste artigo. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/github/).

## <a name="more-connectors"></a>Mais conectores
Volte à [lista APIs](apis-list.md).