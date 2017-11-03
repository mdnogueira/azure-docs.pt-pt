---
title: "Ver ou analisar dados de análise de registos do Azure recolhidos | Microsoft Docs"
description: "Este artigo inclui um tutorial que descreve como criar registo pesquisas e analisar os dados armazenados no seu recurso de análise de registos com o portal de registo de pesquisa.  O tutorial inclui a execução de algumas consultas simples para devolver os diferentes tipos de dados e analisa os resultados."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: dfcbb925a16ca1e53d10b7bf70d03e62bc9dae69
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="view-or-analyze-data-collected-with-log-analytics-log-search"></a>Ver ou analisar os dados recolhidos com a pesquisa de registo de análise de registos

Na análise de registos pode tirar partido das pesquisas de registo por construir consultas para analisar os dados recolhidos, utilize pré-existente dashboards que pode personalizar com vistas gráficas da sua pesquisas mais importantes.  Agora que definiu a recolha de dados operacionais de VMs do Azure e os registos de atividade, este tutorial, a saber como:

> [!div class="checklist"]
> * Atualizar o recurso do Log Analytics do Azure para o novo idioma de consulta 
> * Efetuar uma procura simples de dados de eventos e utilizar as funcionalidades para modificar e filtrar os resultados 
> * Saiba como trabalhar com dados de desempenho

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente [ligado à área de trabalho de análise de registos](log-analytics-quick-collect-azurevm.md).  

Criação e edição de consultas, além de forma interativa a trabalhar com dados devolvidos, podem ser conseguido de duas maneiras.  Para consultas básicas, utilize a página de pesquisa de registo no portal do Azure ou para consultas avançadas, pode utilizar o portal de análise avançadas. Para saber mais sobre a diferença de funcionalidade entre dois portais, consulte [portais para criação e edição de consultas de registo no Log Analytics do Azure](log-analytics-log-search-portals.md)

Neste tutorial, iremos irá funcionar com a pesquisa de registo no portal do Azure. 

## <a name="log-in-to-azure-portal"></a>Inicie sessão no portal do Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="open-the-log-search-portal"></a>Abra o portal de registo de pesquisa 
Comece por abrir o portal de registo de pesquisa.   

1. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
2. No painel de subscrições de análise de registos, selecione uma área de trabalho e, em seguida, selecione o **pesquisa registo** mosaico.<br> ![Botão de procura de registo](media/log-analytics-tutorial-viewdata/azure-portal-01.png)

Poderá ter reparado a faixa na parte superior da sua página de recursos de análise de registos no portal do inviting a atualizar.<br> ![Análise de registos atualizar aviso no portal do Azure](media/log-analytics-tutorial-viewdata/log-analytics-portal-upgradebanner.png)

Análise de registos recentemente-lhe apresentado um novo idioma de consulta faz com que facilitam a construir a consulta, correlacionar dados a partir de várias origens e analisam a identificar rapidamente as tendências ou problemas.

A atualizar é simple.  Iniciar o processo clicando na faixa que indica que **saber mais e atualizar**.  Leia as informações adicionais sobre a atualização, na página de informações de atualização e, em seguida, clique em **atualizar agora**.

O processo demorará alguns minutos a concluir e durante este tempo, pode acompanhar o progresso em **notificações** no menu. Pode saber mais sobre o [vantagens do novo idioma de consulta](log-analytics-log-search-upgrade.md#why-the-new-language).

## <a name="create-a-simple-search"></a>Criar uma procura simples
Obter alguns dados para trabalhar com a forma mais rápida é uma consulta simples que devolve todos os registos na tabela.  Se tiver quaisquer clientes Windows ou Linux ligado à sua área de trabalho, em seguida, terá de dados do evento (Windows) ou tabela de Syslog (Linux).

Escreva um as seguintes consultas na caixa de pesquisa e clique no botão de procura.  

```
Event
```
```
Syslog
```

São devolvidos dados na vista de lista predefinido e pode ver o número total de registos foram devolvidas.

![Consulta simples](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-01.png)

São apresentadas apenas as primeiro algumas propriedades de cada registo.  Clique em **mostrar mais** para apresentar todas as propriedades para um determinado registo.

## <a name="filter-results-of-the-query"></a>Filtrar os resultados da consulta
No lado esquerdo do ecrã é o painel de filtro que lhe permite adicionar filtragem à consulta sem modificá-lo diretamente.  Várias propriedades de registos são apresentadas para esse tipo de registo e pode selecionar um ou mais valores de propriedade para restringir os resultados da pesquisa.

Se estiver a trabalhar com **eventos**, selecione a caixa de verificação junto a **erro** em **EVENTLEVELNAME**.   Se estiver a trabalhar com **Syslog**, selecione a caixa de verificação junto a **err** em **nível de GRAVIDADE**.  Muda a consulta para um dos seguintes para limitar os resultados para eventos de erro.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtro](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-02.png)

Adicionar propriedades para o painel de filtro selecionando **adicionar filtros** no menu de propriedade dos registos.

![Adicionar ao menu de filtro](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-03.png)

Pode definir o filtro mesmo selecionando **filtro** no menu de propriedade para um registo com o valor que pretende filtrar.  

Só tem do **filtro** opção para propriedades com os respetivos nomes azul quando paira o rato por cima deles.  Estes são *pesquisável* campos que são indexados para condições de pesquisa.  Campos cinzento são *livre texto pesquisável* campos que têm apenas o **mostrar referências** opção.  Esta opção devolve registos que possuem esse valor em qualquer propriedade.

Pode agrupar os resultados de uma única propriedade selecionando o **Agrupar por** opção no menu de registo.  Isto irá adicionar um [resumir](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) operador à sua consulta que apresenta os resultados de um gráfico.  Pode agrupar em mais do que uma propriedade, mas terá de editar a consulta diretamente.  Selecione o menu de registo junto ao **computador** propriedade e selecione **Agrupar por 'Computador'**.  

![Agrupar por computador](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-04.png)

## <a name="work-with-results"></a>Trabalhar com os resultados
O portal de registo de pesquisa tem uma variedade de funcionalidades para trabalhar com os resultados de uma consulta.  Pode ordenar, filtro e resultados de grupo para analisar os dados sem modificar a consulta real.  Resultados de uma consulta não são ordenados por predefinição.

Para ver os dados no formato de tabela que fornece opções adicionais para filtrar e ordenar, clique em **tabela**.  

![Vista em tabela](media/log-analytics-tutorial-viewdata/log-search-portal-table-01.png)

Clique na seta por um registo para ver os detalhes para esse registo.

![Resultados de ordenação](media/log-analytics-tutorial-viewdata/log-search-portal-table-02.png)

Ordenação em qualquer campo ao clicar no respectivo cabeçalho de coluna.

![Resultados de ordenação](media/log-analytics-tutorial-viewdata/log-search-portal-table-03.png)

Filtre os resultados de um valor na coluna específico ao clicar no botão Filtro e fornecer uma condição de filtro.

![Resultados do filtro](media/log-analytics-tutorial-viewdata/log-search-portal-table-04.png)

Grupo numa coluna, arrastando o cabeçalho de coluna na parte superior dos resultados.  Pode agrupar vários campos arrastando várias colunas na parte superior.

![Resultados de grupo](media/log-analytics-tutorial-viewdata/log-search-portal-table-05.png)


## <a name="work-with-performance-data"></a>Trabalhar com dados de desempenho
Dados de desempenho de agentes do Windows e Linux são armazenados na área de trabalho de análise de registos no **desempenho** tabela.  Registos de desempenho mais semelhante a qualquer outro registo e, vamos escrever uma consulta simple que devolve todos os registos de desempenho, tal como com eventos.

```
Perf
```

![Dados de desempenho](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-01.png)

Embora a devolver milhões de registos para todos os objetos de desempenho e contadores não é muito útil.  Pode utilizar os mesmos métodos que utilizou acima para filtrar os dados ou escreva a seguinte consulta diretamente na caixa de pesquisa de registo.  Esta ação devolve apenas processador registos de utilização para computadores Windows e Linux.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Utilização do processador](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-02.png)

Isto limita os dados para um contador específico, mas ainda não colocá-la num formulário que é particularmente útil.  Pode apresentar os dados no gráfico de linhas, mas primeiro tem de agrupá-lo por computador e TimeGenerated.  Para agrupar em vários campos, terá de modificar a consulta diretamente, por isso, modifique a consulta para o seguinte.  Esta opção utiliza o [médio](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) funcionar o **CounterValue** propriedade para calcular o valor médio ao longo de cada hora.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Gráfico de dados de desempenho](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-03.png)

Agora que os dados deverá são agrupados, pode apresentar-um gráfico visual adicionando o [compor](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) operador.  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Gráfico de linhas](media/log-analytics-tutorial-viewdata/log-analytics-portal-linechart-01.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar pesquisas de registo básico para analisar dados de eventos e desempenho.  Avançar para o próximo tutorial para saber como visualizar os dados através da criação de um dashboard.

> [!div class="nextstepaction"]
> [Criar e partilhar dashboards de análise de registos](log-analytics-tutorial-dashboards.md)