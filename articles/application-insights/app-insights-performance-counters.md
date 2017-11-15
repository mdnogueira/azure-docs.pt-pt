---
title: Contadores de desempenho no Application Insights | Microsoft Docs
description: Monitor de sistema e contadores de desempenho do .NET personalizados no Application Insights.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: mbullwin
ms.openlocfilehash: 40821d32c5bdfe51bb3cb205660d6f25b2c3fadc
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de desempenho do sistema no Application Insights
O Windows fornece uma ampla variedade de [contadores de desempenho](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters) , tais como a ocupação da CPU, memória, disco e utilização de rede. Também pode definir os seus próprios. [Application Insights](app-insights-overview.md) pode mostrar estes contadores de desempenho se a aplicação está a ser executada sob o IIS num anfitrião no local ou máquina virtual para os quais têm acesso administrativo. Os gráficos indicam os recursos disponíveis para a aplicação em direto e podem ajudar a identificar desequilibrada carga entre instâncias de servidor.

Contadores de desempenho são apresentados no painel de servidores, o que inclui uma tabela que segmentos por instância de servidor.

![Contadores de desempenho comunicados no Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

(Os contadores de desempenho não estão disponíveis para Web Apps do Azure. Mas pode [enviar diagnósticos do Azure ao Application Insights](app-insights-azure-diagnostics.md).)

## <a name="view-counters"></a>Contadores de vista
O painel servidores mostra um conjunto predefinido de contadores de desempenho. 

Para ver outros contadores, editar os gráficos no painel de servidores ou abra uma nova [Explorador de métricas](app-insights-metrics-explorer.md) painel e adicione gráficos de novo. 

Os contadores disponíveis são listados como métricas quando edita um gráfico.

![Contadores de desempenho comunicados no Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Para ver todos os seus gráficos mais útil num único lugar, crie um [dashboard](app-insights-dashboards.md) e afixar ao mesmo.

## <a name="add-counters"></a>Adicionar contadores
Se o contador de desempenho que pretende que não é apresentado na lista de métricas, isto acontece porque o Application Insights SDK não está recolhê-lo no seu servidor web. Pode configurar para fazê-lo.

1. Saber que contadores estão disponíveis no seu servidor utilizando este comando PowerShell no servidor:
   
    `Get-Counter -ListSet *`
   
    (Consulte [ `Get-Counter` ](https://technet.microsoft.com/library/hh849685.aspx).)
2. Abra Applicationinsights.
   
   * Se tiver adicionado o Application Insights à sua aplicação durante o desenvolvimento, edite Applicationinsights no seu projeto e, em seguida, implementá-la novamente para os seus servidores.
   * Se utilizou o Monitor de estado para instrumentar uma aplicação web no tempo de execução, localize Applicationinsights no diretório de raiz da aplicação no IIS. A atualização não existe em cada instância de servidor.
3. Edite a diretiva de recoletor de desempenho:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Pode capturar contadores padrão tanto os que implementar por si. `\Objects\Processes`Se um exemplo de um contador padrão, disponível em todos os sistemas Windows. `\Sales(photo)\# Items Sold`Se um exemplo de um contador personalizado que pode ser implementado num serviço web. 

O formato é `\Category(instance)\Counter"`, ou para as categorias que não têm instâncias, apenas `\Category\Counter`.

`ReportAs`é necessário para nomes de contador que não correspondam a `[a-zA-Z()/-_ \.]+` -ou seja, que contêm carateres que não estão a ser os seguintes conjuntos: letras, arredondar Parênteses Retos, barra, hífen, um caráter de sublinhado, espaço, ponto.

Se especificar uma instância, serão recolhido como dimensão de "CounterInstanceName" da métrica comunicada.

### <a name="collecting-performance-counters-in-code"></a>Recolha de contadores de desempenho no código
Para recolher contadores de desempenho do sistema e enviá-los para o Application Insights, pode adaptar o fragmento abaixo:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
Ou pode fazê-lo a mesma coisa com métricas personalizadas que criou:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Contadores de desempenho análise
Pode procurar e apresentar relatórios de contador de desempenho no [análise](app-insights-analytics.md).

O **performanceCounters** esquema expõe o `category`, `counter` nome, e `instance` nome de cada contador de desempenho.  A telemetria para cada aplicação, irá ver apenas os contadores para essa aplicação. Por exemplo, para ver contadores de que estão disponíveis: 

![Contadores de desempenho na análise do Application Insights](./media/app-insights-performance-counters/analytics-performance-counters.png)

(Aqui 'Instância' refere-se para a instância do contador de desempenho, não a instância da máquina de função ou servidor. O nome de instância do contador de desempenho normalmente segmenta os contadores, tais como o tempo de processador pelo nome da aplicação ou processo.)

Para obter um gráfico de memória disponível durante o período recente: 

![Memória timechart na análise do Application Insights](./media/app-insights-performance-counters/analytics-available-memory.png)

Como outra telemetria, **performanceCounters** também tem uma coluna `cloud_RoleInstance` indica que a identidade de instância do servidor anfitrião no qual a aplicação está em execução. Por exemplo, para comparar o desempenho da sua aplicação das diferentes máquinas: 

![Desempenho segmentados por instância de função no Application Insights analytics](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET e contagens de Application Insights
*O que é a diferença entre a taxa de exceções e métricas de exceções?*

* *Taxa de exceção* é um contador de desempenho do sistema. O CLR contagens de todas as exceções de processadas e não processadas que estão a ser emitidas e divide o total num intervalo de amostragem pelo comprimento do intervalo. O Application Insights SDK recolhe este resultado e envia-a para o portal.
* *Exceções* é uma contagem dos relatórios TrackException recebida pelo portal no intervalo de amostragem do gráfico. Inclui apenas as exceções processadas nos quais tem escritos TrackException chama no seu código e não inclui todos os [não processada exceções](app-insights-asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Contadores de desempenho em aplicações do Asp.Net Core
Contadores de desempenho apenas são suportados se a aplicação está direcionado para o .NET Framework completa. Não há nenhuma capacidade de recolher contadores de desempenho para o .net Core aplicações.

## <a name="alerts"></a>Alertas
Como outras métricas, pode [definir um alerta](app-insights-alerts.md) para avisar o se um contador de desempenho fica fora de um limite que especificar. Abra o painel de alertas e clique em Adicionar alerta.

## <a name="next"></a>Passos seguintes
* [Controlo de dependência](app-insights-asp-net-dependencies.md)
* [Controlo de exceções](app-insights-asp-net-exceptions.md)

