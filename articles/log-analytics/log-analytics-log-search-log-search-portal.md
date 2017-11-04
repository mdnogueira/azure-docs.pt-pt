---
title: Utilizar o portal de registo de pesquisa no Log Analytics do Azure | Microsoft Docs
description: "Este artigo inclui um tutorial que descreve como criar registo pesquisas e analisar os dados armazenados na sua área de trabalho de análise de registos com o portal de registo de pesquisa.  O tutorial inclui a execução de algumas consultas simples para devolver os diferentes tipos de dados e analisa os resultados."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: bwren
ms.openlocfilehash: 6fc556ceb34cde26d5f3789a2397cdaa34b0b84d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Criar registo pesquisas na análise de registos do Azure utilizando o portal de registo de pesquisa

> [!NOTE]
> Este artigo descreve o portal de registo de pesquisa na análise de registos do Azure utilizando a linguagem de consulta de novo.  Pode saber mais sobre o novo idioma e obter o procedimento para atualizar a sua área de trabalho em [atualizar a sua área de trabalho do Log Analytics do Azure para a pesquisa do novo registo](log-analytics-log-search-upgrade.md).  
>
> Se a sua área de trabalho não foi atualizada para o novo idioma de consulta, devem consultar a [localizar dados através de pesquisas de registo na análise de registos](log-analytics-log-searches.md) para obter informações sobre a versão atual do portal de registo de pesquisa.

Este artigo inclui um tutorial que descreve como criar registo pesquisas e analisar os dados armazenados na sua área de trabalho de análise de registos com o portal de registo de pesquisa.  O tutorial inclui a execução de algumas consultas simples para devolver os diferentes tipos de dados e analisa os resultados.  Concentra-se nas funcionalidades no portal de registo de pesquisa para modificar a consulta, em vez de diretamente a modificar.  Para obter detalhes sobre a editar diretamente a consulta, consulte o [referência de linguagem de consulta](https://go.microsoft.com/fwlink/?linkid=856079).

Para criar pesquisas no portal da análise avançada em vez do portal de pesquisa de registo, consulte [começar a utilizar o Portal da análise](https://go.microsoft.com/fwlink/?linkid=856587).  Ambos os portais de utilizam a mesma linguagem de consulta para aceder aos mesmos dados, na área de trabalho de análise de registos.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial parte do princípio de que já tem uma área de trabalho de análise de registos com pelo menos uma origem ligada que gera dados para as consultas analisar.  

- Se não tiver uma área de trabalho, pode criar um livre utilizando o procedimento em [começar com uma área de trabalho de análise de registos](log-analytics-get-started.md).
- Ligar pelo menos, um [agente do Windows](log-analytics-windows-agents.md) ou um [agente Linux](log-analytics-linux-agents.md) à área de trabalho.  

## <a name="open-the-log-search-portal"></a>Abra o portal de registo de pesquisa
Comece por abrir o portal de registo de pesquisa.  Pode aceder no portal do Azure ou o portal do OMS.

1. Abra o portal do Azure.
2. Navegue até à análise de registos e selecione a sua área de trabalho.
3. Selecione **pesquisa registo** para se manter no portal do Azure ou iniciar o portal do OMS selecionando **Portal do OMS** e, em seguida, clicando no botão de procura de registo.

![Botão de procura de registo](media/log-analytics-log-search-log-search-portal/log-search-button.png)

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

![Consulta simples](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

São apresentadas apenas as primeiro algumas propriedades de cada registo.  Clique em **mostrar mais** para apresentar todas as propriedades para um determinado registo.

![Detalhes do registo](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Definir o âmbito de tempo
Cada registo recolhido pelo Log Analytics tem uma **TimeGenerated** propriedade que contém a data e hora em que o registo foi criado.  Uma consulta no portal de registo de pesquisa devolve apenas os registos com um **TimeGenerated** dentro do âmbito de tempo que é apresentado no lado esquerdo do ecrã.  

Pode alterar o filtro de tempo, selecionando a lista pendente ou modificando o controlo de deslize.  O controlo de deslize apresenta um gráfico de barras que mostra o número de registos para cada segmento de tempo dentro do intervalo relativo.  Este segmento irá variar consoante o intervalo.

O âmbito de tempo predefinido é **1 dia**.  Altere este valor para **7 dias**, e deve aumentar o número total de registos.

![Data hora âmbito](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtrar os resultados da consulta
No lado esquerdo do ecrã é o painel de filtro que lhe permite adicionar filtragem à consulta sem modificá-lo diretamente.  São apresentadas várias propriedades de registos devolvidos com os respetivos valores de dez principais com maior número de registo.

Se estiver a trabalhar com **eventos**, selecione a caixa de verificação junto a **erro** em **EVENTLEVELNAME**.   Se estiver a trabalhar com **Syslog**, selecione a caixa de verificação junto a **err** em **nível de GRAVIDADE**.  Muda a consulta para um dos seguintes para limitar os resultados para eventos de erro.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Adicionar propriedades para o painel de filtro selecionando **adicionar filtros** no menu de propriedade dos registos.

![Adicionar ao menu de filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

Pode definir o filtro mesmo selecionando **filtro** no menu de propriedade para um registo com o valor que pretende filtrar.  

Só tem do **filtro** opção para propriedades com os respetivos nomes azul.  Estes são *pesquisável* campos que são indexados para condições de pesquisa.  Campos cinzento são *livre texto pesquisável* campos que têm apenas o **mostrar referências** opção.  Esta opção devolve registos que possuem esse valor em qualquer propriedade.

![Menu de filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Pode agrupar os resultados de uma única propriedade selecionando o **Agrupar por** opção no menu de registo.  Isto irá adicionar um [resumir](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) operador à sua consulta que apresenta os resultados de um gráfico.  Pode agrupar em mais do que uma propriedade, mas terá de editar a consulta diretamente.  Selecione o menu de registo junto ao **computador** propriedade e selecione **Agrupar por 'Computador'**.  

![Agrupar por computador](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Trabalhar com os resultados
O portal de registo de pesquisa tem uma variedade de funcionalidades para trabalhar com os resultados de uma consulta.  Pode ordenar, filtro e resultados de grupo para analisar os dados sem modificar a consulta real.  Resultados de uma consulta não são ordenados por predefinição.

Para ver os dados no formato de tabela que fornece opções adicionais para filtrar e ordenar, clique em **tabela**.  

![Vista em tabela](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Clique na seta por um registo para ver os detalhes para esse registo.

![Resultados de ordenação](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Ordenação em qualquer campo ao clicar no respectivo cabeçalho de coluna.

![Resultados de ordenação](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Filtre os resultados de um valor na coluna específico ao clicar no botão Filtro e fornecer uma condição de filtro.

![Resultados do filtro](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Grupo numa coluna, arrastando o cabeçalho de coluna na parte superior dos resultados.  Pode agrupar vários campos arrastando várias colunas na parte superior.

![Resultados de grupo](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Trabalhar com dados de desempenho
Dados de desempenho de agentes do Windows e Linux são armazenados na área de trabalho de análise de registos no **desempenho** tabela.  Registos de desempenho mais semelhante a qualquer outro registo e iremos pode escrever uma consulta simples que devolve todos os registos de desempenho, tal como com eventos.

```
Perf
```

![Dados de desempenho](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

Embora a devolver milhões de registos para todos os objetos de desempenho e contadores não é muito útil.  Pode utilizar os mesmos métodos que utilizou acima para filtrar os dados ou escreva a seguinte consulta diretamente na caixa de pesquisa de registo.  Esta ação devolve apenas processador registos de utilização para computadores Windows e Linux.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Utilização do processador](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

Isto limita os dados para um contador específico, mas ainda não colocá-la num formulário que é particularmente útil.  Pode apresentar os dados no gráfico de linhas, mas primeiro tem de agrupá-lo por computador e TimeGenerated.  Para agrupar em vários campos, terá de modificar a consulta diretamente, por isso, modifique a consulta para o seguinte.  Esta opção utiliza o [médio](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) funcionar o **CounterValue** propriedade para calcular o valor médio ao longo de cada hora.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Gráfico de dados de desempenho](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Agora que os dados deverá são agrupados, pode apresentar-um gráfico visual adicionando o [compor](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) operador.  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Gráfico de linhas](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o idioma de consulta de análise de registos em [começar a utilizar o Portal da análise](https://go.microsoft.com/fwlink/?linkid=856079).
- Percorrer um tutorial, utilizando o [portal da análise avançadas](https://go.microsoft.com/fwlink/?linkid=856587) que lhe permite executar as mesmas consultas e aceder os mesmos dados que o portal de registo de pesquisa.
