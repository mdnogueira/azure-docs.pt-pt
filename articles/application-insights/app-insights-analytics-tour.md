---
title: "A introdução através de análise no Azure Application Insights | Microsoft Docs"
description: "Curtas amostras de todas as consultas principais na análise, a ferramenta poderosa pesquisa do Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/06/2017
ms.author: mbullwin
ms.openlocfilehash: 26a5854735bd197fb114fce409a093251dc5c2f0
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="a-tour-of-analytics-in-application-insights"></a>A introdução de análise no Application Insights
[Análise de](app-insights-analytics.md) é a funcionalidade de pesquisa poderoso de [Application Insights](app-insights-overview.md). Estas páginas descrevem o idioma de consulta de análise de registos.

* **[Veja o vídeo introdutórias](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Experimente a análise nos nossos dados simulados](https://analytics.applicationinsights.io/demo)**  se a aplicação não está a enviar dados para o Application Insights ainda.
* **[SQL Server-dos utilizadores cheat folha](https://aka.ms/sql-analytics)**  traduz as idioms mais comuns.

Vamos uma movimentação pela através de algumas consultas básicas para ajudar a começar.

## <a name="connect-to-your-application-insights-data"></a>Ligar aos seus dados do Application Insights
Abra a análise da sua aplicação [painel Descrição geral](app-insights-dashboards.md) no Application Insights:

![Abrir portal.azure.com, abra o recurso do Application Insights e clique em análise.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsioquerylanguagequerylanguagetakeoperatorhtml-show-me-n-rows"></a>[Tirar](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html): Mostrar linhas n
Pontos de dados que o registo de operações do utilizador (normalmente, os pedidos de HTTP recebidos pela sua aplicação web) estão armazenados numa tabela chamada `requests`. Cada linha é um ponto de dados de telemetria recebido do Application Insights SDK na sua aplicação.

Vamos começar por examinar algumas linhas de exemplo da tabela:

![resultados](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Coloque o cursor algures na instrução antes de clicar em Ir. Pode dividir uma instrução através de mais do que uma linha, mas não colocar linhas em branco numa instrução. Linhas em branco são uma maneira conveniente para manter várias consultas separadas na janela.
>
>

Escolha as colunas, arraste-os, grupo por colunas e filtrar:

![Clique em colunas de seleção no canto superior direito de resultados](./media/app-insights-analytics-tour/030.png)

Expanda qualquer item para ver os detalhes:

![Escolha a tabela e utilize configurar colunas](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Clique no cabeçalho da coluna para reordenar os resultados disponíveis no browser. Mas tenha em atenção que para um conjunto de resultados grande, o número de linhas transferido para o browser é limitado. Desta forma a ordenação não sempre mostra-lhe os itens de mais ou menor reais. Para ordenar itens de forma fiável, utilize o `top` ou `sort` operador.
>
>

## <a name="query-across-applications"></a>Consulta em todas as aplicações
Se pretender combinar dados de várias aplicações do Application Insights, utilize o **aplicação** palavra-chave para especificar a aplicação juntamente com o nome da tabela.  Esta consulta combina os pedidos de duas aplicações diferentes com o **union** comando.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsioquerylanguagequerylanguagetopoperatorhtml-and-sorthttpsdocsloganalyticsioquerylanguagequerylanguagesortoperatorhtml"></a>[Parte superior](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) e [ordenação](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)
`take`é útil para obter um exemplo rápido de resultado, mas as linhas da tabela mostra nenhuma ordem específica. Para obter uma vista ordenada, utilize `top` (por exemplo) ou `sort` (através da tabela completa).

Mostra as primeiras n linhas, ordenadas por uma coluna específica:

```AIQL

    requests | top 10 by timestamp desc
```

* *Sintaxe:* operadores a maioria das tem parâmetros de palavra-chave, tal como `by`.
* `desc`= a ordem descendente, `asc` = ascendente.

![](./media/app-insights-analytics-tour/260.png)

`top...`é uma forma de performant mais de indicar `sort ... | take...`. Iremos foi de escrever:

```AIQL

    requests | sort by timestamp desc | take 10
```

O resultado seria o mesmo, mas iria executar um pouco mais lentamente. (Também poderia escrever `order`, que é um alias de `sort`.)

Os cabeçalhos de coluna na vista de tabela também podem ser utilizados para ordenar os resultados no ecrã. Mas de decorrer, se utilizou `take` ou `top` obter apenas parte de uma tabela irá apenas reordenar os registos tiver obtido.

## <a name="wherehttpsdocsloganalyticsioquerylanguagequerylanguagewhereoperatorhtml-filtering-on-a-condition"></a>[Onde](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html): numa condição de filtragem

Vamos ver apenas pedidos que devolveu um código de resultado específica:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

O `where` operador aceita uma expressão booleana. Eis alguns pontos chaves sobre os mesmos:

* `and`, `or`: Operadores booleanos
* `==`, `<>`, `!=` : igual e não ser igual a
* `=~`, `!~` : cadeia sensível igual e não é igual. Existem muitos mais operadores de comparação de cadeias.

<!---Read all about [scalar expressions]().--->

### <a name="getting-the-right-type"></a>Obter o tipo correto
Localize pedidos sem êxito:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Hora

Por predefinição, as suas consultas estão limitadas para as últimas 24 horas. Mas pode alterar este intervalo:

![](./media/app-insights-analytics-tour/change-time-range.png)

Substituir o intervalo de tempo ao escrever qualquer consulta que menciona suportadas `timestamp` numa cláusula where. Por exemplo:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

A funcionalidade de intervalo de tempo é equivalente a uma cláusula 'where' inserida após cada menção de uma das tabelas de origem.

`ago(3d)`significa 'há três dias'. Horas de incluir outras unidades de tempo (`2h`, `2.5h`), minutos (`25m`) e segundos (`10s`).

Outros exemplos:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[Datas e horas referência](https://docs.loganalytics.io/concepts/concepts_datatypes_datetime.html).


## <a name="projecthttpsdocsloganalyticsioquerylanguagequerylanguageprojectoperatorhtml-select-rename-and-compute-columns"></a>[Projeto](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html): selecionar, mudar o nome e colunas de computação
Utilize [ `project` ](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) escolha enviados apenas as colunas que pretende:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

Também pode mudar o nome de colunas e definir novos:

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![resultado](./media/app-insights-analytics-tour/270.png)

* Os nomes das colunas podem incluir espaços ou símbolos se estes estão entre parênteses assim: `['...']` ou`["..."]`
* `%`é a habitual operador do módulo.
* `1d`(que é um dígito, em seguida, um tinha ') é um timespan literal, o que significa um dia. Seguem-se algumas mais literais de timespan: `12h`, `30m`, `10s`, `0.01s`.
* `floor`(alias `bin`) arredonda por um valor para o múltiplo de valor base que fornece mais próximo. Por isso, `floor(aTime, 1s)` Arredonda por um período de tempo para baixo para a segunda mais próximo.

As expressões podem incluir todos os operadores habituais (`+`, `-`,...), sendo uma variedade de funções de útil.

## <a name="extend"></a>Expansão
Se pretender adicionar colunas dos existentes, utilize [ `extend` ](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Utilizar [ `extend` ](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html) é menos verboso que [ `project` ](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) se pretender manter todas as colunas existentes.

### <a name="convert-to-local-time"></a>Converter a hora local

Carimbos encontram-se sempre em UTC. Por isso, se estiver em Costa do Pacífico-nos e é Inverno, o que poderá gostar isto:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizehttpsdocsloganalyticsioquerylanguagequerylanguagesummarizeoperatorhtml-aggregate-groups-of-rows"></a>[Resumir](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html): Agregar grupos de linhas
`Summarize`aplica-se uma determinada *numa função de agregação* através de grupos de linhas.

Por exemplo, o tempo a aplicação web demora a responder a um pedido é reportado no campo `duration`. Vamos ver o tempo de resposta médio para todos os pedidos:

![](./media/app-insights-analytics-tour/410.png)

Ou, iremos foi separar o resultado em pedidos de nomes diferentes:

![](./media/app-insights-analytics-tour/420.png)

`Summarize`recolhe os pontos de dados no fluxo em grupos para os quais o `by` cláusula avalia igualmente. Cada valor de `by` expressão - cada nome de operação de exemplo acima - resulta numa linha na tabela de resultados.

Ou, iremos foi agrupar os resultados por hora do dia:

![](./media/app-insights-analytics-tour/430.png)

Repare como estamos a utilizar o `bin` função (aka `floor`). Se é utilizada apenas `by timestamp`, cada linha da entrada terminaria cópias de segurança no seu próprio grupo reduzida. Para qualquer escalar contínua, como vezes ou números, temos de quebrar o intervalo de contínuo um número de valores discretos gerível. `bin`-que é apenas a familiar arredondamento pendente `floor` funcionar – é a forma mais fácil fazê-lo.

Podemos utilizar a mesma técnica para reduzir os intervalos de cadeias:

![](./media/app-insights-analytics-tour/440.png)

Tenha em atenção que pode utilizar `name=` para definir o nome de uma coluna de resultados, ou as expressões de agregação nem por-cláusula.

## <a name="counting-sampled-data"></a>Contagem de amostragem dados
`sum(itemCount)`é a agregação recomendada para a contagem de eventos. Em muitos casos, itemCount = = 1, pelo que a função contagens simplesmente configurar o número de linhas no grupo. Mas quando [amostragem](app-insights-sampling.md) está numa operação, apenas uma fração dos eventos originais são mantidos como pontos de dados no Application Insights, para que para cada ponto de dados, ver, existem `itemCount` eventos.

Por exemplo, se amostragem rejeita 75% de eventos originais, então itemCount = = 4 nos registos de retidos - ou seja, para cada registo retido, ocorreram quatro registos originais.

Amostragem adaptável faz com que itemCount seja superior durante períodos quando a aplicação está a ser utilizada descontos elevados.

Por conseguinte, summing segurança itemCount fornece uma boa estimativa do número de eventos original.

![](./media/app-insights-analytics-tour/510.png)

Há também um `count()` agregação (e uma operação de contagem), nos casos em que realmente pretende contabilizar o número de linhas de um grupo.

Há uma série de [funções de agregação](https://docs.loganalytics.io/learn/tutorials/aggregations.html).

## <a name="charting-the-results"></a>Charting os resultados
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Por predefinição, os resultados são apresentadas como uma tabela:

![](./media/app-insights-analytics-tour/225.png)

Podemos fazer melhor do que a vista de tabela. Vamos observar os resultados na vista de gráfico com vertical barra opção:

![Clique em gráfico, em seguida, escolha o gráfico de barras Vertical e atribuir x e y eixos](./media/app-insights-analytics-tour/230.png)

Repare que, apesar de que não tenha ordenar os resultados por hora (como pode ver na apresentação do tabela), a visualização de gráfico mostra sempre DateTime na ordem correta.


## <a name="timecharts"></a>Timecharts
Mostra existe eventos quantas está a cada hora:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Selecione a opção de apresentação do gráfico:

![timechart](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Várias séries
Várias expressões no `summarize` cláusula cria várias colunas.

Várias expressões no `by` cláusula cria várias linhas, um para cada combinação de valores.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tabela de pedidos por hora e localização](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Segmentar um gráfico por dimensões
Se o gráfico uma tabela que tem uma coluna de cadeia e uma coluna numérica, a cadeia pode ser utilizada para dividir os dados numéricos em separado série de pontos. Se existir mais do que uma coluna de cadeia, pode escolher qual coluna a utilizar como o discriminador.

![Segmentar um gráfico de análise](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Taxa de saltar

Converte um valor booleano para uma cadeia para utilizá-lo como um discriminador:

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Apresentação das métricas vários
Se o gráfico uma tabela que tem mais do que uma coluna numérica, além de timestamp, pode apresentar qualquer combinação dos mesmos.

![Segmentar um gráfico de análise](./media/app-insights-analytics-tour/110.png)

Tem de selecionar **não divisão** para poder selecionar várias colunas numéricas. Não é possível dividir por uma coluna de cadeia em simultâneo como apresentar mais do que uma coluna numérica.

## <a name="daily-average-cycle"></a>Ciclo de média diário
Como variar utilização ao longo do dia médio?

Pedidos de contagem do tempo de módulo um dia, binned em horas:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Gráfico de linhas de horas dentro de um dia médio](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Tenha em atenção que atualmente temos converter DateTime durações de tempo para apresentar no gráfico de linhas.
>
>

## <a name="compare-multiple-daily-series"></a>Comparar várias séries diárias
Como utilização variar com a hora do dia em diferentes países?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![Divisão pela client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Desenhar uma distribuição
O número de sessões existem de comprimentos diferentes?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

A última linha é necessário para converter para datetime. Atualmente, o eixo x de um gráfico é apresentado como escalar apenas se se tratar de uma datetime.

O `where` cláusula exclui da sessões (sessionDuration = = 0) e define o comprimento do eixo x.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsioquerylanguagequerylanguagepercentilesaggfunctionhtml"></a>[Percentiles](https://docs.loganalytics.io/queryLanguage/query_language_percentiles_aggfunction.html)
Os intervalos de durações abrangem diferentes percentagens de sessões?

Utilize a consulta acima, mas substitua a última linha:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Removemos também o limite superior no where cláusula, para obter as suas figuras corretas, incluindo todas as sessões com mais do que um pedido:

![resultado](./media/app-insights-analytics-tour/180.png)

De que é possível ver que:

* % de 5 de sessões de ter uma duração inferior a 3 minutos 34s;
* 50% das sessões última menos 36 minutos;
* % de 5 de sessões última mais de 7 dias

Para obter uma divisão separada para cada país, iremos apenas tem de colocar a coluna de client_CountryOrRegion em separado através de ambos resumem operadores:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Associar
Temos de acesso a várias tabelas, incluindo pedidos e exceções.

Para localizar as exceções relacionadas com um pedido que devolveu uma resposta de falha, pode associar as tabelas no `session_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


É recomendável utilizar `project` para selecionar apenas as colunas precisamos antes de efetuar a associação.
Nas cláusulas mesmas, iremos mudar o nome da coluna de carimbo.

## <a name="lethttpsdocsloganalyticsioquerylanguagequerylanguageletstatementhtml-assign-a-result-to-a-variable"></a>[Permitir que](https://docs.loganalytics.io/queryLanguage/query_language_letstatement.html): atribua um resultado a uma variável

Utilize `let` para separar terminar as partes da expressão anterior. Os resultados são iguais:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> No cliente de análise, não coloque linhas em branco entre as partes da consulta. Certifique-se executar todos.
>

Utilize `toscalar` para converter uma célula de tabela única para um valor:

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Funções

Utilize *permitem* para definir uma função:

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Ao aceder aos objetos aninhados
Objetos aninhados podem ser acedidos facilmente. Por exemplo, no fluxo exceções pode ver objetos estruturados como esta:

![resultado](./media/app-insights-analytics-tour/520.png)

Pode aplaná-lo ao escolher as propriedades que estiver interessado em:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Tenha em atenção que tem de converter o resultado para o tipo apropriado.


## <a name="custom-properties-and-measurements"></a>Propriedades personalizadas e medidas
Se a sua aplicação anexa [dimensões personalizadas (propriedades) e medidas personalizadas](app-insights-api-custom-events-metrics.md#properties) eventos, em seguida, verá-las no `customDimensions` e `customMeasurements` objetos.

Por exemplo, se a sua aplicação inclui:

```C#

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Para extrair estes valores na análise:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type

```

Para verificar se uma dimensão personalizada é um tipo específico:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

## <a name="dashboards"></a>Dashboards
Pode afixar os resultados a um dashboard para reunir todas as suas mais importantes gráficos e tabelas.

* [Dashboard de partilhada do Azure](app-insights-dashboards.md#share-dashboards): clique no ícone de pin. Antes de fazer isto, tem de ter um dashboard partilhado. No portal do Azure, abra ou criar um dashboard e clique em partilha.
* [Dashboard do Power BI](app-insights-export-power-bi.md): clique em exportar, a consulta do Power BI. Uma vantagem esta alternativa é que pode apresentar a sua consulta juntamente com outros resultados de uma vasta gama de origens.

## <a name="combine-with-imported-data"></a>Combinar com os dados importados

Relatórios de análise parecer excelentes no dashboard, mas por vezes, pretende traduzir os dados para um formulário mais digestible. Por exemplo, suponha que os utilizadores autenticados são identificados na telemetria por um alias. Pretende apresentar os respetivos nomes reais os resultados. Para tal, precisa de um ficheiro CSV que mapeia dos aliases para os nomes reais.

Pode importar um ficheiro de dados e utilizá-lo, tal como qualquer uma das tabelas standard (pedidos, exceções e assim sucessivamente). A consulta-lo no seu próprio ou associar com outras tabelas. Por exemplo, se tiver uma tabela com o nome usermap e tem colunas `realName` e `userId`, em seguida, pode utilizá-lo ao traduzir o `user_AuthenticatedId` campo na telemetria do pedido:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Importe uma tabela, no painel do esquema, em **outras origens de dados**, siga as instruções para adicionar uma nova origem de dados, através do carregamento de uma amostra dos seus dados. Em seguida, pode utilizar esta definição para carregar as tabelas.

A funcionalidade de importação está atualmente em pré-visualização, pelo que inicialmente, verá uma ligação "Contacte-nos" em "Outras origens de dados". Utilize esta opção para se inscrever no programa de pré-visualização e a ligação, em seguida, será substituída por um botão "Adicionar nova origem de dados".


## <a name="tables"></a>Tabelas
O fluxo de telemetria recebida da sua aplicação é acessível através de várias tabelas. O esquema das propriedades disponíveis para cada tabela está visível no lado esquerdo da janela.

### <a name="requests-table"></a>Tabela de pedidos
Número pedidos HTTP para a sua aplicação web e o segmento de por nome da página:

![Pedidos de contagem segmentados por nome](./media/app-insights-analytics-tour/analytics-count-requests.png)

Localize os pedidos que não obedeçam a maior parte:

![Pedidos de contagem segmentados por nome](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Tabela de eventos personalizados
Se utilizar [trackevent ()](app-insights-api-custom-events-metrics.md#trackevent) para enviar os seus próprios eventos, pode lê-los desta tabela.

Vejamos um exemplo em que o seu código de aplicação contém estas linhas:

```C#

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Apresenta a frequência destes eventos:

![Taxa de visualização de eventos personalizados](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Extrair dimensões e medidas de eventos:

![Taxa de visualização de eventos personalizados](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Tabela de métricas personalizadas
Se estiver a utilizar [trackmetric ()](app-insights-api-custom-events-metrics.md#trackmetric) para enviar os seus próprios valores métricos, encontrará dos resultados no **customMetrics** fluxo. Por exemplo:  

![Métricas personalizadas na análise do Application Insights](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> No [Explorador de métricas](app-insights-metrics-explorer.md), todas as medidas personalizadas anexadas a qualquer tipo de telemetria de aparecerem juntos no painel de métricas, juntamente com as métricas enviadas utilizando `TrackMetric()`. Mas no Analytics, medidas personalizadas ainda estão ligadas a qualquer tipo de telemetria que foram executadas no - eventos ou pedidos e assim sucessivamente, enquanto métricas enviadas pelo TrackMetric aparecem no seu próprio fluxo.
>
>

### <a name="performance-counters-table"></a>Tabela de contadores de desempenho
[Contadores de desempenho](app-insights-performance-counters.md) mostram-lhe métricas básica do sistema para a sua aplicação, tais como CPU, memória e a utilização de rede. Pode configurar o SDK para enviar contadores adicionais, incluindo os suas próprias contadores personalizados.

O **performanceCounters** esquema expõe o `category`, `counter` nome, e `instance` nome de cada contador de desempenho. Os nomes de instâncias do contador só são aplicáveis a alguns contadores de desempenho e normalmente indicam o nome do processo para o qual relacionada com a contagem. A telemetria para cada aplicação, irá ver apenas os contadores para essa aplicação. Por exemplo, para ver contadores de que estão disponíveis:

![Contadores de desempenho na análise do Application Insights](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Para obter um gráfico de memória disponível durante o período selecionado:

![Memória timechart na análise do Application Insights](./media/app-insights-analytics-tour/analytics-available-memory.png)

Como outra telemetria, **performanceCounters** também tem uma coluna `cloud_RoleInstance` indica que a identidade do computador anfitrião no qual a aplicação está em execução. Por exemplo, para comparar o desempenho da sua aplicação das diferentes máquinas:

![Desempenho segmentados por instância de função no Application Insights analytics](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Tabela de exceções
[Exceções comunicado pela sua aplicação](app-insights-asp-net-exceptions.md) estão disponíveis nesta tabela.

Para localizar os pedidos de HTTP que a aplicação foi processar quando ocorreu a exceção, associar no operation_Id:

![Associar exceções com pedidos no operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Tabela de temporizações de browser
`browserTimings`Mostra os dados de carregamento de página recolhidos nos browsers dos seus utilizadores.

[Configurar a aplicação de telemetria do lado do cliente](app-insights-javascript.md) para ver estas métricas.

O esquema inclui [métricas que indica que os comprimentos das diferentes fases da processo de carregamento da página](app-insights-javascript.md#page-load-performance). (Não indicam o período de tempo que os utilizadores ler uma página.)  

Mostrar popularities de páginas diferentes e carregar vezes para cada página:

![Tempos de carregamento de página no Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Tabela de resultados de disponibilidade
`availabilityResults`Mostra os resultados da sua [testes web](app-insights-monitor-web-app-availability.md). Cada execução do seu testes de cada localização de teste é comunicada separadamente.

![Tempos de carregamento de página no Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabela de dependências
Contém resultados de chamadas que torna a sua aplicação para as bases de dados e REST APIs e outras chamadas para TrackDependency(). Também inclui as chamadas AJAX feitas a partir do browser.

Chamadas AJAX do browser:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Chamadas de dependência do servidor:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Mostram sempre resultados de dependência do lado do servidor `success==False` se o agente do Application Insights não está instalado. No entanto, os outros dados estão corretos.

### <a name="traces-table"></a>Tabela de rastreios
Contém a telemetria enviada pela sua aplicação utilizar tracktrace (,) ou [outras arquiteturas de registo](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Vídeo 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Consultas avançadas:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Passos seguintes
* [Referência de linguagem de análise](app-insights-analytics-reference.md)
* [SQL Server-dos utilizadores cheat folha](https://aka.ms/sql-analytics) traduz as idioms mais comuns.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
