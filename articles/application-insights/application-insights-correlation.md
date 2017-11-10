---
title: "Correlação de telemetria do Azure Application Insights | Microsoft Docs"
description: "Correlação de telemetria do Application Insights"
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
ms.openlocfilehash: e821a640d3d75e712c022bd681eb07b83da91911
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlação de telemetria no Application Insights

No mundo de serviços micro, cada operação lógica requer o trabalho feito na vários componentes do serviço. Cada um destes componentes pode ser monitorizada separadamente pela [Application Insights](app-insights-overview.md). O componente de aplicação web comunica com o componente de fornecedor de autenticação para validar as credenciais de utilizador e com o componente da API para obter dados para visualização. O componente de API na respetiva ative pode consultar dados a partir de outros serviços utilizam componentes de fornecedor de cache e notificar o componente de faturação sobre esta chamada. Correlação de telemetria distribuídas do Application Insights suporta. Permite-lhe detetar que componente é responsável por falhas ou degradação do desempenho.

Este artigo explica o modelo de dados utilizado pelo Application Insights para correlacionar a telemetria enviada pelo vários componentes. Aborda as técnicas de propagação de contexto e os protocolos. Também abrange a implementação dos conceitos de correlação em diferentes idiomas e plataformas.

## <a name="telemetry-correlation-data-model"></a>Modelo de dados de correlação de telemetria

Application Insights define um [modelo de dados](application-insights-data-model.md) para correlação de telemetria distribuída. Para associar o funcionamento lógico telemetria, todos os itens de telemetria tem um campo de contexto chamado `operation_Id`. Este identificador é partilhado por todos os itens de telemetria no rastreio distribuído. Por isso, mesmo com perda de telemetria de uma camada única ainda pode associar telemetria comunicada por outros componentes.

Operação lógica distribuída é composta por, normalmente, um conjunto de operações mais pequena - pedidos processados por um dos componentes. Essas operações são definidas pelo [pedido telemetria](application-insights-data-model-request-telemetry.md). A telemetria de cada pedido tem o seu próprio `id` que o identifica exclusivamente global. E toda a telemetria - rastreios, exceções, etc. associada a este pedido deve definir o `operation_parentId` para o valor do pedido `id`.

Cada operação de envio como uma chamada de http para outro componente representado pelo [telemetria de dependência](application-insights-data-model-dependency-telemetry.md). Telemetria de dependência também define os seus próprios `id` que seja globalmente exclusivo. Telemetria de pedido, iniciada por esta chamada de dependência, utiliza-o como `operation_parentId`.

Pode criar a vista de utilização da operação lógica distribuída `operation_Id`, `operation_parentId`, e `request.id` com `dependency.id`. Os campos também definem a ordem de causality das chamadas de telemetria.

Num ambiente de serviços micro, rastreios de componentes podem ir para o armazenamento diferente. Todos os componentes podem ter a sua própria chave de instrumentação no Application Insights. Para obter telemetria para o funcionamento lógico, tem de consultar os dados do armazenamento de cada. Quando o número de armazenamento for grande, terá de ter uma sugestão sobre onde parecer seguinte.

Modelo de dados define dois campos para resolver este problema do Application Insights: `request.source` e `dependency.target`. O primeiro campo identifica o componente que iniciou o pedido de dependência e o segundo identifica o componente devolvida a resposta da chamada de dependência.


## <a name="example"></a>Exemplo

Vejamos um exemplo de um gráfico de cotações de aplicação os preços que mostra o preço de mercado atual de um gráfico de cotações utilizando a API externa chamada a API de AÇÕES. A aplicação de preços de gráfico de cotações tem uma página `Stock page` aberta utilizando o cliente web browser `GET /Home/Stock`. A aplicação de consulta da API de gráfico de cotações através da utilização de uma chamada HTTP `GET /api/stock/value`.

Pode analisar telemetria resultante execução de uma consulta:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Na nota de vista de resultado que todos os itens de telemetria partilham raiz `operation_Id`. Quando chamada ajax efetuada a partir da página - novo id exclusivo `qJSXU` está atribuída a telemetria de dependência e id do pageView é utilizado como `operation_ParentId`. Por sua vez no pedido do servidor utiliza o id do ajax como `operation_ParentId`, etc.

| ItemType   | nome                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | As cotações página                |              | STYz               | STYz         |
| dependência | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| Pedido    | GET Home/Stock            | KqKwlrSt9PA = | qJSXU              | STYz         |
| dependência | OBTER /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Agora quando a chamada `GET /api/stock/value` efetuadas a um serviço externo que pretende conheçam a identidade do servidor. Para que possa definir `dependency.target` campo adequadamente. Quando o serviço externo não suporta a monitorização - `target` está definido como o nome de anfitrião do serviço como `stock-prices-api.com`. No entanto se esse serviço identifica-se por um predefinidos a devolver o cabeçalho HTTP - `target` contém a identidade de serviço que permite que o Application Insights criar o rastreio distribuído consultando a telemetria de que o serviço. 

## <a name="correlation-headers"></a>Cabeçalhos de correlação

Estamos a trabalhar proposta de RFC para o [correlação protocolo HTTP](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Este proposta define dois cabeçalhos:

- `Request-Id`transportar o id exclusivo global da chamada
- `Correlation-Context`-transportar a coleção de pares de valor de nome das propriedades do rastreio distribuída

A norma também define duas esquemas de `Request-Id` geração - simples e hierárquica. Com o esquema simples, há um conhecidos `Id` chave definida para o `Correlation-Context` coleção.

Application Insights define o [extensão](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) para a protocolo HTTP de correlação. Utiliza `Request-Context` nome pares de valor para propagar a coleção de propriedades utilizadas pelo chamador imediato ou destinatário. Application Insights SDK utiliza este cabeçalho definir `dependency.target` e `request.source` campos.

## <a name="open-tracing-and-application-insights"></a>Rastreio aberto e o Application Insights

[Abra o rastreio](http://opentracing.io/) e o Application Insights procura de modelos de dados 

- `request`, `pageView` mapeia para **Span** com`span.kind = server`
- `dependency`mapeia para **Span** com`span.kind = client`
- `id`de um `request` e `dependency` mapeia para **Span.Id**
- `operation_Id`mapeia para **TraceId**
- `operation_ParentId`mapeia para **referência** do tipo`ChildOf`

Consulte [modelo de dados](application-insights-data-model.md) para o modelo de tipos e os dados do Application Insights.

Consulte [especificação](https://github.com/opentracing/specification/blob/master/specification.md) e [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) para definições de rastreio abra conceitos.


## <a name="telemetry-correlation-in-net"></a>Correlação de telemetria no .NET

Ao longo do tempo .NET definido várias formas para correlacionar os registos de telemetria e de diagnóstico. Não há `System.Diagnostics.CorrelationManager` que permite controlar [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource`e Windows ETW definir o método [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger`utiliza [âmbitos de registo](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). Transmissão WCF e Http cópias de segurança "atual" propagação de contexto.

No entanto os métodos não ative o suporte de rastreio distribuída automática. `DiagnosticsSource`é uma forma para suportar automática cruzada correlação da máquina. Bibliotecas .NET suportam a origem de diagnóstico e permitem automáticas cruzada máquina propagação do contexto de correlação através de transporte, como http.

O [guia para atividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) na origem de diagnóstico explica as noções básicas de actividades de controlo. 

Núcleo de ASP.NET 2.0 suporta a extração dos cabeçalhos de Http e iniciar a nova atividade. 

`System.Net.HttpClient`versão inicial `<fill in>` suporta a inserção automática da correlação cabeçalhos de Http e a chamada de http como uma atividade de controlo.

Há um novo módulo de Http [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) para clássica ASP.NET. Este módulo implementa utilizando DiagnosticsSource de correlação de telemetria. Começa com base nos cabeçalhos de pedido de entrada de atividade. Também está correlacionada com a telemetria de diferentes fases do processamento do pedido. Mesmo para os casos quando cada fase do processamento do IIS é executado num threads gerir diferentes.

Versão inicial do Application Insights SDK `2.4.0-beta1` utiliza DiagnosticsSource e a atividade para recolher telemetria e associá-lo com a atividade atual. 

## <a name="next-steps"></a>Passos seguintes

- [Grave a telemetria personalizada](app-insights-api-custom-events-metrics.md)
- Carregar todos os componentes do seu serviço micro no Application Insights. Veja [plataformas suportadas](app-insights-platforms.md).
- Consulte [modelo de dados](application-insights-data-model.md) para o modelo de tipos e os dados do Application Insights.
- Saiba como [expandir e filtrar telemetria](app-insights-api-filtering-sampling.md).
