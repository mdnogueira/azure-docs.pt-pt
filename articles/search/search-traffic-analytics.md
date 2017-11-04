---
title: "Análise de tráfego de pesquisa para a Azure Search | Microsoft Docs"
description: "Ative a análise de tráfego de pesquisa para a Azure Search, um serviço de pesquisa em nuvem alojado no Microsoft Azure, para desbloquear informações sobre os seus utilizadores e os dados."
services: search
documentationcenter: 
author: bernitorres
manager: jlembicz
editor: 
ms.assetid: b31d79cf-5924-4522-9276-a1bb5d527b13
ms.service: search
ms.devlang: multiple
ms.workload: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/05/2017
ms.author: betorres
ms.openlocfilehash: 303ca5c820f573dc0b58f1910f258403c3baad2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-search-traffic-analytics"></a>O que é a análise de tráfego de pesquisa
Análise de tráfego de pesquisa é um padrão para implementar um ciclo de comentários do seu serviço de pesquisa. Este padrão descreve os dados necessários e como recolhê-la utilizando o Application Insights, um leader da indústria para monitorizar os serviços em várias plataformas.

Análise de tráfego de pesquisa permite-lhe ganhar visibilidade ao seu serviço de pesquisa e desbloquear informações sobre os seus utilizadores e do respetivo comportamento. Fazendo com que dados sobre o que escolhem os seus utilizadores, é possível para tomar decisões melhorar ainda mais a sua experiência de pesquisa e para fazer uma cópia desativado quando os resultados são não que o esperado.

A pesquisa do Azure oferece uma solução de telemetria que integra o Azure Application Insights e o Power BI para fornecer monitorização e controlo detalhada. Uma vez interação com a Azure Search é apenas através de APIs, tem de ser implementada a telemetria pelos programadores utilizando a pesquisa, seguindo as instruções nesta página.

## <a name="identify-the-relevant-search-data"></a>Identificar os dados de pesquisa relevantes

Para que as métricas de pesquisa útil, é necessário iniciar sessão algumas sinais dos utilizadores da aplicação de pesquisa. Estes sinais significam conteúdo que os utilizadores estão interessados nas e que o considerar relevante para as suas necessidades.

Existem dois sinais que tem de análise de tráfego de pesquisa:

1. Eventos de pesquisa de gerados pelo utilizador: apenas consultas de pesquisa iniciadas por um utilizador são interessantes. Pedidos de pesquisa utilizados para preencher facetas, conteúdo adicional ou quaisquer informações internas, não são importantes e poderem prejudicar e bias os resultados.

2. Eventos de clique gerados pelo utilizador: por cliques neste documento, vamos referir-se a um utilizador selecionar um resultado de pesquisa específico devolvido por uma consulta de pesquisa. Um clique, geralmente, significa que um documento é um resultado relevante para uma consulta de pesquisa específico.

Ao ligar os eventos de pesquisa e clique em com um id de correlação, é possível analisar os comportamentos dos utilizadores na sua aplicação. Estas informações de pesquisa são impossíveis obter com apenas registos de tráfego de pesquisa.

## <a name="how-to-implement-search-traffic-analytics"></a>Como implementar a análise de tráfego de pesquisa

Sinais mencionados na secção anterior devem ser reunidas a partir da aplicação de pesquisa como o utilizador interage com o mesmo. Application Insights é uma solução de monitorização extensível, disponível para várias plataformas, com opções de instrumentação flexível. Utilização do Application Insights permite-lhe tirar partido dos relatórios de pesquisa do Power BI criado por da Azure Search para facilitar a análise de dados.

No [portal](https://portal.azure.com) página para o seu serviço da Azure Search, o painel de análise de tráfego de pesquisa contém uma cábula para seguir este padrão de telemetria. Também pode selecionar ou criar um recurso do Application Insights e ver os dados necessários, num só local.

![Instruções de análise de tráfego de pesquisa][1]

### <a name="1-select-an-application-insights-resource"></a>1. Selecione um recurso do Application Insights

Tem de selecionar um recurso do Application Insights para utilizar ou criar um se ainda não tiver um. Pode utilizar um recurso que já está a ser utilizado para registar os eventos personalizados necessários.

Ao criar um novo recurso do Application Insights, todos os tipos de aplicação são válidos para este cenário. Selecione a que melhor se adequa a plataforma que está a utilizar.

Tem a chave de instrumentação para criar o cliente de telemetria para a sua aplicação. Pode obtê-lo a partir do dashboard do portal do Application Insights ou, pode obtê-lo da página de análise de tráfego de pesquisa, selecionar a instância que pretende utilizar.

### <a name="2-instrument-your-application"></a>2. Instrumente a sua aplicação

Esta fase é onde pode instrumente a sua própria aplicação de pesquisa, utilizando o recurso do Application Insights sua criada no passo acima. Existem quatro passos a este processo:

**POSSO. Criar um cliente de telemetria** este é o objeto que envia eventos para o recurso do Application Insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Para outros idiomas e plataformas, consulte o concluída [lista](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**II. Um ID de pesquisa para correlação de pedido** para correlacionar os pedidos de pesquisa com cliques, é necessário ter um id de correlação que está relacionada com estes dois eventos distintos. A pesquisa do Azure fornece um Id de pesquisa quando solicitá-la com um cabeçalho:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**III. Registo de eventos de pesquisa**

Sempre que um pedido de pesquisa é emitido por um utilizador, deve iniciar que como um evento de procura com o esquema seguinte num evento personalizado do Application Insights:

**ServiceName**: nome do serviço de pesquisa (cadeia) **SearchId**: identificador de exclusivo (guid) da consulta de pesquisa (vem na resposta pesquisa) **IndexName**: índice de serviço de pesquisa (cadeia) a ser consultar **QueryTerms**: termos de pesquisa (cadeia) introduzidos pelo utilizador **o parâmetro ResultCount**: (int) número de documentos que foram devolvidas (vem na resposta pesquisa)  **ScoringProfile**: nome o perfil de classificação utilizado, caso existam (cadeia)

> [!NOTE]
> Contagem de pedidos em consultas gerados pelo utilizador adicionando $count = TRUE para a consulta de pesquisa. Ver mais informação [aqui](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Lembre-se apenas iniciar consultas de pesquisa que são geradas pelos utilizadores.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**IV. Clique em de registo eventos**

Sempre que um utilizador clica num documento, que é um sinal de que tem de ter sessão iniciado para efeitos de análise de pesquisa. Utilize eventos personalizados do Application Insights para registar estes eventos com o esquema seguinte:

**ServiceName**: nome do serviço de pesquisa (cadeia) **SearchId**: identificador de exclusivo (guid) de consulta de pesquisa relacionados **DocId**: identificador de documento (cadeia) **posição** : página de resultados de classificação (int) do documento na pesquisa

> [!NOTE]
> Posição refere-se para a ordem cardinal na sua aplicação. Está livre para definir este número, desde que é sempre o mesmo, para permitir a comparação.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.TrackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

### <a name="3-analyze-with-power-bi-desktop"></a>3. Analisar com o ambiente de trabalho do Power BI

Depois de ter instrumentados a sua aplicação e verificar a que sua aplicação está corretamente ligada ao Application Insights, pode utilizar um modelo predefinido criado pelo Azure Search para ambiente de trabalho do Power BI.
Este modelo contém gráficos e tabelas que o ajudar a tomar decisões mais informadas para melhorar o desempenho de pesquisa e relevância.

Para instanciar o modelo de ambiente de trabalho do Power BI, terá de três informações sobre o Application Insights. Estes dados podem ser encontrados na página de análise de tráfego de pesquisa, quando seleciona o recurso a utilizar

![Application Insights dados no painel de análise de tráfego de pesquisa][2]

Métricas incluídas no modelo de ambiente de trabalho do Power BI:

*   Clique em através de velocidade (CTR): o rácio de utilizadores que clique num documento específico para o número de pesquisas totais.
*   Procura sem cliques: termos de licenciamento para cima consultas que não registar nenhum cliques
*   Clicou mais documentos: clicado mais documentos por ID nas últimas 24 horas, 7 dias e 30 dias.
*   Pares de documento de termo populares: termos de licenciamento que resultam no mesmo documento clicado, ordenadas pelo cliques.
*   Tempo de clicar em: cliques agrupadosm por tempo desde a consulta de pesquisa

![Modelo de BI de energia para ler a partir do Application Insights][3]


## <a name="next-steps"></a>Passos Seguintes
Instrumente a sua aplicação de pesquisa para obter dados de elevado desempenho e insightful sobre o serviço de pesquisa.

Pode encontrar mais informações sobre o Application Insights [aqui](https://go.microsoft.com/fwlink/?linkid=842905). Visite o Application Insights [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre os diferentes escalões de serviço.

Saiba mais sobre a criação de relatórios incrível. Consulte [introdução ao Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/) para obter detalhes

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
