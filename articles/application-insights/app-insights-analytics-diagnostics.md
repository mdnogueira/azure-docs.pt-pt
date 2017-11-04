---
title: "Smart diagnósticos de alterações de desempenho de aplicações web no Azure Application Insights | Microsoft Docs"
description: "Diagnóstico automático de picos ou os passos na telemetria de desempenho da sua aplicação web."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: mbullwin
ms.openlocfilehash: e0c8d712f03da0c5e47ea422b051c0b8734c6b21
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>Diagnosticar repentino alterações na sua telemetria de aplicação

*Esta funcionalidade está em pré-visualização.*

Diagnosticar alterações repentino no desempenho da aplicação web ou a utilização com um único clique! A funcionalidade de diagnóstico inteligente está disponível sempre que se cria um gráfico de tempo no [análise](app-insights-analytics.md) no [Application Insights](app-insights-overview.md). Sempre que há uma alteração de tendência dos resultados da sua, tais como um pico de pedidos ou um dip invulgar diagnóstico inteligente identifica um padrão de dimensões e os valores relacionados que possam explicam a alteração. Isto ajuda a diagnosticar o problema rapidamente. 

Neste exemplo, o diagnóstico inteligente identificou um padrão dos valores de propriedade associados a alteração e realça a diferença entre os resultados com e sem que padrão:

![resultados de diagnóstico de análise de exemplo](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>Diagnosticar alterações de dados

1.  Executar uma consulta na análise e compor-lo como um gráfico de tempo. 
2.  Clique em qualquer ponto de pico realçado, se existir um.
 
    ![ponto de pico](./media/app-insights-analytics-diagnostics/peak.png)

    Diagnóstico demora alguns segundos para detetar um padrão.

3. O separador de resultados de diagnóstico mostra um padrão que pode explicar a descontinuidade de dados.

    ![resultado](./media/app-insights-analytics-diagnostics/result.png)
 
    O texto mostra os valores de dimensão que parecem estar relacionados com o shift. Neste exemplo, está associada a um pedido específico e uma versão de browser específico.

    Repare também os dois componentes do gráfico, com o verdadeiro de filtro e false. O componente falso mostra uma tendência inalterada. Por outras palavras, não há nenhuma alteração nos resultados da telemetria, iremos exclua a combinação problemática das dimensões diagnóstico identificou. Por outro lado, os resultados dentro desse combinação mostram uma alteração enormes dentro da área de realçado da investigação. Isto mostra que o diagnóstico encontrou uma combinação de propriedades que explica a alteração.

4.  Se o padrão for complexo, tem de colocar o cursor sobre **Mostrar tudo** para ver as dimensões.

    ![mostrar tudo](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  No caso de diagnóstico não localiza nenhum padrão significativo para notificar acerca, será apresentada a página 'não existem resultados'. Neste momento, pode alterar a consulta. Por exemplo, pode restringir o intervalo de tempo e a discretização na consulta de análise, para uma análise adicional e potencialmente melhor resultados.

Armed com o conhecimento do que uma página específica do seu site tiver um problema num determinado browser, pode ir diretamente para a página de problema e investigar as alterações recentes.

## <a name="try-the-demo"></a>Experimentar a demonstração

[Clique aqui para ver uma demonstração](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H) nos dados de exemplo.

## <a name="how-it-works"></a>Como funciona

Diagnóstico inteligente utiliza um algoritmo de aprendizagem máquina supervisionados avançada com base no [DiffPatterns](app-insights-analytics-reference.md) operação. Procura padrões de candidatos que podem explicam a alteração de dados. Analyses o impacto de cada candidato na métrica e mostra o padrão que melhor está correlacionada com a alteração.

## <a name="no-diagnostic-points"></a>Não existem pontos de diagnóstico?

Diagnóstico inteligente só funciona quando são cumpridos os seguintes critérios:

 * Definição de diagnóstico inteligente é ativada. Procure no ícone de definições na análise.
 * Selecionar a opção de diagnóstico inteligente nas definições de análise. 
 * Eixo de tempo: O eixo x do gráfico tem de ser do tipo `datetime`.
 * Gráfico de área ou linha: um diagnóstico só funciona estes tipos de gráfico. Utilize `| render timechart` ou `| render areachart` no final da sua consulta; ou selecione linha ou a área de gráfico de Seletor de lista pendente.
 * Descontinuidade: Tem de existir uma descontinuidade significativa nos dados.
 * Pontos suficientes para analisar.
 * Resumir não mais do que uma cláusula FROM na consulta.
 * Nenhuma cláusula de projeto que contém uma definição de nome antes da cláusula summarize.

 
 ## <a name="related-articles"></a>Artigos relacionados

 * [Tutorial de análise](app-insights-analytics-tour.md)
 * [Deteção de smart](app-insights-proactive-diagnostics.md) automaticamente alerta-o para problemas de desempenho.