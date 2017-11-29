---
title: "Azure folha de referência do idioma de consulta de análise de registos | Microsoft Docs"
description: "Este artigo fornece assistência sobre a transição para o novo idioma de consulta de análise de registos, se já estiver familiarizado com o idioma de legado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.openlocfilehash: 9c487ab33859ae453a0074ef0344f61de19c7b4d
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Transição para o novo idioma de consulta de análise de registos do Azure
Análise de registos recentemente implementado um novo idioma de consulta.  Este artigo fornece assistência sobre a transição neste idioma para análise de registos, se já estiver familiarizado com o idioma de legado e ainda precisa de algumas assistência.

## <a name="resources"></a>Recursos


## <a name="language-converter"></a>Conversor de idioma

Se estiver familiarizado com o idioma de consulta de análise de registos legado, é a forma mais fácil para criar a mesma consulta num novo idioma a utilizar o conversor de idioma instalado no portal de registo de pesquisa para a sua área de trabalho é convertida.  Utilizar o conversor é tão simple como escrever uma consulta legada na caixa de texto superior e, em seguida, clicando em **converter**.  Ou pode clicar no botão de procura para executar a consulta ou copiar e colar para utilizá-lo algures pessoa.

![Conversor de idioma](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>Recursos
O [site de documentação para o idioma de consulta de análise do registo](https://docs.loganalytics.io) tem todos os recursos tem de ficar previsto num novo idioma.  Isto inclui tutoriais, exemplos e uma referência de linguagem concluída.


## <a name="cheat-sheet"></a>Nota

A tabela seguinte fornece uma comparação entre uma variedade de consultas comuns para comandos equivalentes entre o idioma de consulta novo e legado no Log Analytics do Azure.

| Descrição | Legado | novo |
|:--|:--|:--|
| Todas as tabelas de pesquisa      | erro | procurar "error" (não sensível confidencial) |
| Selecione os dados da tabela | Tipo de evento de = |  Evento |
|                        | Tipo = eventos &#124; Selecione a origem, o registo de eventos, EventID | Evento &#124; EventID de origem, o registo de eventos, do projeto |
|                        | Tipo = eventos &#124; primeiros 100 | Evento &#124; tirar 100 |
| Comparação de cadeias      | Tipo = Computer=srv01.contoso.com de eventos   | Evento &#124; onde computador = = "srv01.contoso.com" |
|                        | Tipo = Computer=contains("contoso") de eventos | Evento &#124; em que o computador contém "contoso" (não sensível confidencial)<br>Evento &#124; onde computador contains_cs "Contoso" (sensível às maiúsculas e) |
|                        | Tipo = evento computador = RegEx ("@contoso@")  | Evento &#124; em que o computador com regex ". *contoso*" |
| Comparação de data        | Tipo = eventos TimeGenerated > 1DAYS agora | Evento &#124; onde TimeGenerated > ago(1d) |
|                        | Tipo = eventos TimeGenerated > 2017-05-01 TimeGenerated < 2017-05-31 | Evento &#124; onde TimeGenerated entre (datetime(2017-05-01). datetime(2017-05-31)) |
| Comparação booleana     | Tipo = Heartbeat IsGatewayInstalled = false  | Heartbeat \| onde IsGatewayInstalled = = false |
| Ordenar                   | Tipo = eventos &#124; Ordenar asc do computador, desc de registo de eventos, EventLevelName asc | Evento \| Ordenar por computador asc, desc de registo de eventos, EventLevelName asc |
| Distintos               | Tipo = eventos &#124; a eliminação de duplicados computador \| Selecione o computador | Evento &#124; Resumir por computador, o registo de eventos |
| Expandir colunas         | Tipo = desempenho CounterName = "% tempo do processador" &#124; EXPANDA if(map(CounterValue,0,50,0,1),"HIGH","LOW") como utilização | Desempenho &#124; onde CounterName = = "% tempo do processador" \| expandir a utilização = iff (CounterValue > 50, "Alta", "Baixa") |
| Agregação            | Tipo = eventos &#124; medida existente como contagem por computador | Evento &#124; resumir contagem = existente pelo computador |
|                                | Tipo = desempenho ObjectName = processador CounterName = "% tempo do processador" &#124; medidas avg(CounterValue) por intervalo de computador 5 minutos | Desempenho &#124; onde ObjectName = = "Processador" e CounterName = = "% tempo do processador" &#124; resumir avg(CounterValue) por computador, bin (TimeGenerated, 5min) |
| Agregação de limite | Tipo = eventos &#124; medida existente pelo computador &#124; 10 principais | Evento &#124; resumir AggregatedValue = existente pelo computador &#124; limite de 10 |
| União                  | Tipo = eventos ou tipo = Syslog | União evento Syslog |
| Associar                   | Tipo = NetworkMonitoring &#124; associação interna AgentIP (tipo = Heartbeat) ComputerIP | NetworkMonitoring &#124; tipo de associação = interna (tipo de pesquisa = = "Heartbeat") no $left. AgentIP = = $right.ComputerIP |



## <a name="next-steps"></a>Passos seguintes
- Veja uma [tutorial sobre como escrever consultas](https://go.microsoft.com/fwlink/?linkid=856078) utilizando a linguagem de consulta de novo.
- Consulte o [referência de linguagem de consulta](https://go.microsoft.com/fwlink/?linkid=856079) para obter detalhes sobre todos os comandos, operadores e funções para o novo idioma de consulta.  
