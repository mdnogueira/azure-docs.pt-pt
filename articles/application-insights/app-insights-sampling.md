---
title: A amostragem de telemetria no Azure Application Insights | Microsoft Docs
description: Como manter o volume de telemetria sob o controlo.
services: application-insights
documentationcenter: windows
author: vgorbenko
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: mbullwin
ms.openlocfilehash: af184574bdfa7d3a11baf75d8cdfbf80f1544dde
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="sampling-in-application-insights"></a>Amostragem no Application Insights


A amostragem é uma funcionalidade do [Azure Application Insights](app-insights-overview.md). É a forma recomendada para reduzir o tráfego de telemetria e de armazenamento, mantendo sempre uma análise estatisticamente correta de dados da aplicação. O filtro seleciona itens que estão relacionadas, para que possa navegar entre os itens quando estão a fazer as investigações diagnóstico.
Quando as contagens de métricas são apresentadas no portal, estas são renormalized seja a conta de amostragem, a minimizar as consequências nas estatísticas.

A amostragem reduz os custos de tráfego e os dados e ajuda a evitar a limitação.

## <a name="in-brief"></a>Em breve:
* A amostragem mantém 1 na  *n*  regista e elimina o resto. Por exemplo, este poderá manter 1 em 5 eventos, uma frequência de amostragem de 20%. 
* A amostragem ocorre automaticamente se a aplicação envia uma grande quantidade de telemetria, nas aplicações de servidor web ASP.NET.
* Também pode definir amostragem manualmente no portal da página de preços; ou no SDK do ASP.NET no ficheiro. config, também reduzir o tráfego de rede.
* Se o registo de eventos personalizados e pretender certificar-se de que um conjunto de eventos é mantido ou eliminado em conjunto, certifique-se de que têm o mesmo valor de OperationId.
* O divisor amostragem  *n*  é considerado em cada registo na propriedade `itemCount`, que na pesquisa é apresentado sob o nome amigável "contagem de pedido" ou "contagem de eventos". Quando a amostragem não se encontra numa operação, `itemCount==1`.
* Se escrever consultas de análises, deve [tomar conta da amostragem](app-insights-analytics-tour.md#counting-sampled-data). Em particular, em vez de registos de contagem simplesmente, deve utilizar `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Tipos de amostragem
Existem três métodos alternativos de amostragem:

* **Amostragem adaptável** ajusta automaticamente o volume de telemetria enviada do SDK na sua aplicação ASP.NET. Predefinido de 2.0.0-Beta3 v SDK. Acessível apenas a telemetria do lado do servidor ASP.NET. 
* **A amostragem-taxa** reduz o volume de telemetria enviada do seu servidor de ASP.NET e browsers dos seus utilizadores. Definir a velocidade. O cliente e o servidor irão sincronizar os seus amostragem para essa, procura na, possa navegar entre os pedidos e vistas de página relacionados.
* **A amostragem de ingestão** funciona no portal do Azure. Elimina algumas na telemetria o que são recebidos a partir da sua aplicação, uma taxa que definir. Não reduzir o tráfego de telemetria, mas ajuda a manter dentro da sua quota mensal. A grande vantagem de amostragem de ingestão é que pode configurá-lo sem Reimplementar a sua aplicação e uniformemente funciona para todos os servidores e clientes. 

Se forem Adaptive ou fixo amostragem de velocidade numa operação, a amostragem de ingestão está desativada.

## <a name="ingestion-sampling"></a>Amostragem de ingestão
Esta forma de amostragem funciona no ponto onde a telemetria do seu servidor web, os browsers e dispositivos atinge o ponto final de serviço de Application Insights. Apesar de este não reduzir o tráfego de telemetria enviado a partir da sua aplicação, a reduzir a quantidade processados retidos (e cobrada) pelo Application Insights.

Utilize este tipo de amostragem se a sua aplicação, muitas vezes, passa pela respetiva quota mensal e não tem a opção de utilizar um dos tipos de baseado no SDK de amostragem. 

Defina a frequência de amostragem nas Quotas e preços painel:

![No painel de descrição geral de aplicação, clique em definições, quotas, amostras, em seguida, selecione uma frequência de amostragem e clique em Atualizar.](./media/app-insights-sampling/04.png)

Como outros tipos de amostragem, o algoritmo mantém os itens de telemetria relacionados. Por exemplo, quando está a inspecionar a telemetria na pesquisa, poderá encontrar o pedido relacionadas com uma exceção específica. Métrica contagens, tais como a taxa de pedidos e velocidade de exceção corretamente são retidos.

Pontos de dados que são rejeitados pelo amostragem não estão disponíveis em qualquer funcionalidade do Application Insights, tais como [exportação contínua](app-insights-export-telemetry.md).

A amostragem de ingestão não funciona enquanto amostragem adaptável ou -taxa com base no SDK está na operação. Tenha em atenção que a amostragem adaptável está ativada por predefinição quando o ASP.NET SDK está ativado no Visual Studio ou utilizando o Monitor de estado e amostragem da ingestão está desativada. Se a frequência de amostragem, o SDK é inferior a 100%, em seguida, a frequência de amostragem de ingestão que definir é ignorada.

> [!WARNING]
> O valor mostrado no mosaico indica o valor que definiu para a amostragem da ingestão. -Não representa a frequência de amostragem real amostragem SDK esteja na operação.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Amostragem adaptável no seu servidor web
Amostragem adaptável está disponível para o Application Insights SDK para 2.0.0-Beta3 do ASP.NET v e posterior e está ativada por predefinição. 

Amostragem adaptável afeta o volume de telemetria enviada a partir da sua aplicação de servidor web para o serviço do Application Insights. O volume é ajustado automaticamente para manter dentro de uma velocidade máxima especificada de tráfego.

Este não funciona em volumes baixos de telemetria, por isso, uma aplicação de depuração ou um Web site com baixa utilização não será afetado.

Para alcançar o volume de destino, algumas da telemetria gerada rejeitados. Mas como outros tipos de amostragem, o algoritmo mantém os itens de telemetria relacionados. Por exemplo, quando está a inspecionar a telemetria na pesquisa, poderá encontrar o pedido relacionadas com uma exceção específica. 

Métrica contagens, tais como a taxa de pedidos e velocidade de exceção são ajustado para compensar a frequência de amostragem, para que mostram, aproximadamente, os valores corretos no Explorador de métrica.

**Atualizar o NuGet seu projeto** pacotes para a versão mais recente *pré-lançamento* versão do Application Insights: clique com o botão direito no projeto no Explorador de soluções, selecione gerir pacotes NuGet, verifique **incluir pré-lançamento** e procure applicationinsights. 

No [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md), pode ajustar vários parâmetros no `AdaptiveSamplingTelemetryProcessor` nós. Os valores apresentados são os valores predefinidos:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    A velocidade de destino que o algoritmo adaptável visa para **em cada anfitrião do servidor**. Se a sua aplicação web é executada em vários anfitriões, reduza este valor, de modo a manter-se dentro da taxa de destino do tráfego no portal do Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervalo em que a taxa actual de telemetria é reavaliada. Avaliação é executada como uma média móvel. Pode querer Encurte este intervalo esteja responsabilizada para bursts repentino sua telemetria.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Quando as alterações do valor de percentagem de amostragem, como logo após são iremos permitidos para reduzir a percentagem de amostragem novamente para capturar dados menos.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Quando as alterações do valor de percentagem de amostragem, como logo após são iremos permitidos para aumentar a percentagem de amostragem novamente para capturar os dados mais.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Como percentagem de amostragem varia, o que é o valor mínimo que estão autorizados a definir.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Como percentagem de amostragem varia, o que é o valor máximo que estão autorizados a definir.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    No cálculo da média móvel, a ponderação atribuída para o valor mais recente. Utilize um valor igual ou inferior a 1. Os valores mais pequenos alterá o algoritmo menos reativa para repentino.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    O valor atribuído quando a aplicação apenas foi iniciado. Não reduza este enquanto estiver a depuração. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Lista de tipos que não pretende ser objeto de amostragem delimitada por ponto e vírgula. Reconhecido tipos são: dependência, eventos, exceções, PageView, pedido, rastreio. Todas as instâncias dos tipos especificados são transmitidas; os tipos que não foram especificados são objeto de amostragem.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Lista de tipos que pretende ser objeto de amostragem delimitada por ponto e vírgula. Reconhecido tipos são: dependência, eventos, exceções, PageView, pedido, rastreio. Os tipos de especificado servem; todas as instâncias dos outros tipos sempre serão transmitidas.


**Para desactivar** adaptável de amostragem, remove o nó de AdaptiveSamplingTelemetryProcessor applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Em alternativa: Configurar amostragem adaptável no código
Em vez de ajustar a amostragem no ficheiro. config, pode utilizar o código. Isto permite-lhe especificar uma função de chamada de retorno que é invocada sempre que a frequência de amostragem é reavaliada. Pode utilizar, por exemplo, para saber que frequência de amostragem está a ser utilizada.

Remova o `AdaptiveSamplingTelemetryProcessor` nó a partir do ficheiro. config.

*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Saiba mais sobre os processadores de telemetria](app-insights-api-filtering-sampling.md#filtering).)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>Amostragem para páginas web com JavaScript
Pode configurar páginas web para a amostragem-taxa de qualquer servidor. 

Quando lhe [configurar as páginas web para o Application Insights](app-insights-javascript.md), modificar o fragmento que obtém a partir do portal do Application Insights. (Em aplicações do ASP.NET, o fragmento normalmente passa em layout.)  Inserir uma linha como `samplingPercentage: 10,` antes da chave de instrumentação:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Para a percentagem de amostragem, escolha uma percentagem que está próximo 100/N em que N é um número inteiro.  Amostragem atualmente não suporta outros valores.

Se também ativar a amostragem-taxa no servidor, os clientes e o servidor irão sincronizar, de modo que, de pesquisa, possa navegar entre os pedidos e vistas de página relacionados.

## <a name="fixed-rate-sampling-for-aspnet-web-sites"></a>-Taxa amostragem para ASP.NET web sites
Taxa de fixa amostragem reduz o tráfego enviado a partir do seu servidor web e browsers da web. Ao contrário de amostragem adaptável, reduz a telemetria uma taxa fixo optado por si. Também sincroniza, o cliente e a amostragem de servidor para que os itens relacionados são mantidos - por exemplo, para que o se observar uma vista de página na pesquisa, pode encontrar o pedido relacionado.

O algoritmo de amostragem mantém itens relacionados. Para cada pedido HTTP evento e os eventos relacionados são eliminados ou transmitidos. 

No Explorador de métricas, taxas como contagens de pedidos e exceções são multiplicadas por um fator para compensar a frequência de amostragem, para que fiquem aproximadamente corretos.

1. **Atualizar pacotes de NuGet do seu projeto** para o versão mais recente *pré-lançamento* versão do Application Insights. Clique com o botão direito no projeto no Explorador de soluções, selecione gerir pacotes NuGet, verifique **incluir pré-lançamento** e procure applicationinsights. 
2. **Desativar a amostragem adaptável**: no [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md), remover ou comente a `AdaptiveSamplingTelemetryProcessor` nós.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

1. **Ative o módulo de amostragem-taxa.** Adicionar este fragmento [Applicationinsights](app-insights-configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

> [!NOTE]
> Para a percentagem de amostragem, escolha uma percentagem que está próximo 100/N em que N é um número inteiro.  Amostragem atualmente não suporta outros valores.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativa: Ativar amostragem-taxa no seu código de servidor
Em vez de definição do parâmetro de amostragem no ficheiro. config, pode utilizar o código. 

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Saiba mais sobre os processadores de telemetria](app-insights-api-filtering-sampling.md#filtering).)

## <a name="when-to-use-sampling"></a>Quando utilizar amostragem?
Amostragem adaptável é ativada automaticamente se utilizar o ASP.NET SDK versão 2.0.0-Beta3 ou posterior. Independentemente da que versão SDK utilizada, pode utilizar a amostragem de ingestão (no nosso servidor).

Não precisa de amostragem para a maioria das aplicações de tamanho de pequenas e médias. As informações de diagnóstico mais úteis e as estatísticas mais exatas são obtidas através da recolha de dados em todas as atividades do utilizador. 

São as principais vantagens da amostragem:

* Application Insights service remoções ("Limitações") pontos de dados quando a aplicação envia uma taxa muito elevada de telemetria resumindo intervalo de tempo. 
* Para manter dentro de [quota](app-insights-pricing.md) de pontos de dados para o escalão de preço. 
* Para reduzir o tráfego de rede da coleção de telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Que tipo de amostragem devo utilizar?
**Utilize a ingestão de amostragem se:**

* Muitas vezes, pode aceder através da sua quota mensal de telemetria.
* Está a utilizar uma versão do SDK que não suporta a amostragem - por exemplo, o SDK de Java ou ASP.NET versões anteriores ao 2.
* A obter uma grande quantidade de telemetria de browsers da web dos seus utilizadores.

**Utilize-taxa amostragem se:**

* Está a utilizar o Application Insights SDK para a versão de serviços web ASP.NET 2.0.0 ou posterior, e
* Pretende amostragem sincronizados entre cliente e servidor, para que, quando estiver a investigar eventos [pesquisa](app-insights-diagnostic-search.md), pode navegar entre os eventos relacionados no cliente e servidor, tais como as vistas de página e pedidos de http.
* Tem a certeza da percentagem de amostragem adequado para a sua aplicação. Deve ser suficientemente alto para obter uma métrica precisa, mas abaixo a velocidade a que excede a quota de preço e os limites de limitação. 

**Utilize a amostragem adaptável:**

Caso contrário, é recomendável amostragem adaptável. Isto é ativado por predefinição no servidor de ASP.NET SDK versão 2.0.0-Beta3 ou posterior. -Não reduzir o tráfego até que um determinada taxa mínima, pelo que não afetam a um site de baixa utilização.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Como saber se amostragem está na operação?
Para detetar a frequência de amostragem real, independentemente de onde foi aplicado, utilize um [consulta de análise](app-insights-analytics.md) como esta:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Em cada retidos registo, `itemCount` indica o número de registos originais que representa, igual a 1 + o número de registos rejeitados anteriores. 

## <a name="how-does-sampling-work"></a>Como funciona a amostragem?
Taxa de fixo e amostragem adaptável são uma funcionalidade do SDK em versões do ASP.NET de 2.0.0 em diante. A amostragem de ingestão é uma funcionalidade do serviço Application Insights e pode ser numa operação, se o SDK não está a executar a amostragem. 

O algoritmo de amostragem decide os itens de telemetria ao largar e aqueles manter (se está no SDK ou no serviço do Application Insights). A decisão de amostragem baseia-se em várias regras que pretende manter todos os pontos de dados interrelated intactas, manter uma experiência de diagnóstico no Application Insights, que é acionável e fiável do mesmo com um conjunto de dados reduzido. Por exemplo, se de um pedido falhado a aplicação envia itens de telemetria adicionais (como exceções e rastreios com sessão iniciados neste pedido), amostragem não dividir este pedido e outra telemetria. -O mantém ou ignora-las a todos os em conjunto. Como resultado, quando observar os detalhes do pedido no Application Insights, pode ver sempre o pedido, juntamente com os seus itens de telemetria associados. 

Para aplicações que definem "utilizador" (ou seja, mais comuns aplicações web), a decisão de amostragem baseiam o hash do id de utilizador, o que significa que toda a telemetria para qualquer utilizador específico é preservada ou removida. Para os tipos de aplicações que não definem utilizadores (tais como serviços web) a amostragem decisão baseia-se o ID de operação do pedido. Por fim, para os itens de telemetria que não tenham o id de utilizador nem operação definido (para itens de telemetria de exemplo comunicadas a partir do threads assíncronas com nenhum contexto de http) amostragem simplesmente capturas de percentagem de itens de telemetria de cada tipo. 

Quando a apresentação de telemetria para o serviço do Application Insights ajusta as métricas pela percentagem de amostragem mesmo que foi utilizada no momento da coleção, para compensar para os pontos de dados em falta. Por conseguinte, quando observar a telemetria no Application Insights, os utilizadores são ver approximations estatisticamente corretos que são muito próximo os números de real.

A precisão da aproximação do seu depende em grande medida a percentagem de amostragem configurado. Além disso, a precisão aumenta para aplicações que processam um grande volume de pedidos, geralmente, semelhantes de muitos utilizadores. Por outro lado, para aplicações que não funcionam com uma significativa de carga, amostragem não é necessária como estas aplicações, normalmente, podem enviar os respetivos telemetria permanecendo dentro da quota, sem causar a perda de dados de limitação. 

Note que o Application Insights não de exemplo tipos de telemetria de métricas e sessões, desde para estes tipos, redução da precisão pode ser altamente indesejável. 

### <a name="adaptive-sampling"></a>Amostragem adaptável
Amostragem adaptável adiciona um componente que monitoriza a taxa actual de transmissão do SDK e ajusta a percentagem de amostragem para tentar permanecer na velocidade máxima de destino. O ajuste efetuado é recalculado a intervalos regulares e baseia-se uma média móvel de taxa de transmissão de saída.

## <a name="sampling-and-the-javascript-sdk"></a>A amostragem e o SDK de JavaScript
O lado do cliente (JavaScript) SDK participa numa taxa fixo amostragem em conjunto com o SDK do lado do servidor. As páginas instrumented só irão enviar telemetria do lado do cliente dos mesmos utilizadores de que o lado do servidor efetuou a decisão de "exemplo no". Esta lógica foi concebida para manter a integridade da sessão de utilizador em lados cliente e servidor. Como resultado, a partir de qualquer item específico telemetria no Application Insights pode encontrar todos os outros itens de telemetria para este utilizador ou a sessão. 

*A minha cliente e a telemetria do lado do servidor não apresentam exemplos coordenados como descrito acima.*

* Certifique-se de que ativou a amostragem-taxa no servidor e cliente.
* Certifique-se de que a versão do SDK 2.0 ou posterior.
* Certifique-se de que definiu a percentagem de amostragem mesmo no cliente e servidor.

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes
*Razão pela qual não é amostragem um simples "recolher X por cento de cada tipo de telemetria"?*

* Enquanto esta abordagem de amostragem iria fornecer com uma precisão muito elevada na approximations métricas, seria interromper a capacidade para correlacionar dados de diagnóstico por utilizador, a sessão e a pedido, o que é fundamental para obter um diagnóstico. Por conseguinte, fazendo a amostragem funciona melhor com "recolher todos os telemetria itens para X percentagem de utilizadores de aplicação" ou "recolher toda a telemetria X por cento dos pedidos de aplicação" lógica. Os itens de telemetria não associados os pedidos (por exemplo, o processamento assíncrono em segundo plano), volta a reversão é "recolher X percentagem de todos os itens para cada tipo de telemetria." 

*A percentagem de amostragem pode alterar ao longo do tempo?*

* Sim, amostragem adaptável alterações gradualmente a percentagem de amostragem, com base no volume de telemetria a atualmente observado.

*Se utilizar a amostragem-taxa, como posso saber que amostragem percentagem funcionarão melhor para a minha aplicação?*

* Uma forma é amostragem adaptável para começar, descobrir que classificar assenta (consulte a questão acima) e, em seguida, mude para-taxa amostragem com essa taxa. 
  
    Caso contrário, terá de adivinhar. Analisar a utilização atual de telemetria no AI, observar qualquer limitação que está a ocorrer e estimar o volume de telemetria a recolhidos. Estas três entradas, juntamente com o escalão de preço selecionado, sugerimos quanto poderá reduzir o volume de telemetria a recolhidos. No entanto, um aumento no número de utilizadores ou alguns outro shift no volume de telemetria pode invalidar a estimativa.

*O que acontece se configurar a percentagem de amostragem demasiado baixa?*

* Percentagem de amostragem excessivamente baixa (amostragem over-aggressive) reduz a precisão do approximations, quando tenta Application Insights compensar a visualização dos dados para a redução do volume de dados. Além disso, experiência de diagnóstico pode ser afetada negativamente e como alguns dos pedidos com pouca frequência falhar ou lentos poderão ser utilizados como saída.

*O que acontece se configurar a percentagem de amostragem demasiado elevada?*

* Configurar a percentagem de amostragem demasiado elevada (não agressiva suficientemente) resulta numa redução insuficiente no volume de telemetria a recolhidos. Ainda pode ocorrer perda de dados de telemetria relacionadas com a limitação e o custo de utilização do Application Insights pode ser superior a que planeada devido a custos de excedidas.

*Em que plataformas posso utilizar amostragem?*

* A amostragem de ingestão situação pode ocorrer automaticamente a qualquer telemetria acima de um determinado volume, se o SDK não está a executar a amostragem. Isto funciona, por exemplo, se a sua aplicação utiliza um servidor de Java ou se estiver a utilizar uma versão mais antiga do SDK do ASP.NET.
* Se estiver a utilizar versões do ASP.NET SDK 2.0.0 e acima (alojada no Azure ou no seu próprio servidor), que obtém adaptável de amostragem por predefinição, mas pode mudar para-taxa conforme descrito acima. Com a amostragem de taxa corrigido, o browser SDK sincroniza automaticamente para a amostragem eventos relacionados. 

*Existem determinados eventos raros que sempre quero ver. Como posso obtê-los passado o módulo de amostragem?*

* Inicializar uma instância separada do TelemetryClient com um novo TelemetryConfiguration (não o predefinido um Active Directory). Utilize-a para enviar os eventos raros.

## <a name="next-steps"></a>Passos seguintes
* [Filtragem](app-insights-api-filtering-sampling.md) pode fornecer controlo strict mais do que o SDK envia.

