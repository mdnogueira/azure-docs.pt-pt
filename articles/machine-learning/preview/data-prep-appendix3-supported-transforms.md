---
title: "Utilize as transformações de dados de preparação de dados no Azure Machine Learning | Microsoft Docs"
description: "Este artigo fornece uma lista completa das transformações disponíveis para a preparação de dados do Azure Machine Learning"
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
ms.openlocfilehash: 4331bb4a16d56f027dd63a97868822fa6ecc92d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Transformações de dados de utilização de preparação de dados no Azure Machine Learning

A *transformar* no Azure Machine Learning consome dados num formato especificado, efetua uma operação nos dados (como alterar o tipo de dados) e, em seguida, produz dados no novo formato. Cada transformação tem o seu próprio interface e o comportamento. Pode encadeiam várias transformações em conjunto através de passos no fluxo de dados, permitindo-lhe efetuar transformações de complexas e repetíveis nos seus dados. Este é o núcleo da funcionalidade de preparação de dados.

Segue-se uma lista de transformações disponíveis no Azure Machine Learning. 

## <a name="column-selection"></a>Seleção de coluna 
Muitas das seguintes transformações listadas funcionam em apenas uma coluna ou muitos. Para selecionar várias colunas, utilize o **Ctrl** chave; ou para selecionar um intervalo de colunas, utilize o **Shift** chave.

## <a name="transforms-from-main-menu-andor-grid-header"></a>Transforma principal no cabeçalho menu e/ou grelha 
Podem ser acedidas transformações da opção de transformações no menu principal. Também é possível selecionar as transformações clicando com o nome da coluna na grelha de dados. Se forem selecionadas várias colunas, em seguida, clique em qualquer um deles fornece um menu de transformações.

Só são disponibilizadas transformações válidas para o tipo de dados selecionada no menu de contexto. O menu principal oferece todas as transformações mas desativa as transformações que não são relevantes para as colunas selecionadas.

Está disponível um pequeno subconjunto de transformações contextuais clicando uma célula. Estes transformações de cópia, substituir e filtram. Estes transformações são o tipo de dados com suporte para, pelo que as opções para uma coluna de número são diferentes para uma coluna de cadeia.

## <a name="derive-column-by-example"></a>Derivar coluna por exemplo
Esta transformação permite a criação de uma nova coluna como um derivativo de uma ou mais colunas existentes. As transformações analisa as colunas de entrada (selecionadas) e o exemplo fornecido e, em seguida, determina a saída de desire na coluna novo. 

Para utilizar esta transformação, selecione uma ou mais colunas. Adicione uma nova coluna derivada (em branco), por exemplo. Escreva um exemplo de que pretende ver no derivada coluna (partindo do princípio de que deriva de outras colunas) e o "por exemplo" tecnologia tenta de preencher todas as outras células na coluna. 

Para obter exemplos mais complicados, poderá ser necessário fornecer mais do que um exemplo. Para tal, selecione outra célula e escreva outro exemplo.

A tecnologia de "Por exemplo" utiliza colunas selecionadas para tentar determinar o significado de um exemplo. Se não foram seleccionadas colunas quando esta transformação é invocada, todas as células para a linha atual são utilizadas. Ao selecionar apenas as colunas necessárias leva a resultados mais exatos.

Pode selecionar as colunas antes de invocar a transformação. Depois do editor de transformação foi iniciado, as caixas de verificação na parte superior de cada coluna indica as colunas são selecionadas como entradas. Pode adicionar ou remover colunas da seleção utilizando as caixas de verificação dos cabeçalhos das colunas.

Para obter uma explicação mais detalhada de **derivada coluna por exemplo** transformação, juntamente com mais exemplos, consulte: [derivar coluna por referência de exemplo](data-prep-derive-column-by-example.md)  

## <a name="split-column-by-example"></a>Dividir coluna por exemplo
Esta transformação assume uma coluna existente e utilizar o motor de "Por exemplo" tenta dividir a essa coluna para  *n*  outras colunas. É possível executar a divisão automática nas colunas geradas subsequentes.

Para obter uma explicação mais detalhada de **dividir coluna por exemplo** transformação, juntamente com mais exemplos, consulte: [dividir coluna por referência de exemplo](data-prep-split-column-by-example.md)

## <a name="expand-json"></a>Expandir o JSON

Esta transformação permite-lhe adicionar várias colunas, expandindo uma coluna com o texto JSON válido.

Para obter uma explicação mais detalhada de **expanda JSON** transformação, juntamente com mais exemplos, consulte: [expanda referência de JSON](data-prep-expand-json.md)


## <a name="combine-columns-by-example"></a>Combinar as colunas por exemplo

Esta transformação permite-lhe adicionar uma nova coluna através da combinação de valores de várias colunas. 

Para obter uma explicação mais detalhada de **combinar colunas, por exemplo** transformação, juntamente com mais exemplos, consulte: [combinar colunas por referência de exemplo](data-prep-combine-columns-by-example.md)


## <a name="duplicate-column"></a>Duplicar a coluna
Esta transformação faz uma cópia exata de uma ou mais colunas selecionadas e fornecem cada um novo nome derivado do nome da coluna original.

## <a name="text-clustering"></a>Clustering de texto 
Esta transformação é concebida para aproveitar valores inconsistentes que devem ser o mesmo e agrupá-los em conjunto.  

Quando utiliza esta transformação, os valores numa coluna única são analisados relativamente a semelhança e agrupados em clusters. Para cada cluster tem um valor canónico, que é o valor que substitui todas as instâncias do cluster e valores de instância. Clusters concluídas podem ser removidos e o valor canónico pode ser editado. Instâncias podem ser removidas de um determinado cluster. Além disso, o filtro de limiar de pontuação de semelhança que utilizou para instâncias de grupo para um cluster pode ser alterado.

Por predefinição esta transformação substitui todos os valores de instância de cluster com o valor canónico para esse cluster, criar uma nova coluna que contém os novos valores. Também é possível adicionar a classificação de semelhança, para cada instância, para uma nova coluna (que pode ser chamada) para permitir que a sua utilização mais tarde no fluxo de dados.

## <a name="replace-values"></a>Substitua os valores
Esta transformação permite uma cadeia de ser substituído por outro. A cadeia de origem pode ser uma cadeia parcial ou completa célula; a substituição pode aplicar a uma coluna ou muitos. A cadeia de procura suporta carateres especiais, bem como carateres regulares a procurar. 

## <a name="replace-na-values"></a>Substitua os valores NA
Esta transformação permite as várias formas diferentes de NA (n/d, NA, null, NaN, etc.) ou vazio cadeias ser substituído com um valor único para os tornar consistente. Esta transformação suporta uma ou várias colunas. Esta transformação só é apresentada quando uma coluna está selecionada e não está presente no menu principal transformação quando não foram seleccionadas colunas.

## <a name="replace-missing-values"></a>Substitua os valores em falta
Esta transformação substitui dados em falta com um valor único. Esta transformação suporta uma ou várias colunas. Esta transformação só é apresentada quando uma coluna está selecionada e não está presente no menu principal transformação quando não foram seleccionadas colunas.

## <a name="replace-error-values"></a>Substitua os valores de erro
Esta transformação substitui erros com um valor único. Esta transformação suporta uma ou várias colunas. Esta transformação só é apresentada quando uma coluna está selecionada e não está presente no menu principal transformação quando não foram seleccionadas colunas.

## <a name="trim-string"></a>Cadeia de limitação
Esta transformação remove direita "espaços em branco" carateres e à esquerda (inclui espaços, separadores, *etc.*), de uma ou mais colunas.

## <a name="adjust-precision"></a>Ajustar a precisão
Esta transformação permite-lhe definir o número de casas decimais para uma coluna numérica.

## <a name="rename-column"></a>Mudar o nome de coluna
Esta transformação altera o nome da coluna selecionada. Esta transformação também pode ser invocado inline no cabeçalho da coluna ao clicar no nome da coluna.

## <a name="remove-column"></a>Remover a coluna
Esta transformação remove as colunas selecionadas. Esta transformação funciona em apenas uma coluna ou muitos. 

## <a name="keep-column"></a>Manter a coluna
Esta transformação mantém apenas as colunas selecionadas. Esta transformação funciona em apenas uma coluna ou muitos.

## <a name="handle-path-column"></a>Processar a coluna de caminho
Durante a importação de um ficheiro, uma coluna de caminho é automaticamente adicionada ao conjunto de dados pelo Assistente Adicionar origem de dados. Contém o nome de ficheiro completamente qualificado que constitui o caminho para o conjunto de dados. Esta transformação adiciona ou remove essa coluna adicional do conjunto de dados.

## <a name="convert-field-type-to-numeric"></a>Converter o tipo de campo numérico
Esta transformação o tipo de coluna é alterado para numérica. Pode especificar o separador se houver dados não-inteiros. Por predefinição, esta transformação não pedir a confirmação para o separador, utilize o **editar** item de menu para invocar o editor. Esta transformação funciona em apenas uma coluna ou muitos.

## <a name="convert-field-type-to-date"></a>Converter o tipo de campo de data
Esta transformação muda o tipo de coluna Data. Um formato de data/hora predefinido é utilizado, mas pode ser substituído utilizando `strftime` diretivas. Também é possível preceder os valores time com uma data fixo.

Por predefinição esta transformação não pedir o formato, utilize o **editar** item de menu no passo resultante para invocar o editor. Esta transformação funciona em apenas uma coluna ou muitos.

Apenas as datas entre 9-22-1677 e 4-11-2262 pode ser convertido para o tipo de data.

## <a name="convert-field-type-to-boolean"></a>Converter o tipo de campo para booleano
Esta transformação o tipo de coluna é alterado para verdadeiro/falso. Esta transformação suporta o mapeamento de vários valores para VERDADEIRO ou FALSO, e é possível editar estes mapeamentos. Também suporta uma constante para o qual pode mapear os valores que não existem nas tabelas de mapeamento de verdadeiro/falso. Esta transformação funciona em apenas uma coluna ou muitos.

## <a name="convert-field-type-to-string"></a>Converter o tipo de campo de cadeia
Esta transformação muda o tipo de coluna como uma cadeia. Esta transformação funciona em apenas uma coluna ou muitos.

## <a name="convert-unix-timestamp-to-datetime"></a>Converter Unix Timestamp DateTime
Esta transformação compreende o formato Unix Timestamp, mesmo se é representada como uma cadeia de dados e converte-la corretamente para um tipo de data na preparação de dados.

## <a name="filter"></a>Filtro
Esta transformação suporta filtragem de linhas com base nos valores de uma ou várias colunas. As condições do filtro são dependentes no tipo de dados de cada coluna, facilitando a possíveis para colunas de cadeia de filtro por "contém" ou "não contém". Colunas numéricas pode ser filtrada "superior" por "inferior a" condições.

Quando a transformação do filtro é invocada no menu principal ou de clicar no cabeçalho da coluna, a opção de copiar as linhas de falhar no outro fluxo de dados está disponível. O fluxo de dados principal continua com filtrado **no** linhas e um novo fluxo de dados é criado que contém todas as linhas que foram filtradas **saída**.

## <a name="use-first-row-as-headers"></a>Utilizar primeira linha como cabeçalhos
Esta transformação promove a primeira linha do conjunto de dados para os nomes das colunas. Se algumas colunas não tiverem dados na primeira linha, em seguida, é um nome gerado automaticamente. Utilizar esta transformação significa que a importação de dados é iniciada na linha 2 logo. Consoante a definição de linhas de ignorar a importação pode começar a mesmo mais baixo no conjunto de dados. Também pode ser utilizado para remover a promoção e para ficarem com nomes gerado automaticamente.

## <a name="join"></a>Associar
Esta transformação é utilizada para associar dois fluxos de dados em conjunto. Pode selecionar o resultado da associação a um deve ser o resultado: êxito linhas da associação, o "esquerda" Falha nas linhas da associação a um ou o "direito" Falha nas linhas da associação a um.

O Assistente de associação é iniciado a partir de um fluxo de dados individual e seleciona esse fluxo de dados como lado da associação a um. -Lo, em seguida, pede-lhe outro fluxo de dados para outro lado da associação a um. Assim que os dois fluxos foram selecionados, o assistente necessita de uma única coluna em cada lado da conjunta selecionar para participar no. Se a associação a precisar de mais de uma coluna, em seguida, crie uma coluna derivada antes de iniciar o Assistente para criar uma nova coluna (concatenada) a ser utilizada apenas para associar. O assistente tentará sugerir chaves de associação e se for possível gerar automaticamente a coluna derivada.

Depois da associação foi concluída, é apresentada uma vista de diagrama de Sankey da associação a um. A largura das linhas em relação ao entre si representa o número de linhas passagem pelos parte do fluxo de associação. O painel à direita permite-lhe selecionar as linhas com êxito, falhar à esquerda ou direita exclusivamente a falhar. Também é possível escolher apenas um ramo.

## <a name="append-rows"></a>Acrescente linhas
Esta transformação acrescenta dados a partir de outro fluxo de dados para o atual. -Mapeia as colunas pela posição para adicionar novas linhas no final.

## <a name="append-columns"></a>Acrescentar colunas
Esta transformação acrescenta novas colunas de outro fluxo de dados para o atual. Adiciona novas colunas à direita; Não tente linha dos dados em linhas.

## <a name="summarize"></a>Resumir
Esta transformação calcula as agregações para a combinação de valores exclusivos nas colunas selecionadas uma ou mais. 

As agregações suportadas são: CONTAGEM, SUM, Mín, Máx., média, VARIÂNCIA e desvio-padrão. A lista de agregados de uma dada coluna está filtrada para apenas aqueles que se aplicam para o tipo de dados. Agrega que aplicar não está desativadas. Por exemplo, não é possível calcular a média de uma coluna de cadeia, mas é possível calcular o mínimo e o máximo.

Depois do editor não estiver disponível, arraste o cabeçalho de coluna cópias de segurança no painel de na parte superior esquerda. onde são apresentadas as colunas a ser agregada. Este painel é hierárquica, pelo que é possível fazer agregados aninhados. O painel de editor no canto superior direito é utilizado para selecionar o agregado para aplicar a uma coluna. Uma única coluna pode ser agregada uma ou várias vezes. Depois de, pelo menos, um agregado foi escolhido, da grelha na parte inferior direita previews os dados na sua forma agregada. 

Esta transformação é semelhante a um `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Remova os duplicados
Esta transformação remove a toda a linha em que existem valores duplicados num ou mais colunas de selecionado. Se não existem colunas são escolhidas, em seguida, apenas linhas que são removidas são aqueles onde todos os valores de coluna são os mesmos.

## <a name="sort"></a>Ordenar
Esta transformação ordena os dados. A ordenação pode ser feita através de uma única coluna ou muitas e cada coluna pode ser ordenada (predefinição) de ascendente ou descendente (pode ser alterado de editor).

Esta transformação é semelhante a um `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Transformações de saída
As seguintes transformações dados de saída. É possível ter vários blocos de escrita num fluxo único para poder escrever os dados em diferentes pontos.

### <a name="write-to-csv"></a>Escrever CSV
Esta transformação escreve os dados em formato CSV do ponto de atual no fluxo de dados. Permite o controlo da localização (local ou remoto) e várias definições em todo o ficheiro.

### <a name="write-to-parquet"></a>Escrever Parquet
Esta transformação escreve os dados no formulário Parquet do ponto de atual no fluxo de dados. Permite o controlo da localização (local ou remoto) e várias definições em todo o ficheiro.

## <a name="script-based-transforms"></a>Transformações de baseado em script
As seguintes transformações utilizam script (Python) para efetuar a funcionalidade que está em falta no produto principal. Antes de utilizar qualquer uma destas transformações, leia [extensibilidade através de Python](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Adicionar a coluna (Script)
Esta transformação permite uma coluna a ser adicionado aos dados de utilização de uma expressão de Python.
Para obter mais informações, consulte [código de exemplo Python para efetuar a derivação de novas colunas](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Filtragem avançadas (Script)
Esta transformação permite um filtro de nível de linha de Python para serem escritos.
Para obter mais informações, consulte [expressões de filtro de exemplo](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transformar o fluxo de dados (scripts)
Esta transformação permite Python sejam aplicadas ao conjunto de dados completo.
Para obter mais informações, consulte [transformações de fluxo de dados de transformação de exemplo](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="transform-dataflow-script"></a>Transformar o fluxo de dados (scripts)
Esta transformação permite Python seja aplicado a uma partição de dados completo.
Para obter mais informações, consulte [transformações de fluxo de dados de transformação de exemplo](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Escrever o fluxo de dados (scripts)
Esta transformação permite Python seja aplicada ao escrever a e o conjunto completo de dados.
Para obter mais informações, consulte [Python de exemplo para efetuar a derivação de novas colunas](data-prep-appendix9-sample-destination-connections-python.md).



