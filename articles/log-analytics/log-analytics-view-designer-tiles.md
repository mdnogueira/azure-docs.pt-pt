---
title: "Mosaico de referência para o estruturador de vistas no OMS Log Analytics | Microsoft Docs"
description: "Estruturador de vistas no Log Analytics permite-lhe criar vistas personalizadas na consola do OMS que contêm visualizações diferentes dos dados no repositório de OMS. Este artigo fornece uma referência de definições para cada um dos mosaicos disponíveis para utilização na sua vistas personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 2bce5c63b4c6edd3753f1b234cc4f493dcf53dea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-view-designer-tile-reference"></a>Referência de mosaico do estruturador de vistas de análise de registo
O estruturador de vistas no Log Analytics permite-lhe criar vistas personalizadas na consola do OMS que contêm visualizações diferentes dos dados no repositório de OMS. Este artigo fornece uma referência de definições para cada um dos mosaicos disponíveis para utilização na sua vistas personalizadas.

Outros artigos disponíveis para o estruturador de vistas são:

* [Ver Designer](log-analytics-view-designer.md) -descrição geral do estruturador de vistas e procedimentos para criar e editar vistas personalizadas.
* [Referência de parte de visualização](log-analytics-view-designer-parts.md) -referência das definições para cada um dos mosaicos disponíveis para utilização na sua vistas personalizadas.

>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), e consultas em todas as vistas têm de ser escritas [novo idioma de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas as vistas que foram criadas para a área de trabalho foi atualizada será automtically convertido.

A tabela seguinte lista os diferentes tipos de mosaicos disponíveis o estruturador de vistas.  As secções abaixo descrevem cada tipo de mosaico detalhes e as respetivas propriedades.

| Mosaico | Descrição |
|:--- |:--- |
| [Número](#number-tile) |Número único que mostra a contagem de registos por uma consulta. |
| [Dois números](#two-numbers-tile) |Dois números único que mostra o número de registos de duas consultas diferentes. |
| [Anel](#donut-tile) |Gráfico de anel baseado numa consulta com um valor de resumo no centro do. |
| [Gráfico de linhas & chamada](#line-chart-amp-callout-tile) |Gráfico de linhas com base na consulta e uma chamada com um valor de resumo. |
| [Gráfico de linhas](#line-chart-tile) |Gráfico de linhas com base numa consulta. |
| [Duas linhas cronológicas](#two-timelines-tile) |Gráfico de colunas com cada baseada numa consulta separada de duas séries. |

## <a name="number-tile"></a>Mosaico de número
O **número** mosaico apresenta um número único que mostra a contagem de registos a partir de uma consulta de registo e uma etiqueta.

![Mosaico de número](media/log-analytics-view-designer/tile-number.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |Texto a apresentar na parte superior do mosaico. |
| Descrição |Texto a apresentar sob o nome do mosaico. |
| **Mosaico** | |
| Legenda |Texto a apresentar sob o valor. |
| Consulta |Consulta seja executada.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Avançadas** |**> Verificação de-fluxo de dados** |
| Ativado |Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período de temporário quando a vista está instalada e dados ficar disponíveis. |
| Consulta |Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta devolve não existem resultados, em seguida, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |Mensagem a apresentar se a consulta de verificação de fluxo de dados devolver sem dados.  Não se fornecer nenhuma mensagem *efetuar avaliação* é apresentado. |


## <a name="two-numbers-tile"></a>Mosaico de dois números
O **número dois** mosaico apresenta dois números que mostra a contagem de registos de duas consultas de registo diferentes e uma etiqueta para cada.

![Mosaico de dois números](media/log-analytics-view-designer/tile-two-numbers.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |Texto a apresentar na parte superior do mosaico. |
| Descrição |Texto a apresentar sob o nome do mosaico. |
| **Primeiro mosaico** | |
| Legenda |Texto a apresentar sob o valor. |
| Consulta |Consulta seja executada.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Mosaico segundo** | |
| Legenda |Texto a apresentar sob o valor. |
| Consulta |Consulta seja executada.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Avançadas** |**> Verificação de-fluxo de dados** |
| Ativado |Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período de temporário quando a vista está instalada e dados ficar disponíveis. |
| Consulta |Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta devolve não existem resultados, em seguida, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |Mensagem a apresentar se a consulta de verificação de fluxo de dados devolver sem dados.  Não se fornecer nenhuma mensagem *efetuar avaliação* é apresentado. |


## <a name="donut-tile"></a>Mosaico de anel
O **anel** mosaico apresenta um número único resumido a partir da coluna valor de uma consulta de registo.  O anel graficamente apresenta resultados dos registos de três principais.

![Mosaico de anel](media/log-analytics-view-designer/tile-donut.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |Texto a apresentar na parte superior do mosaico. |
| Descrição |Texto a apresentar sob o nome do mosaico. |
| **Anel** | |
| Consulta |Consulta de executar o anel.  A primeira propriedade deve ser um valor de texto e a segunda propriedade um valor numérico.  Isto é normalmente uma consulta que utiliza o **medidas** palavra-chave para resumir os resultados. |
| **Anel** |**> Center** |
| Texto |Texto a apresentar em valor dentro do anel. |
| Operação |A operação para efetuar a propriedade de valor para resumir para um valor único.<br><br>-Soma: Adicione os valores de todos os registos com o valor da propriedade.<br>-Percentagem: Percentagem dos valores summed de registos com o valor da propriedade em comparação comparada os valores summed de todos os registos. |
| Valores de resultado utilizados na operação do System center |Opcionalmente, clique no sinal para adicionar um ou mais valores.  Os resultados da consulta serão limitados para registos com os valores de propriedade que especificar.  Se não existem valores são adicionados, que todos os registos estão incluídos na consulta. |
| **Anel** |**> Opções adicionais** |
| Cores |A cor a apresentar para cada um dos três propriedades superiores.  Se pretender especificar cores alternativas para os valores de propriedade específica, em seguida, utilize avançadas mapeamento de cor. |
| Mapeamento de cor avançadas |Apresenta uma cor para os valores de propriedade específica.  Se tiver o valor que especifica os três principais, em seguida, a cor alternativa é apresentada em vez da cor do padrão.  Se a propriedade não está a ser os três principais, a cor não é apresentada. |
| **Avançadas** |**> Verificação de-fluxo de dados** |
| Ativado |Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período de temporário quando a vista está instalada e dados ficar disponíveis. |
| Consulta |Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta devolve não existem resultados, em seguida, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |Mensagem a apresentar se a consulta de verificação de fluxo de dados devolver sem dados.  Não se fornecer nenhuma mensagem *efetuar avaliação* é apresentado. |


## <a name="line-chart-tile"></a>Mosaico de gráfico de linha
O **gráfico de linhas** mosaico apresenta um gráfico de linhas com várias séries por uma consulta de registo ao longo do tempo.  

![Mosaico de gráfico de linhas & chamada](media/log-analytics-view-designer/tile-line-chart.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |Texto a apresentar na parte superior do mosaico. |
| Descrição |Texto a apresentar sob o nome do mosaico. |
| **Gráfico de linhas** | |
| Consulta |Consulta para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda propriedade um valor numérico.  Isto é normalmente uma consulta que utiliza o **medidas** palavra-chave para resumir os resultados.  Se a consulta utiliza a **intervalo** palavra-chave, em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não inclui o **intervalo** intervalos de palavra-chave e a hora a hora são utilizados para o eixo x. |
| **Gráfico de linhas** |**> Eixo Y** |
| Utilizar escala logarítmica |Selecione para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico indicar os tipos de valor e, opcionalmente, para converter os valores.  O **um tipo de unidade** Especifica a categoria da unidade e define o **atual um tipo de unidade** valores que estão disponíveis.  Se selecionar um valor na **converter** , em seguida, os valores numéricos são convertidos do **unidade atual** escreva para a **converter** tipo. |
| Etiqueta personalizada |Texto a apresentar para o eixo Y junto a etiqueta para o tipo de unidade.  Não se for especificada nenhuma etiqueta, em seguida, é apresentado apenas o tipo de unidade. |
| **Avançadas** |**> Verificação de-fluxo de dados** |
| Ativado |Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período de temporário quando a vista está instalada e dados ficar disponíveis. |
| Consulta |Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta devolve não existem resultados, em seguida, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |Mensagem a apresentar se a consulta de verificação de fluxo de dados devolver sem dados.  Não se fornecer nenhuma mensagem *efetuar avaliação* é apresentado. |


## <a name="line-chart--callout-tile"></a>Mosaico de gráfico & da chamada de linha
O **linha gráfico & chamada** mosaico apresenta um gráfico de linhas com várias séries por uma consulta de registo ao longo do tempo e uma chamada com um valor resumido.  

![Mosaico de gráfico de linhas & chamada](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |Texto a apresentar na parte superior do mosaico. |
| Descrição |Texto a apresentar sob o nome do mosaico. |
| **Gráfico de linhas** | |
| Consulta |Consulta para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda propriedade um valor numérico.  Isto é normalmente uma consulta que utiliza o **medidas** palavra-chave para resumir os resultados.  Se a consulta utiliza a **intervalo** palavra-chave, em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não inclui o **intervalo** intervalos de palavra-chave e a hora a hora são utilizados para o eixo x. |
| **Gráfico de linhas** |**> Chamada** |
| Chamada |Texto do título para apresentar superior ao valor da chamada. |
| Nome da série |Valor da propriedade para a série a utilizar para o valor da chamada.  Se não for fornecido nenhum série, todos os registos da consulta são utilizados. |
| Operação |A operação para efetuar a propriedade de valor para resumir para um valor único para a chamada.<br>-Média: A média do valor de todos os registos.<br><br>-Contagem: Contagem de todos os registos devolvidos pela consulta.<br>-Última amostra: Valor no último intervalo incluído no gráfico.<br>-Máximo: O valor máximo de intervalos incluído no gráfico.<br>-Mínimo: O valor mínimo de intervalos incluído no gráfico.<br>-Soma: Soma do valor de todos os registos. |
| **Gráfico de linhas** |**> Eixo Y** |
| Utilizar escala logarítmica |Selecione para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico indicar os tipos de valor e, opcionalmente, para converter os valores.  O **um tipo de unidade** Especifica a categoria da unidade e define o **atual um tipo de unidade** valores que estão disponíveis.  Se selecionar um valor na **converter** , em seguida, os valores numéricos são convertidos do **unidade atual** escreva para a **converter** tipo. |
| Etiqueta personalizada |Texto a apresentar para o eixo Y junto a etiqueta para o tipo de unidade.  Não se for especificada nenhuma etiqueta, em seguida, é apresentado apenas o tipo de unidade. |
| **Avançadas** |**> Verificação de-fluxo de dados** |
| Ativado |Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período de temporário quando a vista está instalada e dados ficar disponíveis. |
| Consulta |Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta devolve não existem resultados, em seguida, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |Mensagem a apresentar se a consulta de verificação de fluxo de dados devolver sem dados.  Não se fornecer nenhuma mensagem *efetuar avaliação* é apresentado. |


## <a name="two-timelines-tile"></a>Mosaico de duas linhas cronológicas
O **duas linhas cronológicas** mosaico apresenta os resultados das duas consultas de registo ao longo do tempo como gráficos de colunas.  É apresentada uma chamada para cada série.  

![Mosaico de duas linhas cronológicas](media/log-analytics-view-designer/tile-two-timelines.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |Texto a apresentar na parte superior do mosaico. |
| Descrição |Texto a apresentar sob o nome do mosaico. |
| Gráfico primeiro | |
| Legenda |Texto a apresentar na chamada para a série primeiro. |
| Cor |Cor a utilizar para as colunas da série primeiro. |
| Consulta de gráfico |Consulta seja executada para a série primeiro.  A contagem do número de registos ao longo de cada intervalo de tempo será representada pelas colunas de gráfico. |
| Operação |A operação para efetuar a propriedade de valor para resumir para um valor único para a chamada.<br><br>-Média: A média do valor de todos os registos.<br>-Contagem: Contagem de todos os registos devolvidos pela consulta.<br>-Última amostra: Valor no último intervalo incluído no gráfico.<br>-Máximo: O valor máximo de intervalos incluído no gráfico. |
| **Segundo gráfico** | |
| Legenda |Texto a apresentar na chamada para a série segundo. |
| Cor |Cor a utilizar para as colunas na série de segundo. |
| Consulta de gráfico |Consulta seja executada para a série segundo.  A contagem do número de registos ao longo de cada intervalo de tempo será representada pelas colunas de gráfico. |
| Operação |A operação para efetuar a propriedade de valor para resumir para um valor único para a chamada.<br><br>-Média: A média do valor de todos os registos.<br>-Contagem: Contagem de todos os registos devolvidos pela consulta.<br>-Última amostra: Valor no último intervalo incluído no gráfico.<br>-Máximo: O valor máximo de intervalos incluído no gráfico. |
| **Avançadas** |**> Verificação de-fluxo de dados** |
| Ativado |Selecione se a verificação de fluxo de dados deve ser ativada para o mosaico.  Isto fornece uma mensagem alternativa se dados não estão disponíveis para o mosaico.  Isto é normalmente utilizado para fornecer uma mensagem durante o período de temporário quando a vista está instalada e dados ficar disponíveis. |
| Consulta |Para executar para verificar se os dados estão disponíveis para a vista de consulta.  Se a consulta devolve não existem resultados, em seguida, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |Mensagem a apresentar se a consulta de verificação de fluxo de dados devolver sem dados.  Não se fornecer nenhuma mensagem *efetuar avaliação* é apresentado. |


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para suportar as consultas de mosaicos.
* Adicionar [partes de visualização](log-analytics-view-designer-parts.md) à vista personalizada.
