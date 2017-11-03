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
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.openlocfilehash: 63784c5e3af360b2f3f8cb330a9df8b27a85d859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-pricing-model"></a>Modelo de preços de Aplicações Lógicas
Aplicações lógicas do Azure permite-lhe dimensionar e executar fluxos de trabalho de integração na nuvem.  Seguem-se detalhes sobre a faturação e planos de preços para as Logic Apps.
## <a name="consumption-pricing"></a>Consumo de preços
Recém-criado Logic Apps utilizar um plano de consumo. Com o Logic Apps consumo modelo de preços, apenas paga a utiliza.  As Logic Apps não estão limitadas ao utilizar um plano de consumo.
Todas as ações executadas numa execução de uma instância de aplicação lógica são limitadas.
### <a name="what-are-action-executions"></a>Quais são as execuções de ação?
Cada passo numa definição de aplicação lógica é uma ação, o que inclui acionadores, fluxo de controlo de passos, tal como as condições, os âmbitos, para cada ciclos, fazer até ciclos, chamadas de conectores e chama a ações nativas.
Acionadores são especiais ações que foram concebidas para instanciar uma nova instância de uma aplicação lógica, quando ocorre um evento específico.  Existem diversos comportamentos diferentes para acionadores, que podem afetar a forma como a aplicação lógica está limitada.
* **Acionador da consulta** – este acionador consulta continuamente um ponto final até receber uma mensagem que satisfaça os critérios para criar uma instância de uma aplicação lógica.  O intervalo de consulta pode ser configurado no acionador no Designer de aplicação lógica.  Cada consulta, mesmo não crie uma instância de uma aplicação lógica, contagens de pedido como uma execução de ação.
* **Webhook acionador** – este acionador aguarda que um cliente enviar um pedido de um ponto final específico.  Cada pedido enviado ao ponto final do webhook conta como uma execução de ação. O pedido e o acionador de HTTP Webhook são ambos os acionadores de webhook.
* **Acionador de recorrência** – este acionador cria uma instância da aplicação lógica com base no intervalo de periodicidade configurado no acionador.  Por exemplo, um acionador de recorrência pode ser configurado para ser executado a cada três dias ou mesmo a cada minuto.

Execuções de Acionador podem ser vistas no painel de recursos Logic Apps na parte histórico de Acionador.

Todas as ações que foram executadas, se o fizeram com êxito ou falha são limitados como uma execução de ação.  As ações que foram ignoradas devido a uma condição que não a ser cumprida ou ações que não tenha executar porque a aplicação lógica terminado antes da conclusão não são contabilizadas como execuções de ação.

Ações executadas dentro de ciclos são contabilizadas por iteração do ciclo.  Por exemplo, uma única ação num para cada ciclo repetir uma lista de 10 itens são contadas como o número de itens na lista (10) multiplicado pelo número de ações no ciclo (1) juntamente com um para o início do ciclo , que, neste exemplo, seria (10 * 1) + 1 = 11 execuções de ação.
Desativado Logic Apps não pode ter novas instâncias instanciadas e por isso, ao desativado, não serem cobradas.  Ser mindful que depois de desativar uma aplicação lógica poderá demorar um pequeno tempo para as instâncias para silenciar antes completamente a ser desativada.
### <a name="integration-account-usage"></a>Utilização da conta de integração
Incluídas com base no consumo de utilização é um [conta integração](logic-apps-enterprise-integration-create-integration-account.md) de exploração, desenvolvimento e teste fins, permitindo-lhe utilizar o [B2B/EDI](logic-apps-enterprise-integration-b2b.md) e [processar XML](logic-apps-enterprise-integration-xml.md)funcionalidades das Logic Apps sem custos adicionais. Conseguir criar um máximo de uma conta por região e o arquivo de até 10 contratos e 25 maps. Esquemas, certificados e parceiros têm sem limites e pode carregar tantos conforme necessário.

Para além da inclusão de contas de automatização com consumo, também pode criar contas de automatização padrão sem estes limites e com o nosso SLA de aplicações lógicas padrão. Para obter mais informações, consulte [preços do Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="app-service-plans"></a>Planos do Serviço de Aplicações
As Logic apps criadas anteriormente que referencia um plano do App Service continua se comporte como anteriormente. Consoante o plano escolhido, limitadas após as execuções diárias previstas for excedidas, mas são faturadas com a medição de execução da ação.
Os clientes EA que tenham um plano do App Service na sua subscrição, o que não têm de ser explicitamente associado à aplicação lógica, obter o benefício de quantidades incluídas.  Por exemplo, se tiver um padrão plano do App Service na sua subscrição EA e uma aplicação lógica na mesma subscrição, em seguida, não lhe é cobrada para 10 000 execuções de ação por dia (consulte a tabela seguinte). 

Planos do App Service e as respetivas diária execuções de ação permitidos:
|  | Liberte/partilhado/básica | Standard | Premium |
| --- | --- | --- | --- |
| Execuções de ação por dia |200 |10,000 |50,000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Converter a partir do plano do App Service preços para consumo
Para alterar uma aplicação lógica que tenha um plano do App Service associado a um modelo de consumo, remova a referência para o plano do App Service na definição da aplicação lógica.  Esta alteração pode ser feita com uma chamada para um cmdlet do PowerShell:`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`
## <a name="pricing"></a>Preços
Para detalhes de preços, consulte [Logic Apps preços](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Passos seguintes
* [Uma descrição geral das Logic Apps][whatis]
* [Criar a sua primeira aplicação lógica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

