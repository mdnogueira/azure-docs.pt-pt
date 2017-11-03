---
title: "Parte de referência para o estruturador de vistas no OMS Log Analytics | Microsoft Docs"
description: "Estruturador de vistas no Log Analytics permite-lhe criar vistas personalizadas na consola do OMS que contêm visualizações diferentes dos dados no repositório de OMS. Este artigo fornece uma referência de definições para cada uma das partes de visualização disponíveis para utilização na sua vistas personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 40a6101576708936404447576d704a49666143fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-view-designer-visualization-part-reference"></a>Referência de parte de visualização do estruturador de vistas de análise de registo
O estruturador de vistas no Log Analytics permite-lhe criar vistas personalizadas na consola do OMS que contêm diferentes visualizações de dados do repositório OMS. Este artigo fornece uma referência de definições para cada uma das partes de visualização disponíveis para utilização na sua vistas personalizadas.

Outros artigos disponíveis para o estruturador de vistas são:

* [Ver Designer](log-analytics-view-designer.md) -descrição geral do estruturador de vistas e procedimentos para criar e editar vistas personalizadas.
* [Mosaico referência](log-analytics-view-designer-tiles.md) -referência das definições para cada um dos mosaicos disponíveis para utilização na sua vistas personalizadas.

>[!NOTE]
> Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), e consultas em todas as vistas têm de ser escritas [novo idioma de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas as vistas que foram criadas para a área de trabalho foi atualizada será automtically convertido.

A tabela seguinte descreve os diferentes tipos de mosaicos disponíveis o estruturador de vistas.  As secções abaixo descrevem cada tipo de mosaico detalhes e as respetivas propriedades.

| Tipo de vista | Descrição |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Mostra uma lista de consultas de pesquisa de registo.  O utilizador pode clicar em cada consulta para ver os respetivos resultados. |
| [Número & lista](#number-amp-list-part) |Cabeçalho tem um único número que mostra número de registos por uma consulta de pesquisa de registo.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo. |
| [Dois números & lista](#two-numbers-amp-list-part) |Cabeçalho tem dois números que mostra a contagem de registos de consultas de pesquisa de registo separados.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo. |
| [& Lista dos anel](#donut-amp-list-part) |Cabeçalho apresenta um número único resumido a partir da coluna valor de uma consulta de registo.  O anel graficamente apresenta resultados dos registos de três principais. |
| [Duas linhas cronológicas & lista](#two-timelines-amp-list-part) |Cabeçalho apresenta os resultados das duas consultas de registo ao longo do tempo como gráficos de colunas com uma chamada apresentar um único número resumido a partir da coluna valor de uma consulta de registo.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo. |
| [Informações](#information-part) |Cabeçalho mostra texto estático e uma ligação opcional.  Lista apresenta um ou mais itens com texto estático e título. |
| [Gráfico de linhas, chamada & lista](#line-chart-callout-amp-list-part) |Cabeçalho apresenta um gráfico de linhas com várias séries por uma consulta de registo ao longo do tempo e uma chamada com um valor resumido.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo. |
| [Gráfico de linhas & lista](#line-chart-amp-list-part) |Cabeçalho apresenta um gráfico de linhas com várias séries por uma consulta de registo ao longo do tempo.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo. |
| [Pilha da peça de gráficos de linha](#stack-of-line-charts-part) |Apresenta os três separado gráficos de linhas com várias séries por uma consulta de registo ao longo do tempo. |

## <a name="list-of-queries-part"></a>Lista de parte de consultas
Mostra uma lista de consultas de pesquisa de registo.  O utilizador pode clicar em cada consulta para ver os respetivos resultados.  A vista incluirá uma única consulta por predefinição e pode clicar em **+ consulta** adicionar consultas adicionais.

![Lista de vista de consultas](media/log-analytics-view-designer/view-list-queries.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título |Texto a apresentar na parte superior da vista. |
| Novo grupo |Selecione para criar um novo grupo na vista de começando vista atual. |
| Filtros previamente selecionados |Lista de propriedades para incluir no painel de filtro esquerda quando o utilizador seleciona uma consulta delimitada por vírgulas. |
| Modo de composição |Vista inicial apresentada quando a consulta estiver selecionada.  O utilizador pode selecionar qualquer vistas disponíveis depois de abrir a consulta. |
| **Consultas** | |
| Consulta de pesquisa |Consulta seja executada. |
| Nome amigável |Nome descritivo da consulta para apresentar ao utilizador. |

## <a name="number--list-part"></a>Número & lista parte
Cabeçalho tem um único número que mostra número de registos por uma consulta de pesquisa de registo.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo.

![Lista de vista de consultas](media/log-analytics-view-designer/view-number-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |Texto a apresentar na parte superior da vista. |
| Novo grupo |Selecione para criar um novo grupo na vista de começando vista atual. |
| Ícone |Ficheiro de imagem a apresentar junto o resultado no cabeçalho. |
| Ícone de utilização |Selecione para ter a apresentação de ícone. |
| **Título** | |
| Legenda |Texto a apresentar na parte superior do cabeçalho. |
| Consulta |Consulta seja executada para o cabeçalho.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Lista** | |
| Consulta |Consulta para a lista.  Serão apresentadas as primeiras duas propriedades dos registos primeiro dez nos resultados da.  A primeira propriedade deve ser um valor de texto e a segunda propriedade um valor numérico.  Barras são automaticamente criadas com base no valor da coluna numérica relativo.<br><br>Utilize o comando de ordenação na consulta para ordenar os registos na lista.  O utilizador pode clicar em ver todos os para executar a consulta e devolver todos os registos. |
| Ocultar gráfico |Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar sparklines |Selecione esta opção para apresentar o gráfico sparkline em vez de barras horizontais.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Cor |Cor de barras ou sparklines. |
| Nome e valor de separação |Delimitador de carácter único se pretender analisar a propriedade de texto em vários valores.  Consulte [definições comuns](#name-value-separator) para obter mais detalhes. |
| Consulta de navegação |Consulta para ser executada quando o utilizador seleciona um item na lista.  Consulte [definições comuns](#navigation-query) para obter mais detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a apresentar na parte superior da coluna primeiro da lista. |
| Valor |Texto a apresentar na parte superior da coluna segundo da lista. |
| **Lista** |**> Limiares** |
| Ativar os limiares |Selecione esta opção para ativar os limiares.  Consulte [definições comuns](#thresholds) para obter mais detalhes. |

## <a name="two-numbers--list-part"></a>Dois números & parte da lista
Cabeçalho tem dois números que mostra a contagem de registos de consultas de pesquisa de registo separados.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo.

![& Vista de lista de dois números](media/log-analytics-view-designer/view-two-numbers-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |Texto a apresentar na parte superior da vista. |
| Novo grupo |Selecione para criar um novo grupo na vista de começando vista atual. |
| Ícone |Ficheiro de imagem a apresentar junto o resultado no cabeçalho. |
| Ícone de utilização |Selecione para ter a apresentação de ícone. |
| **Título** | |
| Legenda |Texto a apresentar na parte superior do cabeçalho. |
| Consulta |Consulta seja executada para o cabeçalho.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| **Lista** | |
| Consulta |Consulta para a lista.  Serão apresentadas as primeiras duas propriedades dos registos primeiro dez nos resultados da.  A primeira propriedade deve ser um valor de texto e a segunda propriedade um valor numérico.  Barras são automaticamente criadas com base no valor da coluna numérica relativo.<br><br>Utilize o comando de ordenação na consulta para ordenar os registos na lista.  O utilizador pode clicar em ver todos os para executar a consulta e devolver todos os registos. |
| Ocultar gráfico |Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar sparklines |Selecione esta opção para apresentar o gráfico sparkline em vez de barras horizontais.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Cor |Cor de barras ou sparklines. |
| Operação |Operação de efetuar para o gráfico sparkline.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Nome e valor de separação |Delimitador de carácter único se pretender analisar a propriedade de texto em vários valores.  Consulte [definições comuns](#name-value-separator) para obter mais detalhes. |
| Consulta de navegação |Consulta para ser executada quando o utilizador seleciona um item na lista.  Consulte [definições comuns](#navigation-query) para obter mais detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a apresentar na parte superior da coluna primeiro da lista. |
| Valor |Texto a apresentar na parte superior da coluna segundo da lista. |
| **Lista** |**> Limiares** |
| Ativar os limiares |Selecione esta opção para ativar os limiares.  Consulte [definições comuns](#thresholds) para obter mais detalhes. |

## <a name="donut--list-part"></a>Parte de anel & lista
Cabeçalho apresenta um número único resumido a partir da coluna valor de uma consulta de registo.  O anel graficamente apresenta resultados dos registos de três principais.

![Vista de anel & lista](media/log-analytics-view-designer/view-donut-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |Texto a apresentar na parte superior do mosaico. |
| Novo grupo |Selecione para criar um novo grupo na vista de começando vista atual. |
| Ícone |Ficheiro de imagem a apresentar junto o resultado no cabeçalho. |
| Ícone de utilização |Selecione para ter a apresentação de ícone. |
| **Cabeçalho** | |
| Título |Texto a apresentar na parte superior do cabeçalho. |
| Subtítulo do |Texto a apresentar sob o título na parte superior do cabeçalho. |
| **Anel** | |
| Consulta |Consulta de executar o anel.  A primeira propriedade deve ser um valor de texto e a segunda propriedade um valor numérico. |
| **Anel** |**> Center** |
| Texto |Texto a apresentar em valor dentro do anel. |
| Operação |A operação para efetuar a propriedade de valor para resumir para um valor único.<br><br>-Soma: Adicione os valores de todos os registos.<br>-Percentagem: Percentagem dos registos devolvido pelos valores na **resultar valores utilizados na operação do Centro de** para o total de registos na consulta. |
| Valores de resultado utilizados na operação do System center |Opcionalmente, clique no sinal para adicionar um ou mais valores.  Os resultados da consulta serão limitados para registos com os valores de propriedade que especificar.  Se não existem valores são adicionados, todos os registos estão incluídos na consulta. |
| **Opções adicionais** |**> Cores** |
| Cor 1<br>Cor 2<br>Cor 3 |Selecione a cor para cada um dos valores apresentados no anel. |
| **Opções adicionais** |**> Mapeamento de cor avançadas** |
| Valor do campo |Escreva o nome de um campo para apresentá-la como uma cor diferente se está incluído no anel. |
| Cor |Selecione a cor para o campo exclusivo. |
| **Lista** | |
| Consulta |Consulta para a lista.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| Ocultar gráfico |Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar sparklines |Selecione esta opção para apresentar o gráfico sparkline em vez de barras horizontais.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Cor |Cor de barras ou sparklines. |
| Operação |Operação de efetuar para o gráfico sparkline.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Nome e valor de separação |Delimitador de carácter único se pretender analisar a propriedade de texto em vários valores.  Consulte [definições comuns](#name-value-separator) para obter mais detalhes. |
| Consulta de navegação |Consulta para ser executada quando o utilizador seleciona um item na lista.  Consulte [definições comuns](#navigation-query) para obter mais detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a apresentar na parte superior da coluna primeiro da lista. |
| Valor |Texto a apresentar na parte superior da coluna segundo da lista. |
| **Lista** |**> Limiares** |
| Ativar os limiares |Selecione esta opção para ativar os limiares.  Consulte [definições comuns](#thresholds) para obter mais detalhes. |

## <a name="two-timelines--list-part"></a>Dois parte linhas cronológicas & lista
Cabeçalho apresenta os resultados das duas consultas de registo ao longo do tempo como gráficos de colunas com uma chamada apresentar um único número resumido a partir da coluna valor de uma consulta de registo.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo.

![Duas linhas cronológicas & lista ver](media/log-analytics-view-designer/view-two-timelines-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |Texto a apresentar na parte superior do mosaico. |
| Novo grupo |Selecione para criar um novo grupo na vista de começando vista atual. |
| Ícone |Ficheiro de imagem a apresentar junto o resultado no cabeçalho. |
| Ícone de utilização |Selecione para ter a apresentação de ícone. |
| **Primeiro gráfico<br>segundo gráfico** | |
| Legenda |Texto a apresentar na chamada para a série primeiro. |
| Cor |Cor a utilizar para as colunas da série. |
| Consulta |Consulta seja executada para a série primeiro.  A contagem do número de registos ao longo de cada intervalo de tempo será representada pelas colunas de gráfico. |
| Operação |A operação para efetuar a propriedade de valor para resumir para um valor único para a chamada.<br><br>-Soma: Soma do valor de todos os registos.<br>-Média: A média do valor de todos os registos.<br>-Última amostra: Valor no último intervalo incluído no gráfico.<br>-O primeiro exemplo: O valor do primeiro intervalo de incluído no gráfico.<br>-Contagem: Contagem de todos os registos devolvidos pela consulta. |
| **Lista** | |
| Consulta |Consulta para a lista.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| Ocultar gráfico |Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar sparklines |Selecione esta opção para apresentar o gráfico sparkline em vez de barras horizontais.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Cor |Cor de barras ou sparklines. |
| Operação |Operação de efetuar para o gráfico sparkline.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Consulta de navegação |Consulta para ser executada quando o utilizador seleciona um item na lista.  Consulte [definições comuns](#navigation-query) para obter mais detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a apresentar na parte superior da coluna primeiro da lista. |
| Valor |Texto a apresentar na parte superior da coluna segundo da lista. |
| **Lista** |**> Limiares** |
| Ativar os limiares |Selecione esta opção para ativar os limiares.  Consulte [definições comuns](#thresholds) para obter mais detalhes. |

## <a name="information-part"></a>Parte de informações
Cabeçalho mostra texto estático e uma ligação opcional.  Lista apresenta um ou mais itens com texto estático e título.

![Vista de informações](media/log-analytics-view-designer/view-information.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |Texto a apresentar na parte superior do mosaico. |
| Novo grupo |Selecione para criar um novo grupo na vista de começando vista atual. |
| Cor |Cor de fundo do cabeçalho. |
| **Cabeçalho** | |
| Imagem |Ficheiro de imagem a apresentar no cabeçalho. |
| Etiqueta |Texto a apresentar no cabeçalho. |
| **Cabeçalho** |**> Ligação de** |
| Etiqueta |Texto da hiperligação. |
| Url |URL da ligação. |
| **Itens de informações** | |
| Título |Texto a apresentar para o título de cada item. |
| Conteúdo |Texto a apresentar para cada item. |

## <a name="line-chart-callout--list-part"></a>Gráfico de linhas, chamada & parte da lista
Cabeçalho apresenta um gráfico de linhas com várias séries por uma consulta de registo ao longo do tempo e uma chamada com um valor resumido.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo.

![Gráfico de linhas, chamada & vista de lista](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |Texto a apresentar na parte superior do mosaico. |
| Novo grupo |Selecione para criar um novo grupo na vista de começando vista atual. |
| Ícone |Ficheiro de imagem a apresentar junto o resultado no cabeçalho. |
| Ícone de utilização |Selecione para ter a apresentação de ícone. |
| **Cabeçalho** | |
| Título |Texto a apresentar na parte superior do cabeçalho. |
| Subtítulo do |Texto a apresentar sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |Consulta para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda propriedade um valor numérico.  Isto é normalmente uma consulta que utiliza o **medidas** palavra-chave para resumir os resultados.  Se a consulta utiliza a **intervalo** palavra-chave, em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não inclui o **intervalo** intervalos de palavra-chave e a hora a hora são utilizados para o eixo x. |
| **Gráfico de linhas** |**> Chamada** |
| Título da chamada |Texto a apresentar superior ao valor da chamada. |
| Nome da série |Valor da propriedade para a série a utilizar para o valor da chamada.  Se não for fornecido nenhum série, todos os registos da consulta são utilizados. |
| Operação |A operação para efetuar a propriedade de valor para resumir para um valor único para a chamada.<br><br>-Média: A média do valor de todos os registos.<br>-Contagem de contagem de todos os registos devolvidos pela consulta.<br>-Última amostra: Valor no último intervalo incluído no gráfico.<br>-Máximo: O valor máximo de intervalos incluído no gráfico.<br>-Mínimo: O valor mínimo de intervalos incluído no gráfico.<br>-Soma: Soma do valor de todos os registos. |
| **Gráfico de linhas** |**> Eixo Y** |
| Utilizar escala logarítmica |Selecione para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico indicar os tipos de valor e, opcionalmente, para converter os valores.  O tipo de unidade Especifica a categoria da unidade e define os valores do tipo de unidade atual que estão disponíveis.  Se selecionar um valor em converter, em seguida, os valores numéricos são convertidos do tipo de unidade atual para converter no tipo. |
| Etiqueta personalizada |Texto a apresentar para o eixo Y junto a etiqueta para o tipo de unidade.  Não se for especificada nenhuma etiqueta, em seguida, é apresentado apenas o tipo de unidade. |
| **Lista** | |
| Consulta |Consulta para a lista.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| Ocultar gráfico |Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar sparklines |Selecione esta opção para apresentar o gráfico sparkline em vez de barras horizontais.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Cor |Cor de barras ou sparklines. |
| Operação |Operação de efetuar para o gráfico sparkline.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Nome e valor de separação |Delimitador de carácter único se pretender analisar a propriedade de texto em vários valores.  Consulte [definições comuns](#name-value-separator) para obter mais detalhes. |
| Consulta de navegação |Consulta para ser executada quando o utilizador seleciona um item na lista.  Consulte [definições comuns](#navigation-query) para obter mais detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a apresentar na parte superior da coluna primeiro da lista. |
| Valor |Texto a apresentar na parte superior da coluna segundo da lista. |
| **Lista** |**> Limiares** |
| Ativar os limiares |Selecione esta opção para ativar os limiares.  Consulte [definições comuns](#thresholds) para obter mais detalhes. |

## <a name="line-chart--list-part"></a>Parte de gráfico & lista de linha
Cabeçalho apresenta um gráfico de linhas com várias séries por uma consulta de registo ao longo do tempo.  Lista apresenta os resultados de dez principais por uma consulta com um gráfico que indica o valor relativo de uma coluna numérica ou respetivas alterações ao longo do tempo.

![Vista de gráfico & lista de linha](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |Texto a apresentar na parte superior do mosaico. |
| Novo grupo |Selecione para criar um novo grupo na vista de começando vista atual. |
| Ícone |Ficheiro de imagem a apresentar junto o resultado no cabeçalho. |
| Ícone de utilização |Selecione para ter a apresentação de ícone. |
| **Cabeçalho** | |
| Título |Texto a apresentar na parte superior do cabeçalho. |
| Subtítulo do |Texto a apresentar sob o título na parte superior do cabeçalho. |
| **Gráfico de linhas** | |
| Consulta |Consulta para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda propriedade um valor numérico.  Isto é normalmente uma consulta que utiliza o **medidas** palavra-chave para resumir os resultados.  Se a consulta utiliza a **intervalo** palavra-chave, em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não inclui o **intervalo** intervalos de palavra-chave e a hora a hora são utilizados para o eixo x. |
| **Gráfico de linhas** |**> Eixo Y** |
| Utilizar escala logarítmica |Selecione para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico indicar os tipos de valor e, opcionalmente, para converter os valores.  O tipo de unidade Especifica a categoria da unidade e define os valores do tipo de unidade atual que estão disponíveis.  Se selecionar um valor em converter, em seguida, os valores numéricos são convertidos do tipo de unidade atual para converter no tipo. |
| Etiqueta personalizada |Texto a apresentar para o eixo Y junto a etiqueta para o tipo de unidade.  Não se for especificada nenhuma etiqueta, em seguida, é apresentado apenas o tipo de unidade. |
| **Lista** | |
| Consulta |Consulta para a lista.  Será apresentada a contagem do número de registos devolvidos pela consulta. |
| Ocultar gráfico |Selecione esta opção para desativar o gráfico para a direita da coluna numérica. |
| Ativar sparklines |Selecione esta opção para apresentar o gráfico sparkline em vez de barras horizontais.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Cor |Cor de barras ou sparklines. |
| Operação |Operação de efetuar para o gráfico sparkline.  Consulte [definições comuns](#sparklines) para obter mais detalhes. |
| Nome e valor de separação |Delimitador de carácter único se pretender analisar a propriedade de texto em vários valores.  Consulte [definições comuns](#name-value-separator) para obter mais detalhes. |
| Consulta de navegação |Consulta para ser executada quando o utilizador seleciona um item na lista.  Consulte [definições comuns](#navigation-query) para obter mais detalhes. |
| **Lista** |**> Títulos de coluna** |
| Nome |Texto a apresentar na parte superior da coluna primeiro da lista. |
| Valor |Texto a apresentar na parte superior da coluna segundo da lista. |
| **Lista** |**> Limiares** |
| Ativar os limiares |Selecione esta opção para ativar os limiares.  Consulte [definições comuns](#thresholds) para obter mais detalhes. |

## <a name="stack-of-line-charts-part"></a>Pilha da peça de gráficos de linha
Apresenta os três separado gráficos de linhas com várias séries por uma consulta de registo ao longo do tempo.

![Pilha de gráficos de linha](media/log-analytics-view-designer/view-stack-line-charts.png)

| Definição | Descrição |
|:--- |:--- |
| **Geral** | |
| Título de grupo |Texto a apresentar na parte superior do mosaico. |
| Novo grupo |Selecione para criar um novo grupo na vista de começando vista atual. |
| Ícone |Ficheiro de imagem a apresentar junto o resultado no cabeçalho. |
| **Gráfico 1<br>gráfico 2<br>gráfico 3** |**> Cabeçalho** |
| Título |Texto a apresentar na parte superior do gráfico. |
| Subtítulo do |Texto a apresentar sob o título na parte superior do gráfico. |
| **Gráfico 1<br>gráfico 2<br>gráfico 3** |**Gráfico de linhas** |
| Consulta |Consulta para o gráfico de linhas.  A primeira propriedade deve ser um valor de texto e a segunda propriedade um valor numérico.  Isto é normalmente uma consulta que utiliza o **medidas** palavra-chave para resumir os resultados.  Se a consulta utiliza a **intervalo** palavra-chave, em seguida, o eixo x do gráfico irá utilizar este intervalo de tempo.  Se a consulta não inclui o **intervalo** intervalos de palavra-chave e a hora a hora são utilizados para o eixo x. |
| **Gráfico** |**> Eixo Y** |
| Utilizar escala logarítmica |Selecione para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores devolvidos pela consulta.  Estas informações são utilizadas para apresentar as etiquetas no gráfico indicar os tipos de valor e, opcionalmente, para converter os valores.  O tipo de unidade Especifica a categoria da unidade e define os valores do tipo de unidade atual que estão disponíveis.  Se selecionar um valor em converter, em seguida, os valores numéricos são convertidos do tipo de unidade atual para converter no tipo. |
| Etiqueta personalizada |Texto a apresentar para o eixo Y junto a etiqueta para o tipo de unidade.  Não se for especificada nenhuma etiqueta, em seguida, é apresentado apenas o tipo de unidade. |

## <a name="common-settings"></a>Definições comuns
As secções seguintes descrevem as definições comuns a várias partes de visualização.

### <a name="name-value-separator">Nome e valor de separação</a>
Delimitador de carácter único se pretender analisar a propriedade de texto por uma consulta de lista para valores múltiplos.  Se especificar um delimitador, pode fornecer nomes para cada campo separado pelo delimitador mesmo na caixa de nome.

Por exemplo, considere uma propriedade denominada *localização* que incluídos como valores *Redmond edifício 41* e *Bellevue Building12*.  Pode especificar – para o nome & separador de valor e *cidade edifício* para o nome.  Isto seria analisar cada valor em duas propriedades chamadas *Cidade* e *edifício*.

### <a name="navigation-query">Consulta de navegação</a>
Consulta para ser executada quando o utilizador seleciona um item na lista.  Utilize *{item selecionado}* para incluir a sintaxe para o item que o utilizador selecionado.

Por exemplo, se a consulta tem uma coluna chamada *computador* e a consulta de navegação *{item selecionado}*, uma consulta, tais como *computador = "MyComputer"* iria ser executado quando o utilizador Selecionar um computador.  Se a consulta de navegação é *tipo = eventos {item selecionado}* , em seguida, a consulta *tipo = evento computador = "MyComputer"* iria ser executado.

### <a name="sparklines">Sparklines</a>
Um gráfico sparkline é um gráfico de linhas pequenas que ilustra o valor de uma entrada da lista ao longo do tempo.  Partes de visualização com uma lista, pode selecionar se pretende apresentar um horizontal barra que indica o valor relativo de uma coluna numérica ou um gráfico sparkline que indica o valor ao longo do tempo.

A tabela seguinte descreve as definições para sparklines.

| Definição | Descrição |
|:--- |:--- |
| Ativar Sparklines |Selecione esta opção para apresentar o gráfico sparkline em vez de barras horizontais. |
| Operação |Se sparklines estiver ativada, esta é a efetuar em cada propriedade na lista para calcular os valores para o gráfico sparkline da operação.<br><br>-Última amostra: Último valor para a série ao longo do intervalo de tempo.<br>-Máximo: O valor máximo para a série ao longo do intervalo de tempo.<br>-Mínimo: O valor mínimo para a série ao longo do intervalo de tempo.<br>-Soma: Soma dos valores para a série ao longo do intervalo de tempo.<br>-Resumo: Utiliza o mesmo comando de medida como a consulta no cabeçalho. |

### <a name="thresholds">Limiares</a>
Limiares permitem-lhe apresentar um ícone colorido junto a cada item numa lista dando-lhe um indicador de visual rápido dos itens que exceder um valor específico ou enquadram-se dentro de um intervalo específico.  Por exemplo, foi possível apresentar um ícone verde para itens com um valor aceitável, amarelo, se o valor for dentro de um intervalo que indica um aviso e vermelho, se exceder um valor de erro.

Quando ativar os limiares de uma parte, tem de especificar um ou mais limiares.  Se o valor de um item é maior do que um valor de limiar e inferior ao valor de limiar seguinte, em seguida, esse cor é utilizado.  Se o item é superior ao valor de limiar, em seguida, mais elevado, esse cor está definido.   

Cada conjunto de limiar tem um limiar com um valor de **predefinido**.  Esta é a cor definida se não existem outros valores for excedidos.  Pode adicionar ou remover limiares clicando a  **+**  ou **x** botão.

A tabela seguinte descreve as definições para tresholds.

| Definição | Descrição |
|:--- |:--- |
| Ativar os limiares |Selecione esta opção para apresentar um ícone cor à esquerda de cada valor que indica o estado de funcionamento em relação ao limiares especificados. |
| Nome |Nome para identificar o valor de limiar. |
| Limiar |Valor para o limiar.  A cor do Estado de funcionamento para cada item de lista está definida para a cor do valor mais elevado de limiar excedido pelo valor do item.  Não há um limiar de predefinido que é a cor, se não existem valores de limiar for excedidos. |
| Cor |Cor para o valor de limiar. |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para suportar as consultas em partes de visualização.
