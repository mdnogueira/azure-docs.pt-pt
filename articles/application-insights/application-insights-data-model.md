---
title: "Modelo de dados de telemetria de informações de aplicação do Azure | Microsoft Docs"
description: "Descrição geral do Application Insights dados modelo"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: b14eea46e773a4b92ba20cd3121cd258f86307c9
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-telemetry-data-model"></a>Modelo de dados de telemetria do Application Insights

[Azure Application Insights](app-insights-overview.md) envia a telemetria da sua aplicação web no portal do Azure, para que possa analisar o desempenho e a utilização da sua aplicação. O modelo de telemetria está padronizado para que seja possível criar a plataforma e independente de idioma de monitorização. 

Dados recolhidos pelo Application Insights modelos deste padrão de execução da aplicação típica:

![Modelo de aplicação do Application Insights](./media/application-insights-data-model/application-insights-data-model.png)

Os seguintes tipos de telemetria são utilizados para monitorizar a execução da sua aplicação. Os seguintes tipos de três são normalmente recolhidos automaticamente pelo Application Insights SDK da estrutura da aplicação web:

* [**Pedido** ](application-insights-data-model-request-telemetry.md) - gerado para iniciar um pedido recebido pela sua aplicação. Por exemplo, web do Application Insights SDK gera automaticamente um item de telemetria de pedido para cada pedido HTTP que recebe a sua aplicação web. 

    Um **operação** é o de threads de execução que processa um pedido. Também pode [escrever código](app-insights-api-custom-events-metrics.md#trackrequest) monitorizar os outros tipos de operação, tal como uma "reativar" numa web da tarefa ou periodicamente a função que processa os dados.  Cada operação tem um ID. Este ID que pode ser utilizado para [grupo](application-insights-correlation.md) toda a telemetria gerada enquanto a aplicação está a processar o pedido. Cada operação ou com êxito ou falha e tem uma duração de tempo.
* [**Exceção** ](application-insights-data-model-exception-telemetry.md) -normalmente, representa uma exceção que faz com que uma operação falhar.
* [**Dependência** ](application-insights-data-model-dependency-telemetry.md) -representa uma chamada a partir da sua aplicação para um serviço externo ou o armazenamento, como uma REST API ou SQL Server. No ASP.NET, chamadas de dependência para o SQL são definidas pelo `System.Data`. Chamadas para pontos finais de HTTP são definidas pelo `System.Net`. 

Application Insights fornece três tipos de dados adicionais para telemetria personalizada:

* [Rastreio](application-insights-data-model-trace-telemetry.md) - utilizado a diretamente ou através de um adaptador de registo de diagnósticos de implementar utilizando uma arquitetura de instrumentação que está familiarizada, tais como `Log4Net` ou `System.Diagnostics`.
* [Evento](application-insights-data-model-event-telemetry.md) - normalmente utilizados para capturar a interação do utilizador com o serviço, para analisar padrões de utilização.
* [Métrica](application-insights-data-model-metric-telemetry.md) - utilizado para valores de escalar periódica de relatórios.

Podem definir todos os itens de telemetria a [informações de contexto](application-insights-data-model-context.md) como id de sessão de utilizador ou a versão de aplicação. O contexto é um conjunto de campos com tipo seguro que unblocks determinados cenários. Quando a versão da aplicação foi inicializada corretamente, o Application Insights pode detetar novos padrões no comportamento correlacionado com a reimplementação da aplicação. Id de sessão pode ser utilizado para calcular a interrupção ou impacto problema nos utilizadores. Calcular a contagem distinta de valores de id de sessão para determinados falhou a dependência, o rastreio de erro ou exceção crítica fornece uma boa compreensão de impacto.

Modelo de telemetria do Application Insights define uma forma de [correlacionar](application-insights-correlation.md) telemetria para a operação do qual faz parte. Por exemplo, um pedido pode solicitar uma base de dados SQL e registada informações de diagnóstico. Pode definir o contexto de correlação para os itens de telemetria que associá-lo novamente para a telemetria de pedido.

## <a name="schema-improvements"></a>Melhoramentos de esquema

Modelo de dados do Application Insights é uma forma simple e básica, mas poderosa para modelar telemetria da sua aplicação. Iremos esforçar-nos manter o modelo simples e slim para suportar cenários essenciais e permitir a expandir o esquema para utilização avançada.

Para comunicar problemas de modelo ou um esquema de dados e sugestões de utilizam o GitHub [ApplicationInsights Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) repositório.

## <a name="next-steps"></a>Passos seguintes

- [Grave a telemetria personalizada](app-insights-api-custom-events-metrics.md)
- Saiba como [expandir e filtrar telemetria](app-insights-api-filtering-sampling.md).
- Utilize [amostragem](app-insights-sampling.md) para minimizar a quantidade de telemetria baseada no modelo de dados.
- Veja [plataformas](app-insights-platforms.md) suportado pelo Application Insights.
