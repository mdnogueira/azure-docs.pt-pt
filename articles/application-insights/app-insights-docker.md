---
title: "Monitorizar aplicações de Docker no Azure Application Insights | Microsoft Docs"
description: "Contadores de desempenho do docker, eventos e exceções podem ser apresentadas no Application Insights, juntamente com a telemetria das aplicações de."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 68f408f18b767ed9c5aba8fed8c97f021cdeb123
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-docker-applications-in-application-insights"></a>Monitorizar aplicações de Docker no Application Insights
Contadores de desempenho e de eventos de ciclo de vida do [Docker](https://www.docker.com/) contentores podem ser charted no Application Insights. Instalar o [Application Insights](app-insights-overview.md) imagem num contentor no seu anfitrião e irá apresentar os contadores de desempenho para o anfitrião, bem como para as outras imagens.

Com o Docker, distribuir as aplicações nos contentores simples, completas com todas as dependências. Que serão executados em qualquer máquina de anfitrião que executa um motor de Docker.

Quando executa o [imagem do Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) no anfitrião do Docker, receberá destas vantagens:

* Ciclo de vida telemetria sobre todos os contentores em execução no anfitrião - iniciar, parar e assim sucessivamente.
* Contadores de desempenho para todos os contentores. CPU, memória, utilização de rede e muito mais.
* Se lhe [instalado Application Insights SDK para Java](app-insights-java-live.md) as aplicações em execução nos contentores, a telemetria dessas aplicações serão ter propriedades adicionais que identifica a máquina de anfitrião e do contentor. Por isso, por exemplo, se tiver de instâncias de uma aplicação em execução no anfitrião mais do que uma, pode filtrar facilmente a telemetria de aplicações por anfitrião.

![Exemplo](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Configurar o recurso do Application Insights
1. Inicie sessão no [portal do Microsoft Azure](https://azure.com) e abra o recurso do Application Insights para a sua aplicação; ou [criar um novo](app-insights-create-new-resource.md). 
   
    *O recurso devo utilizar?* Se as aplicações que estejam a executar no seu anfitrião foram desenvolvidas por outra pessoa, em seguida, terá de [criar um novo recurso do Application Insights](app-insights-create-new-resource.md). Este é onde pode ver e analisar a telemetria. (Selecione 'Geral' para o tipo de aplicação.)
   
    Mas se tiver o Programador de aplicações, em seguida, esperamos [adicionar Application Insights SDK](app-insights-java-live.md) para cada um deles. Se não estiverem todas as realmente componentes de uma aplicação de negócio único, em seguida, pode configurar todos eles para enviar telemetria para um recurso e que vai utilizar esse mesmo recurso para apresentar os dados de desempenho e o ciclo de vida do Docker. 
   
    Um terceiro cenário é que desenvolvidas a maioria das aplicações, mas estiver a utilizar recursos separados para apresentar os respetivos telemetria. Nesse caso, provavelmente também pretender criar um recurso separado para os dados de Docker. 
2. Adicionar o mosaico de Docker: escolha **adicionar mosaico**, arraste o mosaico de Docker na galeria do e, em seguida, clique em **feito**. 
   
    ![Exemplo](./media/app-insights-docker/03.png)
3. Clique em de **Essentials** pendente e copie a chave de instrumentação. Utilize esta para indicar ao SDK para onde enviar a telemetria.

    ![Exemplo](./media/app-insights-docker/02-props.png)

Manter essa janela do browser à mão, conforme irá voltar a ele em breve para ver a telemetria.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Executar o monitor do Application Insights no seu anfitrião
Agora que já tem algures para apresentar a telemetria, pode configurar a aplicação de que irá recolher e enviá-lo.

1. Ligar ao seu anfitrião de Docker. 
2. Editar a sua chave de instrumentação para este comando e, em seguida, execute:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Apenas uma imagem do Application Insights é necessária por anfitriões de Docker. Se a aplicação é implementada em vários anfitriões de Docker, em seguida, repita o comando em cada anfitrião.

## <a name="update-your-app"></a>Atualizar a sua aplicação
Se a aplicação é equipada com o [Application Insights SDK para Java](app-insights-java-get-started.md), adicione a seguinte linha no ficheiro ApplicationInsights.xml no seu projeto, sob o `<TelemetryInitializers>` elemento:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Esta ação adiciona informações de Docker como contentor e id de anfitrião para todos os itens de telemetria enviado a partir da sua aplicação.

## <a name="view-your-telemetry"></a>Ver a telemetria
Volte para o recurso do Application Insights no portal do Azure.

Clique no mosaico de Docker.

Em breve irá ver os dados que chegam a partir da aplicação do Docker, especialmente se tiver outros contentores em execução no seu motor de Docker.

Seguem-se algumas das vistas que pode obter.

### <a name="perf-counters-by-host-activity-by-image"></a>Contadores de desempenho por anfitrião, atividade por imagem
![Exemplo](./media/app-insights-docker/10.png)

![Exemplo](./media/app-insights-docker/11.png)

Clique em qualquer nome de anfitrião ou de imagem para obter mais detalhes.

Para personalizar a vista, clique em qualquer gráfico, a grelha de cabeçalho, ou utilize Adicionar gráfico. 

[Saiba mais sobre o Explorador de métricas](app-insights-metrics-explorer.md).

### <a name="docker-container-events"></a>Eventos de contentor do docker
![Exemplo](./media/app-insights-docker/13.png)

Investigue os eventos individuais, clique em [pesquisa](app-insights-diagnostic-search.md). Procurar e filtrar para localizar os eventos que pretende. Clique em qualquer evento para obter mais detalhes.

### <a name="exceptions-by-container-name"></a>Exceções por nome de contentor
![Exemplo](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contexto de docker adicionado a telemetria da aplicação
Telemetria de pedido enviada da aplicação equipada com o SDK de AI, enriquecidos com o contexto de Docker:

![Exemplo](./media/app-insights-docker/16.png)

Tempo do processador e contadores de desempenho de memória disponível, enriquecidos e agrupados pelo nome do contentor de Docker:

![Exemplo](./media/app-insights-docker/15.png)

## <a name="q--a"></a>P&R
*O que Application Insights conceder-me que não é possível receber de Docker?*

* Divisão de detalhado de contadores de desempenho por contentor e imagem.
* Integre o contentor e aplicação de dados no dashboard.
* [Exportar a telemetria](app-insights-export-telemetry.md) para análise adicional para uma base de dados, o Power BI ou outro dashboard.

*Como obter telemetria a partir da aplicação em si?*

* Instale o Application Insights SDK na aplicação. Saiba como para: [as web apps Java](app-insights-java-get-started.md), [aplicações web do Windows](app-insights-asp-net.md).

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes

* [Application Insights para Java](app-insights-java-get-started.md)
* [Application Insights para Node.js](app-insights-nodejs.md)
* [Application Insights para ASP.NET](app-insights-asp-net.md)
