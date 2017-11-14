---
title: "Através da análise - a ferramenta poderosa pesquisa do Azure Application Insights | Microsoft Docs"
description: "Através da análise, a ferramenta de pesquisa de diagnóstico de elevado desempenho do Application Insights. "
services: application-insights
documentationcenter: 
author: danhadari
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: danha; mbullwin
ms.openlocfilehash: 0ca5c8b19f4699548a8551ec673e4a067d4e5fad
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="using-analytics-in-application-insights"></a>Através da análise no Application Insights
[Análise de](app-insights-analytics.md) é a funcionalidade de pesquisa poderoso de [Application Insights](app-insights-overview.md). Estas páginas descrevem o idioma de consulta de análise de registos.

* **[Veja o vídeo introdutórias](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Experimente a análise nos nossos dados simulados](https://analytics.applicationinsights.io/demo)**  se a aplicação não está a enviar dados para o Application Insights ainda.

## <a name="open-analytics"></a>Análise de abrir
No recurso de raiz da sua aplicação no Application Insights, clique em análise.

![Abrir portal.azure.com, abra o recurso do Application Insights e clique em análise.](./media/app-insights-analytics-using/001.png)

O tutorial inline dá-lhe algumas ideias sobre o que pode fazer.

Não existe um [ainda mais extensiva apresentação aqui](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Consulta de telemetria
### <a name="write-a-query"></a>Escrever uma consulta
![Apresentação de esquema](./media/app-insights-analytics-using/150.png)

Começar com os nomes de qualquer uma das tabelas listadas no lado esquerdo (ou o [intervalo](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) ou [union](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) operadores). Utilize `|` para criar um pipeline de [operadores](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

O IntelliSense pede-lhe com os operadores e os elementos de expressão que pode utilizar. Clique no ícone de informações (ou prima CTRL + espaço) para obter uma descrição mais e exemplos sobre como utilizar cada elemento.

Consulte o [apresentação da linguagem de análise](app-insights-analytics-tour.md) e [referência de linguagem](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Executar uma consulta
![Execução de uma consulta](./media/app-insights-analytics-using/130.png)

1. Pode utilizar as quebras de linha única numa consulta.
2. Coloque o cursor no interior ou no final da consulta que pretende executar.
3. Verifique o intervalo de tempo da sua consulta. (Pode alterá-la ou substitui-lo, incluindo as suas próprias [ `where...timestamp...` ](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) cláusula na sua consulta.)
3. Clique em Ir para executar a consulta.
4. Não coloque linhas em branco na sua consulta. Pode manter várias consultas separados por um separador de consulta, separando-as com as linhas em branco. Apenas a consulta que tenha o cursor é executado.

### <a name="save-a-query"></a>Guardar uma consulta
![Guardar uma consulta](./media/app-insights-analytics-using/140.png)

1. Guarde o ficheiro de consulta atual.
2. Abra um ficheiro de consulta guardada.
3. Crie um novo ficheiro de consulta.

## <a name="see-the-details"></a>Consulte os detalhes
Expanda qualquer linha nos resultados para ver a lista completa das propriedades. Pode expandir ainda mais qualquer propriedade que é um valor estruturado - por exemplo, dimensões personalizadas ou a pilha de listagem numa exceção.

![Expanda uma linha](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Dispor os resultados
Pode ordenar, filtrar, de paginação e agrupar os resultados devolvidos da sua consulta.

> [!NOTE]
> Volte a ordenação, agrupamentos e filtragens no browser não executam a consulta. Estes reorganizar apenas os resultados que foram devolvidos pela última consulta. 
> 
> Para efetuar estas tarefas no servidor antes dos resultados são devolvidos, escrever a consulta com o [ordenação](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [resumir](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) e [onde](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) operadores.
> 
> 

Escolha as colunas que gostaria de ver, arraste cabeçalhos de coluna para reorganizá-los e redimensionar colunas arrastando os respetivos limites.

![Dispor colunas](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Ordenar e filtrar itens
Ordene os resultados ao clicar no cabeçalho da coluna. Clique novamente para ordenar a outra forma e, numa terceira tempo para reverter para a ordenação original devolvidos pela sua consulta.

Utilize o ícone de filtro para limitar a pesquisa.

![Ordenar e filtrar colunas](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Itens de grupo
Para ordenar por mais de uma coluna, utilize o agrupamento. Ativar primeiro e, em seguida, arraste cabeçalhos de coluna para o espaço acima da tabela.

![Grupo](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Em falta alguns resultados?

Se considerar que não está a ver todos os resultados que o esperado, existem algumas das razões possíveis.

* **Filtro de intervalo de tempo**. Por predefinição, apenas verá os resultados das últimas 24 horas. Não há um filtro automático que limita o intervalo de resultados que são obtidos a partir de tabelas de origem. 

    No entanto, pode alterar o intervalo de tempo filtro utilizando o menu pendente.

    Ou pode substituir o intervalo automático, incluindo as suas próprias [ `where  ... timestamp ...` cláusula](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) na sua consulta. Por exemplo:

    `requests | where timestamp > ago('2d')`

* **Limite de resultados**. Não há um limite de cerca de 10 mil linhas nos resultados devolvidos a partir do portal. Um aviso mostra se abordar o limite. Se isto acontecer, ordenar os resultados da tabela não serão sempre mostrar todos os reais primeiro ou últimos resultados. 

    É recomendável para evitar atingir o limite. Utilize o filtro de intervalo de tempo, ou operadores, tais como:

  * [primeiros 100 por timestamp](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [tirar 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [resumir](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [onde timestamp > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Mais de 10 linhas de k que pretende? Considere a utilização de [exportação contínua](app-insights-export-telemetry.md) em vez disso. Análise de foi concebida para análise, em vez de ao obter dados não processados.)

## <a name="diagrams"></a>Diagramas
Selecione o tipo de que gostaria de diagrama:

![Selecione um tipo de diagrama](./media/app-insights-analytics-using/230.png)

Se tiver várias colunas dos tipos de direita, pode escolher o x e y eixos e uma coluna de dimensões de dividir os resultados por.

Por predefinição, os resultados são apresentados inicialmente como uma tabela e selecione o diagrama manualmente. Mas pode utilizar o [compor diretiva](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) no final de uma consulta para selecionar um diagrama.

### <a name="analytics-diagnostics"></a>Diagnóstico de análise


Num timechart, se existir um pico pedidos repentino ou passo nos seus dados, poderá ver um ponto de realçado na linha. Isto indica que a análise de diagnóstico identificou uma combinação de propriedades filtrar a alteração repentino. Clique no ponto para obter mais detalhes sobre o filtro de e para ver a versão filtrada. Isto pode ajudar a identificar que causou a alteração. 

[Saiba mais sobre a análise de diagnóstico](app-insights-analytics-diagnostics.md)


![Diagnóstico de análise](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Afixar ao dashboard
Pode afixar um diagrama ou tabela para um dos seus [partilhado dashboards](app-insights-dashboards.md) -basta clicar o pin. 

![Clique no pin](./media/app-insights-analytics-using/pin-01.png)

Isto significa que, quando juntar um dashboard para o ajudar a monitorizar o desempenho ou a utilização dos seus serviços web, pode incluir a análise bastante complexa juntamente com as outras métricas. 

Pode afixar uma tabela para o dashboard, se tiver quatro ou menos colunas. São apresentadas apenas as linhas de sete superiores.

### <a name="dashboard-refresh"></a>Atualização do dashboard
O gráfico afixado ao dashboard seja atualizado automaticamente executando novamente a consulta aproximadamente a cada horas. Também pode clicar no botão atualizar.

### <a name="automatic-simplifications"></a>Simplifications automáticas

Determinados simplifications são aplicadas a um gráfico quando a afixá-lo a um dashboard.

**Restrição de tempo:** consultas estão limitadas automaticamente os últimos 14 dias. O efeito é igual à sua consulta inclui `where timestamp > ago(14d)`.

**Restrição de contagem de bin:** se apresentar um gráfico que tem um grande número de intervalos binários discretos (normalmente, um gráfico de barras), os intervalos de binários menos preenchidos automaticamente são agrupados numa única "outros" bin. Por exemplo, esta consulta:

    requests | summarize count_search = count() by client_CountryOrRegion

Este aspeto no Analytics:

![Gráfico com cauda longa](./media/app-insights-analytics-using/pin-07.png)

mas, quando a afixá-lo a um dashboard, se parece com isto:

![Gráfico com intervalos binários limitados](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exportar para Excel
Após executar uma consulta, pode transferir um ficheiro. csv. Clique em **exportar Excel**.

## <a name="export-to-power-bi"></a>Exportar para o Power BI
Coloque o cursor numa consulta e escolha **exportar, Power BI**.

![Exportar a partir de análise para o Power BI](./media/app-insights-analytics-using/240.png)

Executar a consulta no Power BI. Pode definir para atualizar com base numa agenda.

Com o Power BI, pode criar dashboards que reunir dados a partir de uma ampla variedade de origens.

[Saiba mais sobre a exportação para o Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Ligação avançada

Obtenha uma ligação em **exportação, ligação partilha** que pode enviar para outro utilizador. Desde que o utilizador tem [acesso ao seu grupo de recursos](app-insights-resources-roles-access-control.md), a consulta será aberto na IU de análise.

(Na ligação, é apresentado o texto da consulta após "? q =", gzip comprimidos e com codificação base 64. Pode escrever código para gerar ligações avançadas que fornecem aos utilizadores. No entanto, o modo recomendado para executar a análise a partir do código é utilizando o [REST API](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automatização

Utilize o [API de REST de acesso de dados](https://dev.applicationinsights.io/) para executar consultas de análise. [Por exemplo](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (utilizando o PowerShell):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Ao contrário da IU de análise, a API REST não adiciona automaticamente quaisquer limitação timestamp para as suas consultas. Lembre-se de adicionar a seus próprios cláusula where, para evitar a obter respostas grandes.



## <a name="import-data"></a>Importar dados

Pode importar dados de um ficheiro CSV. É uma utilização típica importar dados estáticos que pode associar as tabelas da sua telemetria. 

Por exemplo, se os utilizadores autenticados são identificados na sua telemetria por um alias ou o id oculta, foi possível importar uma tabela que mapeia aliases para os nomes reais. Efetuando uma associação a telemetria de pedido, pode identificar os utilizadores, os respetivos nomes reais nos relatórios de análise.

### <a name="define-your-data-schema"></a>Definir o esquema de dados

1. Clique em **definições** (na parte superior esquerda) e, em seguida, **origens de dados**. 
2. Adicione uma origem de dados, siga as instruções. É-lhe pedido para fornecer uma amostra de dados, o que devem incluir, pelo menos, dez linhas. Em seguida, corrija o esquema.

Isto define uma origem de dados, em seguida, pode utilizar para importar as tabelas individuais.

### <a name="import-a-table"></a>Importe uma tabela

1. Abra a definição da origem de dados a partir da lista.
2. Clique em "Carregar" e siga as instruções para carregar a tabela. Isto envolve a uma chamada para uma API REST e, por isso, é fácil automatizar. 

A tabela está agora disponível para utilização em consultas de análise. Irá aparecer na análise 

### <a name="use-the-table"></a>Utilize a tabela

Vamos imaginar denomina-se a definição da origem de dados `usermap`, e que tem dois campos, `realName` e `user_AuthenticatedId`. O `requests` tabela também tem um campo com o nome `user_AuthenticatedId`, por isso, é fácil associá-los:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
A tabela resultante de pedidos tem uma coluna adicional, `realName`.

### <a name="import-from-logstash"></a>Importar do LogStash

Se utilizar [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), pode utilizar a análise para consultar os registos. Utilize o [Plug-in que encaminha dados para análise](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

