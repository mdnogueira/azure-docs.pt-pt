---
title: "Dependência de controlo no Azure Application Insights | Microsoft Docs"
description: "Analise a utilização, a disponibilidade e o desempenho da aplicação Web no local ou do Microsoft Azure com o Application Insights."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: fe02adafbf96df22462683c69813b05c182d3106
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="set-up-application-insights-dependency-tracking"></a>Configurar o Application Insights: controlo de dependência
A *dependência* é um componente externo que é chamado pela sua aplicação. Normalmente, é um serviço chamado utilizando HTTP, ou uma base de dados ou um sistema de ficheiros. [Application Insights](app-insights-overview.md) mede o tempo durante o qual a aplicação aguarda dependências e frequência uma chamada de dependência falha. Pode investigar chamadas específicas e estão relacionadas com pedidos e exceções.

![gráficos de exemplo](./media/app-insights-asp-net-dependencies/10-intro.png)

O monitor de dependência de out of box atualmente relatórios chamadas a estes tipos de dependências:

* Servidor
  * Bases de dados SQL
  * Web do ASP.NET e serviços WCF que utilizam enlaces baseado em HTTP
  * Chamadas HTTP locais ou remotas
  * Azure Cosmos DB, tabela, armazenamento de BLOBs e fila
* Páginas Web
  * Chamadas AJAX

Monitorização funciona utilizando [instrumentação de código de byte](https://msdn.microsoft.com/library/z9z62c29.aspx) à volta de métodos selecionados. Overhead de desempenho é mínimo.

Também pode escrever o seus próprio chamadas SDK para monitorizar outras dependências, ambos no código de cliente e servidor, utilizando o [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency).

## <a name="set-up-dependency-monitoring"></a>Configurar a monitorização de dependência
Informações de dependência parcial são recolhidas automaticamente pelo [Application Insights SDK](app-insights-asp-net.md). Para obter dados completo, instale o agente adequado para o servidor anfitrião.

| Plataforma | Instalar |
| --- | --- |
| Servidor IIS |O [instalar Monitor de estado no seu servidor](app-insights-monitor-performance-live-website-now.md) ou [atualizar a sua aplicação para o .NET framework 4.6 ou posterior](http://go.microsoft.com/fwlink/?LinkId=528259) e instalar o [Application Insights SDK](app-insights-asp-net.md) na sua aplicação. |
| Aplicação Web do Azure |No painel de controlo de aplicação da web, [abrir o painel do Application Insights no seu painel de controlo de aplicação web](app-insights-azure-web-apps.md) e escolha a instalação, se lhe for pedido. |
| Serviço em nuvem do Azure |[Tarefa de arranque de utilização](app-insights-cloudservices.md) ou [framework de instalar o .NET 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Onde encontrar dados de dependência
* [O mapeamento de aplicações](#application-map) visualiza dependências entre a aplicação e componentes neighbouring.
* [Painéis de desempenho, browser e falha](#performance-and-blades) mostram os dados de dependência de servidor.
* [Painel browsers](#ajax-calls) mostra as chamadas AJAX browsers dos seus utilizadores.
* [Clique em através de pedidos lentos ou falhados](#diagnose-slow-requests) verificar a respetiva dependência chamadas.
* [Análise de](#analytics) podem ser utilizadas para consultar dados de dependência.

## <a name="application-map"></a>Mapeamento de Aplicações
O mapeamento de aplicações funciona como um auxiliar visual para detetar as dependências entre os componentes da aplicação. É gerado automaticamente de telemetria a partir da sua aplicação. Este exemplo mostra as chamadas AJAX de scripts do browser e chamadas REST da aplicação de servidor para dois serviços externos.

![Mapeamento de Aplicações](./media/app-insights-asp-net-dependencies/08.png)

* **Navegue das caixas de** relevante dependência e outros gráficos.
* **Afixar o mapa** para o [dashboard](app-insights-dashboards.md), onde ficará totalmente funcional.

[Saiba mais](app-insights-app-map.md).

## <a name="performance-and-failure-blades"></a>Painéis de desempenho e falha
O painel desempenho mostra a duração das chamadas de dependência efetuadas pela aplicação de servidor. Não há um gráfico de resumo e uma tabela segmentada por chamada.

![Gráficos de dependência do painel de desempenho](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

Clique nos gráficos de resumo ou os itens de tabela para procurar ocorrências em bruto destas chamadas.

![Instâncias de chamada de dependência](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

**Contagens de falhas** são apresentadas no **falhas** painel. Uma falha é qualquer código de retorno que não se encontra no intervalo 200-399, ou desconhecido.

> [!NOTE]
> **falhas de 100%?** -Isto provavelmente indica que apenas está a obter dados de dependência parcial. Terá de [configurar monitorização de dependência adequado para a sua plataforma](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Chamadas AJAX
O painel Browsers mostra a taxa de falha e duração de chamadas AJAX de [JavaScript nas suas páginas web](app-insights-javascript.md). Estas são apresentadas como dependências.

## <a name="diagnosis"></a>Diagnosticar pedidos lenta
Cada evento do pedido está associado com as chamadas de dependência, exceções e outros eventos que são controlados enquanto a aplicação está a processar o pedido. Por isso, se algumas pedidos estão a funcionar incorretamente, pode descobrir se é devido a respostas lentas do que uma dependência.

Vamos guiá-lo através de um exemplo desse.

### <a name="tracing-from-requests-to-dependencies"></a>Rastreio de pedidos para dependências
Abra o painel desempenho e observe a grelha de pedidos:

![Lista de pedidos com médias e contagens](./media/app-insights-asp-net-dependencies/02-reqs.png)

Dos principais está a demorar muito tempo. Vamos ver se podemos pode saber em que o tempo é gasto.

Clique nessa linha para ver eventos de pedidos individuais:

![Lista de ocorrências de pedido](./media/app-insights-asp-net-dependencies/03-instances.png)

Clique em qualquer instância de execução longa analisá-lo mais e desloque para baixo para as chamadas de dependência remoto relacionados com este pedido:

![Localizar chamadas para dependências remotas, identifique a duração de atividade invulgar](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Se parece com a maioria do tempo de manutenção que este pedido foi despendido numa chamada para um serviço local.

Selecione essa linha para obter mais informações:

![Clique em através desse dependência remota para identificar o culprit](./media/app-insights-asp-net-dependencies/05-detail.png)

Parece que esta é a onde o problema. Iremos tiver pinpointed o problema, por isso, agora vamos apenas precisa de saber por que motivo chamada de que está a demorar tempo.

### <a name="request-timeline"></a>Linha cronológica de pedido
Caso diferente, não há nenhuma chamada de dependência particularmente longo. Mas, ao mudar para a vista de linha cronológica, é possível ver onde o atraso ocorreu no nosso processamento interno:

![Localizar chamadas para dependências remotas, identifique a duração de atividade invulgar](./media/app-insights-asp-net-dependencies/04-1.png)

Parece haver um intervalo de grande Depois de chamar a dependência primeiro, pelo que deverá ter um aspeto no nosso código por que motivo que se encontra.

### <a name="profile-your-live-site"></a>Perfil do site em direto

Não existem ideia onde o tempo passa? O [gerador de perfis do Application Insights](app-insights-profiler.md) rastreios HTTP chama para o seu site em direto e mostra as funções no código demoraram o tempo mais longo.

## <a name="failed-requests"></a>Pedidos falhados
Também podem ser associados com chamadas falhadas para dependências pedidos falhados. Novamente, iremos pode clicar para identificar o problema.

![Clique em do gráfico de pedidos falhados](./media/app-insights-asp-net-dependencies/06-fail.png)

Clique em através de uma ocorrência de um pedido falhado e observar os eventos associados.

![Clique num tipo de pedido, clique na instância para obter uma vista diferente da mesma instância, clique nela para obter detalhes da exceção.](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Análise
Pode controlar as dependências no [idioma de consulta de análise de registos](https://docs.loganalytics.io/). Eis alguns exemplos.

* Localize quaisquer chamadas de falha de dependência:

```

    dependencies | where success != "True" | take 10
```

* Localize as chamadas AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Localize associadas aos pedidos de chamadas de dependência:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Localizar as chamadas AJAX associadas a vistas de página:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Controlo de dependência personalizada
O módulo de registo de dependência padrão Deteta automaticamente as dependências externas, como bases de dados e REST APIs. Mas é aconselhável que alguns componentes adicionais para ser tratados da mesma forma.

Pode escrever o código que envia as informações de dependência com o mesmo [TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency) que é utilizado por módulos de padrão.

Por exemplo, se criar o seu código com uma assemblagem que não tenha de escrever pessoalmente, foi tempo todas as chamadas, para saber que contribuição faz com que os tempos de resposta. Para que estes dados apresentados nos gráficos de dependência no Application Insights, envia-as utilizando `TrackDependency`.

```C#

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Se pretender desactivar o módulo de registo de dependência padrão, remova a referência DependencyTrackingTelemetryModule no [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Resolução de problemas
*Sucesso de dependência sempre sinalizador mostra true ou false.*

*Consulta SQL não mostrada no completo.*

* Atualizar para a versão mais recente do SDK. Se o .NET versão seja a 4.6 inferior a:
  * Anfitrião do IIS: instalar [Application Insights agente](app-insights-monitor-performance-live-website-now.md) nos servidores de anfitrião.
  * Aplicação web do Azure: abrir o Application Insights separador no painel de controlo de aplicação web em instale o Application Insights.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passos seguintes
* [Exceções](app-insights-asp-net-exceptions.md)
* [Dados de utilizador e página](app-insights-javascript.md)
* [Disponibilidade](app-insights-monitor-web-app-availability.md)
