---
title: "Preços e faturação - Azure Logic Apps | Microsoft Docs"
description: "Saiba como funciona o preços e faturação para o Azure Logic Apps."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: f2a92e45b8a759d2d8193ac188efdcfc694a3e6d
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="logic-apps-pricing-model"></a>Modelo de preços de Aplicações Lógicas
Pode criar e executar fluxos de trabalho de integração dimensionável automatizados na nuvem com Azure Logic Apps. Seguem-se os detalhes sobre como funcionam os sobre faturação e preços para as Logic Apps.
## <a name="consumption-pricing-model"></a>Modelo de preços de consumo
Com aplicações lógicas criado recentemente, paga apenas o que utiliza. Novas aplicações lógicas utilizam um consumo plano e preços modelo, que significa que todas as execuções efetuadas por uma instância da aplicação lógica são limitadas.
### <a name="what-are-action-executions"></a>Quais são as execuções de ação?
Cada passo numa definição de aplicação lógica é uma ação, o que inclui acionadores, os passos de fluxo de controlo, as chamadas para as ações incorporadas e chamadas para os conectores.
### <a name="triggers"></a>Acionadores
Acionadores são ações especiais que criar uma instância da aplicação lógica, quando ocorre um evento específico. Acionadores tem diversos comportamentos, que afetam a forma como a aplicação lógica está limitada.
* **Acionador da consulta** – este acionador verifica continuamente um ponto final até que obtém uma mensagem que satisfaça os critérios para criar uma instância da aplicação lógica para iniciar o fluxo de trabalho. Pode configurar o intervalo de consulta no acionador através do Designer de aplicação lógica. Contagens de cada pedido de consulta como uma execução, mesmo quando não é criada nenhuma instância de aplicação lógica.
* **Webhook acionador** – este acionador aguarda que um cliente para enviar um pedido para um ponto final específico. Cada pedido enviado ao ponto final do webhook conta como uma execução de ação. Por exemplo, o acionador pedido e de HTTP Webhook são ambos os acionadores de webhook.
* **Acionador de recorrência** – este acionador cria uma instância da aplicação lógica com base no intervalo de periodicidade que configurou no acionador. Por exemplo, pode definir um acionador de recorrência que é executado a cada três dias ou com base numa agenda mais complexa.

Pode encontrar execuções de Acionador no painel de descrição geral da sua aplicação lógica na secção de histórico de Acionador.

### <a name="actions"></a>Ações
As ações incorporadas, por exemplo, as ações que chamam HTTP, as funções do Azure, ou gestão de API, bem como passos de fluxo de controlo, são limitadas como ações nativas e tem os respetivos tipos. Ações que chamam [conectores](https://docs.microsoft.com/connectors) têm o tipo de "ApiConnection". Os conectores estão classificados como conectores standard ou enterprise e são limitados nos seus respetivos [preços][pricing].
Todas as ações com êxito e êxito tenta executar são contadas e limitadas como execuções de ação. No entanto, as ações que são ignoradas, devido a unmet condições ou ações não executadas, porque a aplicação lógica terminado antes da conclusão, não contam como execuções de ação. Aplicações lógicas desativado não é possível instanciar novas instâncias de, pelo que não são cobrados enquanto estão desativadas.

> [!NOTE]
> Depois de desativar uma aplicação lógica, suas instâncias em execução poderão demorar um pouco tempo antes de completamente o interromper.

As ações que executam no interior de ciclos são contadas por cada ciclo em ciclo. Por exemplo, uma única ação num ciclo "para cada" que processa uma lista de item de 10 é contabilizada multiplicando o número de itens de lista (10) pelo número de ações no ciclo (1) juntamente com um para iniciar o ciclo. Por isso, neste exemplo, o cálculo é (10 * 1) + 1, o que resulta em 11 execuções de ação.

### <a name="integration-account-usage"></a>Utilização da conta de integração
Com base no consumo de utilização inclui um [conta integração](logic-apps-enterprise-integration-create-integration-account.md) onde pode explorar, desenvolver e testar o [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e [processar XML](logic-apps-enterprise-integration-xml.md) funcionalidades das Logic Apps não custo adicional. Pode ter uma destas contas integração por região e arquivo até 10 contratos e 25 maps. Pode ter e carregar parceiros ilimitados, esquemas e certificados.

As Logic Apps também oferece contas de automatização básico e padrão com suportados SLA de aplicações lógicas. Pode utilizar contas de automatização básica quando pretender utilizar apenas as mensagens de processamento, ou atuar como um parceiro de pequenas empresas que tenha uma relação de parceiro comercial com uma entidade de negócio maior. Contas de automatização padrão suportam relações de B2B mais complexas e aumentam o número de entidades que pode gerir. Para obter mais informações, consulte [preços do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="pricing"></a>Preços
Para obter mais informações, consulte [Logic Apps preços](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Passos seguintes
* [Uma descrição geral das Logic Apps][whatis]
* [Criar a sua primeira aplicação lógica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

