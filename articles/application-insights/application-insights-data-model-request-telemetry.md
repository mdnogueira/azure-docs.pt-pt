---
title: "Modelo de dados de telemetria de informações de aplicação do Azure - pedido de telemetria | Microsoft Docs"
description: Modelo de dados do Application Insights para telemetria de pedido
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
ms.openlocfilehash: 0073f38097ffbebd669754eac5f2d48a620941bf
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="request-telemetry-application-insights-data-model"></a>Pedido de telemetria: modelo de dados do Application Insights

Um item de telemetria de pedido (no [Application Insights](app-insights-overview.md)) representa a sequência de lógica de execução é acionada por um pedido externo à sua aplicação. Cada execução do pedido é identificada por exclusivo `ID` e `url` que contém todos os parâmetros de execução. Pode agrupar pedidos por lógica `name` e definir o `source` deste pedido. A execução do código pode resultar em `success` ou `fail` e tem uma determinada `duration`. Execuções de êxitos e falhas poderão ser agrupadas também pelos `resultCode`. Hora de início para a telemetria de pedido definida no nível do envelope.

Pedido de telemetria suporta o modelo de extensibilidade padrão utilizando personalizada `properties` e `measurements`.

## <a name="name"></a>Nome

Nome do pedido representa o caminho de código executado para processar o pedido. Valor de cardinalidade baixa para permitir que melhor de agrupamento de pedidos. Para os pedidos de HTTP representa o método HTTP e o modelo de caminho de URL, como `GET /values/{id}` sem o real `id` valor.

Web do Application Insights SDK envia o nome do pedido "tal como está" relativamente a maiúsculas e minúsculas de letra. Agrupamento na IU é maiúsculas e minúsculas para `GET /Home/Index` é contabilizado separadamente do `GET /home/INDEX` , apesar de muitas vezes, resultam na mesma execução de controlador e a ação. O motivo que é que os urls estão em geral [maiúsculas e minúsculas](http://www.w3.org/TR/WD-html40-970708/htmlweb.html). Pode querer ver se todas as `404` ocorreu para os urls escritos em maiúsculas. Pode ler mais coleção do nome do pedido pelo SDK Web do ASP.Net no [blogue](http://apmtips.com/blog/2015/02/23/request-name-and-url/).

Comprimento máximo: 1024 carateres

## <a name="id"></a>ID

Identificador de uma instância de chamada de pedido. Utilizada para correlação entre pedido e a outros itens de telemetria. ID deve ser globalmente exclusivo. Para obter mais informações, consulte [correlação](application-insights-correlation.md) página.

Comprimento máximo: 128 carateres

## <a name="url"></a>Url

O URL de pedido com todos os parâmetros de cadeia de consulta.

Comprimento máximo: 2048 carateres

## <a name="source"></a>Origem

Origem do pedido. Os exemplos são a chave de instrumentação do chamador ou o endereço ip do chamador. Para obter mais informações, consulte [correlação](application-insights-correlation.md) página.

Comprimento máximo: 1024 carateres

## <a name="duration"></a>Duração

Duração em formato de pedido: `DD.HH:MM:SS.MMMMMM`. Tem de ser positivo e inferior ao `1000` dias. Este campo é obrigatório conforme pedido telemetria representa a operação com o início e fim.

## <a name="response-code"></a>Código de resposta

Resultado da execução do pedido. Código de estado HTTP para pedidos de HTTP. Poderá ser `HRESULT` tipo de exceção ou valor para outros tipos de pedido.

Comprimento máximo: 1024 carateres

## <a name="success"></a>Êxito

Indicação de chamada com êxito ou sem êxito. Este campo é obrigatório. Quando não definir explicitamente a `false` -pedido considerados seja concluída com êxito. Definir este valor como `false` se a operação foi interrompida por exceção ou devolveu o código de resultado de erro.

Para as aplicações web, o Application Insights definir pedidos como falhada quando o código de resposta for inferior a `400` ou igual a `401`. No entanto existem casos quando desta mapeamento predefinido não corresponde a semântica da aplicação. Código de resposta `404` pode indicar que "não existem registos", que podem fazer parte do fluxo normal. Também pode indicar uma ligação interrompida. Para as ligações quebradas, pode implementar mesmo lógica mais avançada. Pode marcar ligações quebradas como falhas de apenas quando as ligações estão localizadas no mesmo site ao analisar a referência de url. Ou marcá-los como falhas quando acedido a partir de aplicações móveis da empresa. Da mesma forma `301` e `302` indica falha quando acedido a partir do cliente que não suporta o redirecionamento.

Parcialmente aceites conteúdo `206` pode indicar uma falha de um pedido geral. Por exemplo, o ponto final do Application Insights recebe um lote de itens de telemetria como um único pedido. Devolve `206` quando alguns itens no lote não foram processados com êxito. Taxa de aumento de `206` indica um problema que tem de ser investigado. Lógica semelhante aplica-se a `207` multi estado onde o sucesso pode ser o pior de códigos de resposta separado.

Pode ler mais resultados de pedido no código e o código de estado no [blogue](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Passos seguintes

- [Escrever telemetria personalizada do pedido](app-insights-api-custom-events-metrics.md#trackrequest)
- Consulte [modelo de dados](application-insights-data-model.md) para o modelo de tipos e os dados do Application Insights.
- Saiba como [configurar ASP.NET Core](app-insights-asp-net.md) aplicação com o Application Insights.
- Veja [plataformas](app-insights-platforms.md) suportado pelo Application Insights.
