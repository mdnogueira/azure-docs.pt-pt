---
title: "Utilize as transformações de dados de preparação de dados no Azure Machine Learning | Microsoft Docs"
description: "Este artigo fornece uma lista completa das transformações disponíveis para a preparação de dados do Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 10/09/2017
ms.openlocfilehash: d0b62a63d381239e17b7dccceb89171bca15a68e
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Transformações de dados de utilização de preparação de dados no Azure Machine Learning

A *transformar* no Azure Machine Learning consome dados num formato especificado, efetua uma operação nos dados (como alterar o tipo de dados) e, em seguida, produz dados no novo formato. Cada transformação tem o seu próprio interface e o comportamento. Por encadeamento várias transformações em conjunto através de passos no fluxo de dados, pode efetuar transformações complexas e repetíveis nos seus dados. Este é o núcleo da funcionalidade de preparação de dados.

Seguem-se transformações disponíveis no Azure Machine Learning. 

## <a name="column-selection"></a>Seleção de coluna 
Muitas das transformações nesta lista funcionam em apenas uma coluna ou muitos. Para selecionar várias colunas, utilize a tecla Ctrl. Para selecionar um intervalo de colunas, utilize a tecla Shift.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Transforma a partir do menu principal ou o cabeçalho da grelha 
Acesso transforma da opção de transformações no menu principal. Também pode selecionar as transformações clicando com o nome da coluna na grelha de dados. Se forem selecionadas várias colunas, clique em qualquer um deles fornece um menu de transformações.

No menu de contexto mostra apenas as transformações válidas para o tipo de dados selecionada. O menu principal oferece todas as transformações mas desativa os não sejam relevantes para as colunas selecionadas.

Está disponível um pequeno subconjunto de transformações contextuais clicando uma célula. Estes transformações de cópia, substituir e filtram. Estes são dados tipo com suporte para, pelo que as opções para uma coluna de número diferentes de uma coluna de cadeia.

## <a name="derive-column-by-example"></a>Derivar coluna por exemplo
Utilize esta transformação para criar uma nova coluna como um derivativo de uma ou mais colunas existentes. A transformação analisa as colunas de entrada (selecionadas) e o exemplo fornecido e, em seguida, determina o resultado pretendido na coluna novo. 

Para utilizar esta transformação, selecione uma ou mais colunas. Adicione uma nova coluna derivada (em branco), por exemplo. Escreva um exemplo de que pretende ver no derivada coluna (partindo do princípio de que deriva de outras colunas) e o "por exemplo" tecnologia tenta de preencher todas as outras células na coluna. 

Para obter exemplos mais complicados, poderá ter de fornecer mais do que um exemplo. Para tal, selecione outra célula e escreva outro exemplo.

A tecnologia de "Por exemplo" utiliza colunas selecionadas para tentar determinar o significado de um exemplo. Se não foram seleccionadas colunas quando esta transformação é invocada, todas as células para a linha atual são utilizadas. Ao selecionar apenas as colunas necessárias leva a resultados mais exatos.

Pode selecionar as colunas antes de invocar a transformação. Quando o editor de transformação foi aberto, as caixas de verificação na parte superior de cada coluna indica as colunas são selecionadas como entradas. Pode adicionar ou remover colunas da seleção utilizando as caixas de verificação dos cabeçalhos das colunas.

Para obter uma explicação mais detalhada do **coluna derivada, por exemplo** transformação, juntamente com mais exemplos, consulte [derivar coluna por referência de exemplo](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Dividir coluna por exemplo
Esta transformação demora uma coluna existente e, utilizando o motor de "Por exemplo", se tentar dividir essa coluna em  *n*  outras colunas. Pode executar a divisão automática nas colunas geradas subsequentes.

Para obter uma explicação mais detalhada do **dividir coluna por exemplo** transformação, juntamente com mais exemplos, consulte [dividir coluna por referência de exemplo](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Expandir o JSON

Esta transformação permite-lhe adicionar várias colunas, expandindo uma coluna com o texto JSON válido.

Para obter uma explicação mais detalhada do **expanda JSON** transformação, juntamente com mais exemplos, consulte [referência expanda JSON](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Combinar as colunas por exemplo

Esta transformação adiciona uma nova coluna através da combinação de valores de várias colunas. 

Para obter uma explicação mais detalhada do **combinar colunas, por exemplo** transformação, juntamente com mais exemplos, consulte [combinar colunas por referência de exemplo](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Duplicar a coluna
Esta transformação faz uma cópia exata de uma ou mais colunas selecionadas e fornecem cada um novo nome derivado do nome da coluna original.

## <a name="text-clustering"></a>Clustering de texto 
Esta transformação é concebida para aproveitar valores inconsistentes que devem ser o mesmo e agrupá-los em conjunto.  

Com esta transformação, os valores numa coluna única são analisados relativamente a semelhança e agrupados em clusters. Para cada cluster, não há um valor canónico, que é o valor que substitui todas as instâncias do cluster e todos os valores de instância. Clusters completas podem ser removidos e o valor canónico editá-lo. Instâncias podem ser removidas de um determinado cluster. O filtro de limiar de pontuação de semelhança que utilizou para instâncias de grupo para um cluster pode ser alterado.

Por predefinição, esta transformação substitui todos os valores de instância de cluster com o valor canónico para esse cluster, criar uma nova coluna que contém os novos valores. Também pode adicionar a classificação de semelhança para cada instância para uma nova coluna (que pode ser chamada) para utilizar mais tarde no fluxo de dados.

## <a name="replace-values"></a>Substitua os valores
Utilize esta transformação para substituir uma cadeia com o outro. A cadeia de origem pode ser uma cadeia parcial ou de uma célula completa e de substituição pode ser aplicadas a uma coluna ou muitos. A cadeia de procura suporta procurar carateres especiais, bem como para regulares carateres. 

## <a name="replace-na-values"></a>Substitua os valores NA
Utilizar esta transformação para substituir as várias formas de NA (n/d, NA, null, NaN, etc.), ou para substituir cadeias vazias com um valor único para os tornar consistente. Esta transformação suporta uma ou várias colunas e está listado apenas quando é selecionada uma coluna. Não está presente no menu de transformação principal quando não foram seleccionadas colunas.

## <a name="replace-missing-values"></a>Substitua os valores em falta
Esta transformação substitui dados em falta com um valor único e a uma ou várias colunas. Esta transformação só é apresentada quando é selecionada uma coluna. Não está presente no menu de transformação principal quando não foram seleccionadas colunas.

## <a name="replace-error-values"></a>Substitua os valores de erro
Esta transformação substitui erros com um valor único e a uma ou várias colunas. Esta transformação só é apresentada quando é selecionada uma coluna. Não está presente no menu de transformação principal quando não foram seleccionadas colunas.

## <a name="trim-string"></a>Cadeia de limitação
Esta transformação remove "espaços em branco" carateres (incluindo espaços, separadores, etc.) de uma ou mais colunas de direita e à esquerda.

## <a name="adjust-precision"></a>Ajustar a precisão
Esta transformação define o número de casas decimais para uma coluna numérica.

## <a name="rename-column"></a>Mudar o nome de coluna
Esta transformação altera o nome da coluna selecionada. Pode também invocá-lo inline no cabeçalho da coluna ao clicar no nome da coluna.

## <a name="remove-column"></a>Remover a coluna
Esta transformação remove as colunas selecionadas e funciona em apenas uma coluna ou muitos. 

## <a name="keep-column"></a>Manter a coluna
Esta transformação mantém apenas as colunas selecionadas e funciona em apenas uma coluna ou muitos.

## <a name="handle-path-column"></a>Processar a coluna de caminho
Durante a importação de um ficheiro, uma coluna de caminho é automaticamente adicionada ao conjunto de dados pelo **Adicionar origem de dados** funcionalidade. Contém o nome de ficheiro completamente qualificado que constitui o caminho para o conjunto de dados. Esta transformação adiciona ou remove essa coluna adicional do conjunto de dados.

## <a name="convert-field-type-to-numeric"></a>Converter o tipo de campo numérico
Esta transformação o tipo de coluna é alterado para numérica. Pode especificar o separador de dados não-inteiros. Por predefinição, esta transformação não pedir a confirmação para o separador, por isso, utilize o **editar** item de menu para invocar o editor. Esta transformação funciona em apenas uma coluna ou muitos.

## <a name="convert-field-type-to-date"></a>Converter o tipo de campo de data
Esta transformação muda o tipo de coluna Data. É utilizado um formato de data/hora predefinido, mas pode ser substituído utilizando `strftime` diretivas. Também pode preceder os valores time com uma data fixo.

Por predefinição, esta transformação não pedir a confirmação para o formato, por isso, utilize o **editar** item de menu no passo resultante para invocar o editor. Esta transformação funciona em apenas uma coluna ou muitos.

Apenas as datas entre 9-22-1677 e 4-11-2262 pode ser convertido para o tipo de data.

## <a name="convert-field-type-to-boolean"></a>Converter o tipo de campo para booleano
Esta transformação o tipo de coluna é alterado para verdadeiro/falso. Suporta o mapeamento de vários valores para VERDADEIRO ou FALSO e pode editar estes mapeamentos. Também suporta uma constante para o qual pode mapear os valores que não existem nas tabelas de mapeamento de verdadeiro/falso. Esta transformação funciona em apenas uma coluna ou muitos.

## <a name="convert-field-type-to-string"></a>Converter o tipo de campo de cadeia
Esta transformação muda o tipo de coluna como uma cadeia e funciona em apenas uma coluna ou muitos.

## <a name="convert-unix-timestamp-to-datetime"></a>Converter Unix Timestamp DateTime
Esta transformação compreende o formato de carimbo de Unix, mesmo é representado como uma cadeia de dados. Converte-o timestamp corretamente para um tipo de data na preparação de dados.

## <a name="filter"></a>Filtro
Esta transformação suporta filtragem de linhas com base nos valores de uma ou várias colunas. As condições do filtro dependem do tipo de dados de cada coluna, pelo que pode filtrar colunas de cadeia por "contém" ou "não contém." É possível filtrar por "superior" ou "inferior" condições colunas numéricas.

Quando o **filtro** transformação é invocada no menu principal ou de clicar no cabeçalho da coluna, a opção para bifurcar linhas falhar no outro fluxo de dados está disponível. O fluxo de dados principal continua com filtrado **no** linhas e um novo fluxo de dados é criado que contém todas as linhas que foram filtradas **saída**.

## <a name="use-first-row-as-headers"></a>Utilizar primeira linha como cabeçalhos
Esta transformação promove a primeira linha do conjunto de dados para os nomes das colunas. Se algumas colunas não tiverem dados na primeira linha, é um nome gerado automaticamente. Utilizar esta transformação significa que a importação de dados é iniciada na linha 2 logo. Consoante o **Ignorar linhas** definição, a importação pode iniciar o mesmo mais baixo no conjunto de dados. Também pode utilizá-lo para remover a promoção e utilizar nomes gerado automaticamente.

## <a name="join"></a>Associar
Esta transformação é utilizada para associar dois fluxos de dados em conjunto. Pode selecionar os resultados da associação a ser o resultado: êxito linhas da associação, o "esquerda" Falha nas linhas da associação a um ou o "direito" Falha nas linhas da associação a um.

O **associação** transformação é iniciado a partir de um fluxo de dados individual e seleciona esse fluxo de dados como lado da associação a um. -Lo, em seguida, pede-lhe para selecionar outro fluxo de dados para outro lado da associação a um. Depois de selecionar os dois fluxos, a transformação necessita de uma única coluna em cada lado da associação a selecionar para participar no. Se a associação a precisar de mais de uma coluna, crie uma coluna derivada antes de iniciar a transformação para criar uma coluna nova, concatenada a ser utilizado apenas para a associação. A transformação tenta sugerir chaves de associação e, se possível, gerar automaticamente a coluna derivada.

A associação tenha sido concluída, é apresentada uma vista de diagrama Sankey da associação a um. A largura das linhas em relação ao entre si representa o número de linhas passagem pelos parte do fluxo de associação. Através do painel à direita, pode selecionar as linhas com êxito, à esquerda de linhas ou à direita falhar linhas exclusivamente a falhar. Também pode escolher um ramo único.

## <a name="append-rows"></a>Acrescente linhas
Esta transformação acrescenta dados a partir de outro fluxo de dados para o atual. -Mapeia as colunas pela posição para adicionar novas linhas no final.

## <a name="append-columns"></a>Acrescentar colunas
Esta transformação acrescenta novas colunas de outro fluxo de dados para o atual. Adiciona novas colunas para a direita. Não tente linha dos dados em linhas.

## <a name="summarize"></a>Resumir
Esta transformação calcula as agregações para a combinação de valores exclusivos nas colunas selecionadas uma ou mais. 

As agregações suportadas são: CONTAGEM, SUM, Mín, Máx., média, VARIÂNCIA e desvio-padrão. A lista de agregados de uma dada coluna está filtrada para apenas os que se aplicam ao tipo de dados agregados. Agrega que aplicar não está desativadas. Por exemplo, não é possível calcular a média de uma coluna de cadeia, mas é possível calcular o mínimo e o máximo.

Quando o editor não estiver disponível, arraste o cabeçalho de coluna cópias de segurança para o painel na parte superior esquerda, onde são apresentadas as colunas para serem agregados. Este painel está hierárquica, pelo que pode fazer agregados aninhados. O painel de editor na parte superior direita é utilizada para selecionar o agregado para aplicar a uma coluna. Uma única coluna pode ser agregada uma ou várias vezes. Depois de, pelo menos, um agregado foi escolhido, da grelha na parte inferior direita previews os dados na sua forma agregada. 

Esta transformação é semelhante a um `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Remova os duplicados
Esta transformação remove a toda a linha quando existem valores duplicados nas colunas selecionadas uma ou mais. Se não existem colunas são escolhidas, o apenas linhas removidas são aqueles onde todos os valores de coluna são os mesmos.

## <a name="sort"></a>Ordenar
Esta transformação ordena os dados. A ordenação pode ser feita através de uma única coluna ou muitas e cada coluna pode ser ordenada (predefinição) de ascendente ou descendente (que pode ser alterado de editor).

Esta transformação é semelhante a um `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Transformações de saída
As seguintes transformações dados de saída. Pode ter vários blocos de escrita num fluxo único para escrever os dados em diferentes pontos.

### <a name="write-to-csv"></a>Escrever CSV
Esta transformação escreve os dados em formato CSV do ponto de atual no fluxo de dados. Controla a localização (local ou remota) e várias definições em todo o ficheiro.

### <a name="write-to-parquet"></a>Escrever Parquet
Esta transformação escreve os dados no formulário Parquet do ponto de atual no fluxo de dados. Controla a localização (local ou remota) e várias definições em todo o ficheiro.

## <a name="script-based-transforms"></a>Transformações baseados em script
As seguintes transformações utilizam script (Python) para efetuar a funcionalidade que está em falta no produto principal. 

>[!NOTE]
>Antes de utilizar qualquer um destes transformações, leia [extensibilidade através de Python](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Adicionar a coluna (script)
Esta transformação adiciona uma coluna para os dados utilizando uma expressão de Python.
Para obter mais informações, consulte [código de exemplo Python para efetuar a derivação de novas colunas](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Filtragem avançadas (script)
Utilize esta transformação para escrever um filtro de nível de linha de Python.
Para obter mais informações, consulte [expressões de filtro de exemplo](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformar o fluxo de dados (scripts)
Esta transformação aplica-se Python para o conjunto completo de dados.
Para obter mais informações, consulte [transformações de fluxo de dados de transformação de exemplo](data-prep-appendix7-sample-transform-data-flow-python.md).

Esta transformação também pode aplicar Python a uma partição de dados completo.
Para obter mais informações, consulte [transformações de fluxo de dados de transformação de exemplo](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Escrever o fluxo de dados (scripts)
Esta transformação utiliza o Python para escrever um conjunto de dados completo.
Para obter mais informações, consulte [Python de exemplo para efetuar a derivação de novas colunas](data-prep-appendix9-sample-destination-connections-python.md).



