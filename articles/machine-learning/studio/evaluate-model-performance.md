---
title: Avaliar o desempenho de modelo no Machine Learning | Microsoft Docs
description: Explica como avaliar o desempenho de modelo no Azure Machine Learning.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: bradsev;garye
ms.openlocfilehash: 48ce4584f7270d78b1d09b848bfdd305d03012b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>Como avaliar o desempenho do modelo no Azure Machine Learning
Este artigo demonstra como a avaliar o desempenho de um modelo no Azure Machine Learning Studio e fornece uma breve explicação das métricas disponíveis para esta tarefa. Três cenários comuns do learning supervisionado são apresentados: 

* Regressão
* classificação binária 
* classificação de várias classes

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Avaliar o desempenho de um modelo é uma das fases de núcleos no processo de ciência de dados. Indica como concluída com êxito as classificação (predições) de um conjunto de dados foi por um modelo preparado. 

Azure Machine Learning suporta a avaliação de modelo através de dois dos seu principal do machine learning módulos: [avaliar modelo] [ evaluate-model] e [modelo de validação cruzada][cross-validate-model]. Estes módulos permitem-lhe ver como o seu modelo efetua em termos de um número de métricas que são frequentemente utilizadas no machine learning e estatísticas.

## <a name="evaluation-vs-cross-validation"></a>Avaliação vs. Validação de se estender
Avaliação e validação cruzada são formas padrão para medir o desempenho do seu modelo. Podem ambos geram métricas de avaliação que pode verificar ou comparar relativamente aos outros modelos.

[Avaliar modelo] [ evaluate-model] espera um conjunto de dados classificado como entrada (ou 2 no caso em que pretende comparar o desempenho de 2 modelos diferentes). Isto significa que terá de preparar o seu modelo com o [modelo de formação] [ train-model] predições do módulo e disponibilizar no conjunto de dados a utilizar o [Pontuar modelo] [ score-model] módulo, antes de pode avaliar os resultados. A avaliação se basear em etiquetas/probabilidades classificadas juntamente com as etiquetas verdadeiras, todos os que são produzidos pelo [Pontuar modelo] [ score-model] módulo.

Em alternativa, pode utilizar validação cruzada para efetuar diversas operações avaliar de pontuação de formação (10 subconjuntos de validação) automaticamente em diferentes subconjuntos de dados de entrada. Os dados de entrada são divididos em 10 peças, onde um está reservado para fins de teste, e a outros 9 para formação. Este processo é repetido 10 vezes e alcançou uma média as métricas de avaliação. Isto ajuda a determinar o quão bem um modelo seria generalize a conjuntos de dados de novo. O [modelo de validação cruzada] [ cross-validate-model] módulo aceita um modelo untrained e algumas identificado conjunto de dados e produz os resultados da avaliação de cada um dos subconjuntos de 10 validação, além dos resultados média.

Nas secções seguintes, iremos criar modelos de regressão e classificação simples e avaliar o seu desempenho, utilizando ambos os [avaliar modelo] [ evaluate-model] e o [modelo de validação cruzada] [ cross-validate-model] módulos.

## <a name="evaluating-a-regression-model"></a>Avaliar um modelo de regressão
Partem do princípio de que queremos prever o preço de um carro com algumas funcionalidades, tais como dimensões, potência de cavalos, especificações de motor e assim sucessivamente. Este é um problema de regressão típica, onde a variável de destino (*preços*) é um valor numérico contínuo. Iremos podem ajustar um modelo de regressão linear simples que, fornecidos os valores de funcionalidade de um carro determinadas pode prever o preço desse automóvel. Este modelo de regressão pode ser utilizado para pontuar o mesmo conjunto de dados que foi preparados no. Assim que tivermos os preços previstos para todas os carros, iremos pode avaliar o desempenho do modelo ao observar quanto serão as predições desvio dos preços reais em média. Para ilustrar isto, utilizamos o *o conjunto de dados do automóvel preços dados (bruto)* disponíveis no **conjuntos de dados guardado** secção no Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Criar a experimentação
Adicione os seguintes módulos à sua área de trabalho no Azure Machine Learning Studio:

* Dados do preço automóvel (bruto)
* [Regressão linear][linear-regression]
* [Preparar modelo][train-model]
* [Modelo de pontuação][score-model]
* [Avaliar modelo][evaluate-model]

Ligar as portas, conforme mostrado abaixo na figura 1 e definir a coluna de etiqueta do [preparar modelo] [ train-model] módulo *preços*.

![Avaliar um modelo de regressão](./media/evaluate-model-performance/1.png)

Figura 1. Avaliar um modelo de regressão.

### <a name="inspecting-the-evaluation-results"></a>A inspecionar os resultados da avaliação
Depois de executar a experimentação, pode clicar na porta de saída a [avaliar modelo] [ evaluate-model] módulo e selecione *visualizar* para ver os resultados da avaliação. As métricas de avaliação disponíveis para os modelos de regressão são: *significa erros absolutos*, *raiz a média dos erros absolutos*, *erro relativo absoluto*, *erro relativo ao quadrado*e o *coeficiente de determinação*.

O termo "error" aqui representa a diferença entre o valor previsto e o valor for VERDADEIRO. O valor absoluto ou o quadrado desta diferença, normalmente, são calculados para capturar a importância total de erros em todas as instâncias, como a diferença entre o valor previsto e verdadeiro pode ser negativa em alguns casos. As métricas de erro medem o desempenho preditivo de um modelo de regressão em termos do desvio de média das respetivas predições entre os valores verdadeiros. Valores mais baixos de erro significa que o modelo é mais exato tomar as predições. Uma métrica de erro geral de 0 significa que o modelo se ajusta aos dados perfeitamente.

O coeficiente de determinação, que também é conhecido como R ao quadrado, também é uma forma de medir o quão bem modelo de dados padrão. Pode ser interpretado como a proporção da variação explicada pelo modelo. Um superior proporção é melhor neste caso, em que 1 indica uma opção perfeita.

![Métricas de avaliação de regressão linear](./media/evaluate-model-performance/2.png)

Figura 2. Métricas de avaliação de regressão linear.

### <a name="using-cross-validation"></a>Utilizar cruzadas validação
Conforme mencionado anteriormente, pode realizar repetida formação, classificação e avaliações automaticamente utilizando o [modelo de validação cruzada] [ cross-validate-model] módulo. Tudo o que precisa neste caso, é um conjunto de dados, um modelo untrained e um [modelo de validação cruzada] [ cross-validate-model] módulo (veja a figura abaixo). Tenha em atenção que tem de definir a coluna de etiqueta *preços* no [modelo de validação cruzada] [ cross-validate-model] propriedades do módulo.

![Um modelo de regressão de validação cruzada](./media/evaluate-model-performance/3.png)

Figura 3. Validação cruzada um modelo de regressão.

Depois de executar a experimentação, pode inspecionar os resultados da avaliação, clicando na porta saída à direita do [modelo de validação cruzada] [ cross-validate-model] módulo. Isto irá fornecer uma vista detalhada das métricas para cada iteração (subconjuntos de validação) e os resultados de média de cada uma das métricas (figura 4).

![Resultados da validação cruzada de um modelo de regressão](./media/evaluate-model-performance/4.png)

Figura 4. Resultados da validação cruzada de um modelo de regressão.

## <a name="evaluating-a-binary-classification-model"></a>Avaliar um modelo de classificação binária
Num cenário de classificação binária, a variável de destino tem apenas dois resultados possíveis, por exemplo: {0, 1} ou {FALSO, true}, {negativo, positivo}. Partem do princípio de que é-lhe fornecido um conjunto de dados de funcionários para adultos com algumas demográficos e variáveis da sua admissão e que é-lhe pedido para prever o nível de receitas, uma variável binário com os valores {"< = 50K", "> 50K"}. Por outras palavras, a classe negativa representa os empregados que efetuam menor ou igual a 50K por ano e a classe positiva representa todos os outros empregados. Como no cenário de regressão, vamos dar formação sobre um modelo, pontuação alguns dados e, avaliar os resultados. A principal diferença aqui é a escolha de métricas que calcula do Azure Machine Learning e saídas. Para ilustrar o cenário de predição de nível de receitas, iremos utilizar o [para adultos](http://archive.ics.uci.edu/ml/datasets/Adult) conjunto de dados para criar uma experimentação do Azure Machine Learning e avaliar o desempenho de um modelo de regressão logística da classe dois, um classificador de binário comummente utilizado.

### <a name="creating-the-experiment"></a>Criar a experimentação
Adicione os seguintes módulos à sua área de trabalho no Azure Machine Learning Studio:

* Conjunto de dados de classificação binária de receitas Census para adultos
* [Regressão logística da duas classes][two-class-logistic-regression]
* [Preparar modelo][train-model]
* [Modelo de pontuação][score-model]
* [Avaliar modelo][evaluate-model]

Ligar as portas, conforme mostrado abaixo na figura 5 e definir a coluna de etiqueta do [preparar modelo] [ train-model] módulo *receitas*.

![Avaliar um modelo de classificação binária](./media/evaluate-model-performance/5.png)

Figura 5. Avaliar um modelo de classificação binária.

### <a name="inspecting-the-evaluation-results"></a>A inspecionar os resultados da avaliação
Depois de executar a experimentação, pode clicar na porta de saída a [avaliar modelo] [ evaluate-model] módulo e selecione *visualizar* para ver os resultados da avaliação (figura 7). As métricas de avaliação disponíveis para os modelos de classificação binária são: *precisão*, *precisão*, *recuperar*, *F1 pontuação*, e *AUC*. Além disso, o módulo produz uma matriz de confusão, que mostra o número de positivos true, falsas negatives, falsos positivos e negatives verdadeiros, bem como *ROC*, *precisão/devolução de chamada*, e *de comparação de precisão* curvas.

Precisão é simplesmente a proporção entre instâncias corretamente classificadas. Normalmente, trata-se a métrica primeiro que observar ao avaliar um classificador. No entanto, quando os dados de teste são desequilibrada (onde a maioria das instâncias pertence a uma das classes), ou é mais interessado no desempenho em qualquer uma das classes, exatidão realmente não captura a eficácia do classificador de um. No cenário de nível de classificação de receitas, partem do princípio de que está a testar em alguns dados em que 99% contra as instâncias representam as pessoas que mais significativos beneficiar de menor que ou igual a 50K por ano. É possível alcançar uma precisão 0.99 ao prever a classe "< = 50K" para todas as instâncias. O classificador neste caso, é apresentada fazer um bom trabalho geral, mas na realidade, não consegue classificar qualquer um dos indivíduos high-income (1%) corretamente.

Por esse motivo, é útil métricas adicionais que capturam aspetos mais específicos da avaliação de computação. Antes de avançar para os detalhes destas métricas, é importante compreender a matriz de confusão de uma avaliação de classificação binária. As etiquetas de classe no conjunto de preparação podem demorar apenas 2 valores possíveis, que, normalmente, consulte como positivo ou negativo. As instâncias de positivos e negativos que um classificador prevê corretamente são denominadas positivos verdadeiros (TP) e negatives verdadeiros (TW), respetivamente. Da mesma forma, as instâncias incorretamente classificadas são denominadas falsos positivos (FP) e negatives falsos (FN). A matriz de confusão é simplesmente uma tabela que mostra o número de instâncias abrangidos por cada uma destas 4 categorias. O Azure Machine Learning automaticamente decide que das duas classes no conjunto de dados é a classe positiva. Se as etiquetas de classe são Boolean ou números inteiros, as instâncias com nome 'true' ou '1' estão atribuídas a classe positiva. Se as etiquetas são cadeias, como no caso do conjunto de dados de receitas, as etiquetas são ordenadas por ordem alfabética e o primeiro nível é escolhido para a classe negativa ao nível da segunda é a classe positiva.

![Matriz de confusão de classificação binária](./media/evaluate-model-performance/6a.png)

Figura 6. Matriz de confusão de classificação binária.

Retroceder para o problema de classificação de receitas, seria queremos pedir a várias perguntas de avaliação nos ajudam a compreender o desempenho do classificador utilizado. Uma pergunta muito natural é: ' fora de pessoas com quem o modelo de prever a ser earning > 50 K (TP + FP), quantas foram corretamente classificadas (TP)? " Esta questão pode ser respondida observando a **precisão** do modelo, que é a proporção de positivos que estão corretamente classificados: TP/(TP+FP). Outra pergunta comum é "fora de todos os alta earning empregados com receitas > 50 k (TP + FN), quantos o classificador de classificar corretamente (TP)". Isto é, na verdade, o **recuperar**, ou a taxa de positiva verdadeira: TP/(TP+FN) do classificador. Poderá reparar que há um compromisso óbvios entre precisão e devolução de chamada. Por exemplo, atribuído um conjunto de dados relativamente equilibrado, um classificador que prevê principalmente positivas instâncias, teria uma devolução de chamada elevada, mas uma precisão em vez disso baixa como muitas das instâncias negativos seria misclassified resultando num grande número de falsos positivos. Para ver um desenho da forma como estas duas métricas variam, pode clicar na curva ' Precisão/devolução de chamada' na página de saída do resultado da avaliação (canto superior esquerdo parte da figura 7).

![Resultados da avaliação de classificação binária](./media/evaluate-model-performance/7.png)

A figura 7. Resultados da avaliação de classificação binária.

Outro relacionadas com a métrica é frequentemente utilizada é a **F1 pontuação**, que demora a precisão e devolução de chamada em consideração. É a média de harmonic destas métricas de 2 e é calculada como tal: F1 = 2 (devolução de chamada de precisão x) / (precisão + devolução de chamada). A classificação de F1 é uma boa forma para resumir a avaliação num único número, mas é sempre boa prática observe a precisão e devolução de chamada em conjunto para compreender melhor a forma como se comporta um classificador.

Além disso, um pode inspecionar a taxa de positiva verdadeira vs. a velocidade positiva falsa no **Recetor operativo característica (ROC)** curva e correspondente **área sob a curva (AUC)** valor. Quanto mais próximo este curva é para o canto superior esquerdo, o melhor desempenho o classificador (que é maximizando a taxa de positiva verdadeira, para minimizar a taxa de positiva falsa). Curvas que estão próximos diagonal de desenho, resultado de classificadores tendem a fazer predições que estão próximos deteção aleatório.

### <a name="using-cross-validation"></a>Utilizar cruzadas validação
Como no exemplo de regressão, pode efetuar uma validação cruzada repetidamente formação, classificação e avaliar subconjuntos diferentes dos dados automaticamente. Da mesma forma, podemos utilizar o [modelo de validação cruzada] [ cross-validate-model] módulo, um modelo de regressão logística da untrained e um conjunto de dados. A coluna de etiqueta deve ser definida como *receitas* no [modelo de validação cruzada] [ cross-validate-model] propriedades do módulo. Depois de executar a experimentação e clicando no lado direito porta de saída a [modelo de validação cruzada] [ cross-validate-model] módulo, é possível ver os valores de métrica de classificação binária para cada subconjuntos de validação, além de média e o desvio-padrão de cada. 

![Um modelo de classificação binária de validação cruzada](./media/evaluate-model-performance/8.png)

Figura 8. Validação cruzada um modelo de classificação binária.

![Resultados da validação cruzada de um classificador binário](./media/evaluate-model-performance/9.png)

Figura 9. Resultados da validação cruzada de um classificador binário.

## <a name="evaluating-a-multiclass-classification-model"></a>Avaliar um modelo de classificação de várias classes
Esta fase experimental utilizaremos o populares [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") conjunto de dados que contém instâncias das 3 diferentes tipos (classes) da maquinaria iris. Existem 4 funcionalidade valores (comprimento de sepal/largura e petal comprimento/largura) para cada instância. Experimentações anteriores foi preparado e testado os modelos utilizando os mesmos conjuntos de dados. Aqui, utilizamos o [dividir dados] [ split] módulo criar 2 subconjuntos de dados, formação no primeiro e Pontuar e avaliar no segundo. O conjunto de dados Iris está publicamente disponível na [UCI Machine Learning repositório](http://archive.ics.uci.edu/ml/index.html)e podem ser transferidos utilizando uma [importar dados] [ import-data] módulo.

### <a name="creating-the-experiment"></a>Criar a experimentação
Adicione os seguintes módulos à sua área de trabalho no Azure Machine Learning Studio:

* [Importar dados][import-data]
* [Floresta de decisão de várias classes][multiclass-decision-forest]
* [Dividir dados][split]
* [Preparar modelo][train-model]
* [Modelo de pontuação][score-model]
* [Avaliar modelo][evaluate-model]

Ligar as portas, conforme mostrado abaixo na figura 10.

Definir o índice de coluna da etiqueta do [preparar modelo] [ train-model] módulo 5. O conjunto de dados não tem nenhuma linha de cabeçalho, mas Sabemos que as etiquetas de classe estão na coluna quinta.

Clique no [importar dados] [ import-data] módulo e defina o *origem de dados* propriedade *URL da Web através de HTTP*e o *URL* para http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Definir a fração de instâncias a serem utilizadas para formação no [dividir dados] [ split] módulo (0,7 por exemplo).

![Avaliar um classificador de várias classes](./media/evaluate-model-performance/10.png)

A figura 10. Avaliar um classificador de várias classes

### <a name="inspecting-the-evaluation-results"></a>A inspecionar os resultados da avaliação
Execute a experimentação e clique na porta de saída do [avaliar modelo][evaluate-model]. Os resultados da avaliação são apresentados sob a forma de uma matriz de confusão, neste caso. A matriz mostra real vs instâncias previstas para todas as classes de 3.

![Resultados da avaliação de classificação de várias classes](./media/evaluate-model-performance/11.png)

Figura 11. Resultados da avaliação de classificação de várias classes.

### <a name="using-cross-validation"></a>Utilizar cruzadas validação
Conforme mencionado anteriormente, pode realizar repetida formação, classificação e avaliações automaticamente utilizando o [modelo de validação cruzada] [ cross-validate-model] módulo. Terá de um conjunto de dados, um modelo untrained e um [modelo de validação cruzada] [ cross-validate-model] módulo (veja a figura abaixo). Novo tem de definir a coluna de etiqueta do [modelo de validação cruzada] [ cross-validate-model] módulo (índice de coluna de 5 neste caso). Depois de executar a experimentação e clicando no direito de saída a porta do [modelo de validação cruzada][cross-validate-model], pode inspecionar os valores de métricos para cada subconjuntos de validação, bem como o desvio médio e standard. As métricas apresentadas aqui são semelhantes para diferentes abordados no caso de classificação binária. No entanto, tenha em atenção que numa classificação várias classes, informática o verdadeiros positivos/negatives e falsos positivos/negatives é efetuado pela contando numa base por classe, como não é não existe nenhuma classe geral positivo ou negativo. Por exemplo, quando informática a precisão ou devolução de chamada da classe 'Iris setosa', presume-se que esta é a classe positiva e todos os outros como negativo.

![Um modelo de classificação de várias classes de validação cruzada](./media/evaluate-model-performance/12.png)

Figura 12. Validação cruzada um modelo de classificação de várias classes.

![Resultados da validação cruzada de um modelo de classificação de várias classes](./media/evaluate-model-performance/13.png)

Figura 13. Resultados da validação cruzada de um modelo de classificação de várias classes.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/

