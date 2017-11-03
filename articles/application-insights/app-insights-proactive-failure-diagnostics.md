---
title: "Smart - deteção de anomalias de falha, no Application Insights | Microsoft Docs"
description: "Alerta-o para alterações invulgares na taxa de pedidos falhados à sua aplicação web e fornece uma análise de diagnóstico. É necessária nenhuma configuração."
services: application-insights
documentationcenter: 
author: yorac
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: mbullwin
ms.openlocfilehash: ca484f4d11cf8ab18db2d0c6152f369a90311f10
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="smart-detection---failure-anomalies"></a>Deteção inteligente - anomalias de falha
[Application Insights](app-insights-overview.md) automaticamente notifica-o em tempo real se a aplicação web sofre um aumento anormal da taxa de pedidos falhados. Deteta um aumento súbito invulgar na taxa de pedidos de HTTP ou chamadas de dependência são comunicadas como falha. Para pedidos, pedidos falhados são normalmente os códigos de resposta de 400 ou superior. Para ajudar a Triar e diagnosticar o problema, uma análise das características das falhas e telemetrias relacionadas é fornecida na notificação. Também existem ligações para o portal do Application Insights para obter diagnósticos adicionais. A funcionalidade não precisa de nenhuma configuração nem a configuração, como o utiliza algoritmos do machine learning para prever a taxa de falhas normais.

Esta funcionalidade funciona para aplicações de web Java e ASP.NET, alojadas na nuvem ou os seus próprios servidores. Também funciona para qualquer aplicação que gera a telemetria de dependência ou de pedido - por exemplo, se tiver uma função de trabalho que chama [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) ou [TrackDependency()](app-insights-api-custom-events-metrics.md#trackdependency).

Depois de configurar [Application Insights para o seu projeto](app-insights-overview.md), e fornecido a sua aplicação gerar uma determinada quantidade mínima de telemetria, inteligente deteção de anomalias de falha demora 24 horas para saber o comportamento normal da sua aplicação, antes de ser ativado e pode enviar alertas.

Segue-se um alerta de exemplo.

![Alerta de deteção inteligente de exemplo que mostra a análise de cluster em torno de falha](./media/app-insights-proactive-failure-diagnostics/013.png)

> [!NOTE]
> Por predefinição, receberá uma mensagem de formato mais curta que neste exemplo. Mas pode [mudar para este formato de detalhado](#configure-alerts).
>
>

Tenha em atenção que o informa sobre:

* A taxa de falhas em comparação comparada o comportamento normal da aplicação.
* Número de utilizadores que é afetado – para saber como muito para se preocupe.
* Um padrão características associado com falhas. Neste exemplo, não há um código de resposta específica, o nome do pedido (operação) e a versão de aplicação. Que imediatamente indica onde iniciar a procura no seu código. Outras possibilidades pode ser um sistema de operativo de cliente ou de browser específico.
* A exceção, rastreios de registo e de falha de dependência (bases de dados ou outros componentes externos) que parecem ser associado com falhas characterized.
* Ligações diretas pesquisas relevantes a telemetria no Application Insights.

## <a name="benefits-of-smart-detection"></a>Vantagens da deteção inteligente
Comum [alertas métricas](app-insights-alerts.md) dizer o pode existir um problema. Mas deteção inteligente inicia o trabalho de diagnóstico para si, efetuar uma grande quantidade da análise, caso contrário, terá de fazer por si. Obter os resultados neatly empacotados, ajudando-o a aceder rapidamente a raiz do problema.

## <a name="how-it-works"></a>Como funciona
Deteção inteligente monitoriza a telemetria recebida da sua aplicação e, em especial as taxas de falha. Esta regra conta o número de pedidos para o qual o `Successful request` propriedade é falsa e chama o número de dependência para o qual o `Successful call` propriedade é falsa. Para pedidos como, por predefinição, `Successful request == (resultCode < 400)` (a menos que escrever código personalizado para [filtro](app-insights-api-filtering-sampling.md#filtering) ou gerar as suas próprias [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest) chamadas). 

O desempenho da aplicação tem um padrão de comportamento normal. Alguns pedidos ou chamadas de dependência serão mais suscetíveis a falha do que outros; e a taxa de falhas global pode ir cópias de segurança que a carga aumenta. Deteção inteligente utiliza a aprendizagem automática localizar estas anomalias.

Como é fornecido a telemetria no Application Insights da sua aplicação web, deteção inteligente compara o comportamento atual com os padrões vistos nos últimos alguns dias. Se um aumento anormal da taxa de falhas de ocupação observado by comparison with desempenho anterior, é acionada uma análise.

Quando uma análise é acionada, o serviço efetua uma análise do cluster de pedido falhado, para tentar identificar um padrão dos valores que caracterizam as falhas. No exemplo acima, a análise detetou que a maioria das falhas são sobre um código de resultado específico, nome do pedido, o anfitrião de URL do servidor e instância de função. Por outro lado, a análise detetou que a propriedade de sistema operativo de cliente é distribuída ao longo de vários valores e, por isso, não está listado.

Quando o seu serviço é equipado com destas chamadas de telemetria, o analyser procura uma exceção e uma falha de dependência que estão associados a pedidos do cluster que identificou, juntamente com um exemplo de quaisquer registos de rastreio associados a esses pedidos.

A análise resultante é enviada como alerta, a menos que tenha configurado não a.

Como o [alertas definir manualmente](app-insights-alerts.md), pode inspecionar o estado do alerta e configurá-lo no painel de alertas de recurso do Application Insights. Mas, ao contrário de outros alertas, não precisa de configurar ou configurar a deteção de inteligente. Se quiser, pode desativá-lo ou alterar os respetivos endereços de correio eletrónico de destino.

## <a name="configure-alerts"></a>Configurar alertas
Pode desativar a deteção inteligente, alterar os destinatários de e-mail, criar um webhook ou optar por mensagens de alerta mais detalhadas.

Abra a página de alertas. Falha de anomalias está incluído juntamente com todos os alertas que definiu manualmente e pode ver se está atualmente no estado de alerta.

![Na página Descrição geral, clique no mosaico alertas. Ou em qualquer página de métricas, clique no botão de alertas.](./media/app-insights-proactive-failure-diagnostics/021.png)

Clique no alerta para configurá-lo.

![Configuração](./media/app-insights-proactive-failure-diagnostics/032.png)

Tenha em atenção que pode desativar a deteção inteligente, mas que não é possível eliminá-lo (ou crie outro).

#### <a name="detailed-alerts"></a>Alertas de detalhado
Se selecionar "Obter um diagnóstico mais detalhado", em seguida, o e-mail irá conter mais informações de diagnóstico. Por vezes, poderá diagnosticar o problema apenas a partir dos dados no e-mail.

Não há um risco de pequena que o alerta mais detalhado pode conter informações confidenciais, pois inclui as mensagens de exceção e rastreio. No entanto, isto só sucederá se o seu código pode permitir a informações confidenciais para essas mensagens.

## <a name="triaging-and-diagnosing-an-alert"></a>A triagem e diagnosticar um alerta
Um alerta indica que foi detetado um aumento anormal da taxa de pedidos falhados. É provável que há um problema com a aplicação ou o seu ambiente.

De percentagem de pedidos e número de utilizadores afetados, pode decidir como urgente o problema é a. No exemplo acima, a taxa de falhas de 22.5% compara com uma taxa normal de 1%, indica que algo incorreto acontecimentos. Por outro lado, apenas 11 utilizadores foram afetados. Se fosse a sua aplicação, deverá conseguir avaliar como que é a gravidade.

Em muitos casos, conseguirá diagnosticar o problema rapidamente a partir do nome do pedido de exceção, dados de falha e o rastreio de dependência fornecidos.

Existem alguns outros processáveis. Por exemplo, a taxa de falhas de dependência neste exemplo é o mesmo que a taxa de exceção (89.3%). Isto sugere que a exceção seja diretamente a partir da falha de dependência - dando-lhe uma ideia limpar de onde pretende começar a procurar no seu código.

Para investigação mais aprofundada, as ligações em cada secção irão demorar simples para um [página procura](app-insights-diagnostic-search.md) filtrada para os pedidos relevantes, exceção, dependência ou rastreios. Ou pode abrir o [portal do Azure](https://portal.azure.com), navegue para o recurso do Application Insights para a sua aplicação e abrir o painel de falhas.

Neste exemplo, clicar na hiperligação 'Ver detalhes de falhas de dependência' abre o painel de pesquisa do Application Insights. Mostra a instrução de SQL que tenha um exemplo da causa raiz: nulos foram fornecidos em campos de preenchimento obrigatórios e não passou na validação durante a guardar a operação.

![Pesquisa de diagnóstico](./media/app-insights-proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Rever os alertas recentes

Clique em **deteção inteligente** para obter as mais recentes do alerta:

![Resumo de alertas](./media/app-insights-proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Qual é a diferença...
Inteligente deteção de anomalias de falha complementa outros semelhantes funcionalidades distintas, mas do Application Insights.

* [Alertas métricas](app-insights-alerts.md) são definidos por si e pode monitorizar uma vasta gama de métricas, tais como a ocupação da CPU, taxas de pedidos, tempos de carregamento de página e assim sucessivamente. Pode utilizá-los para o avisar, por exemplo, se precisar de adicionar mais recursos. Por outro lado, inteligente deteção de anomalias de falha abrange um intervalo pequeno de métricas crítico (atualmente apenas taxa de pedidos falhados), concebido para notificar o utilizador no quase em tempo real de forma assim que a sua aplicação web não conseguiu pedir taxa aumenta significativamente comparada com a aplicação de web comportamento normal.

    Deteção inteligente ajusta automaticamente o respetivo limiar em resposta às condições prevailing.

    Deteção inteligente inicia o trabalho de diagnóstico para si.
* [Deteção de anomalias de desempenho de smart](app-insights-proactive-performance-diagnostics.md) também utiliza máquina intelligence para detetar padrões invulgares na sua métricas e é necessária nenhuma configuração por si. Mas, ao contrário inteligente deteção de anomalias de falha, o objetivo inteligente deteção de anomalias de desempenho é localizem segmentos de sua manifold de utilização que pode ser servido incorretamente, por exemplo, por páginas específicas de um tipo específico de browser. A análise é executada diariamente e, se for encontrado qualquer resultado, é provável que haja muito menos urgente do que um alerta. Por outro lado, a análise para deteção de anomalias de falha é executada continuamente a telemetria de entrada e será notificado dentro de minutos se taxas de falha de servidor são maiores que o esperado.

## <a name="if-you-receive-a-smart-detection-alert"></a>Se receber um alerta de deteção inteligente
*Por que motivo receberam deste alerta?*

* Detetámos um aumento anormal da taxa de pedidos falhados em comparação comparada a linha de base normal do período de anterior. Depois da análise de falhas e telemetria associada, pensamos que há um problema que deverá procurar no.

*A notificação significa sem dúvida de ter um problema?*

* Vamos tentar alerta sobre interrupção de aplicação ou degradação, mas apenas pode compreender o impacto na aplicação ou os utilizadores e a semântica.

*Por isso, guys observar os meus dados?*

* Não. O serviço está totalmente automático. Apenas obter as notificações. Os seus dados são [privada](app-insights-data-retention-privacy.md).

*É necessário que subscrever este alerta?*

* Não. Todas as aplicações que envia telemetria de pedido tem a regra de alerta de deteção inteligente.

*Pode anular a subscrição ou obter as notificações enviadas para os meus colegas em vez disso?*

* Sim, as regras no alerta, clique a regra de deteção inteligente configurá-lo. Pode desativar o alerta ou alterar os destinatários para o alerta.

*Perdeu a mensagem de correio eletrónico. Onde posso encontrar as notificações no portal?*

* Nos registos de atividade. No Azure, abra o recurso do Application Insights para a sua aplicação, em seguida, selecione de registos de atividade.

*Alguns dos alertas são sobre problemas conhecidos e não a pretende receber.*

* Temos a supressão de alerta no nosso registo de segurança.

## <a name="next-steps"></a>Passos seguintes
Estas ferramentas de diagnóstico de ajudam a inspecionar a telemetria da sua aplicação:

* [Explorador de métrica](app-insights-metrics-explorer.md)
* [Explorador de pesquisa](app-insights-diagnostic-search.md)
* [Análise - linguagem de consulta poderosa](app-insights-analytics-tour.md)

Deteções inteligentes são totalmente automáticas. Mas talvez pretenda configurar alguns alertas mais?

* [Alertas de métricos configurados manualmente](app-insights-alerts.md)
* [Testes web de disponibilidade](app-insights-monitor-web-app-availability.md)
