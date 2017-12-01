---
title: Otimizar o seu algoritmos no Azure Machine Learning | Microsoft Docs
description: "Explica como escolher o parâmetro ideal definido para um algoritmo no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: 664ab97cdfb663d9c8a4cc6c7b748eebfbdf580c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Selecione parâmetros para otimizar o seu algoritmos no Azure Machine Learning
Este tópico descreve como escolher o direito hyperparameter definido para um algoritmo no Azure Machine Learning. A maioria dos algoritmos do machine learning tem parâmetros a definir. Quando preparar um modelo, terá de fornecer valores para esses parâmetros. Efficacy do modelo treinado depende os parâmetros de modelo que escolher. O processo de localizar o conjunto de parâmetros ideal é conhecido como *modelo seleção*.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Existem várias formas de seleção de modelo. No machine learning, validação cruzada é um dos métodos mais amplamente utilizados para seleção do modelo e é o mecanismo de seleção de modelo predefinido no Azure Machine Learning. Como o Azure Machine Learning suporta o R e Python, sempre que pode implementar os seus próprios mecanismos de seleção de modelo ao utilizar o R ou Python.

Existem quatro passos no processo de localizar o conjunto de parâmetros melhor:

1. **Definir o espaço de parâmetro**: para o algoritmo de, primeiro tem de decidir os valores de parâmetro exato que pretende considerar.
2. **Definir as definições de validação cruzada**: decidir como escolher a validação cruzada subconjuntos de validação para o conjunto de dados.
3. **Definir a métrica**: decida que métrica a utilizar para determinar o melhor conjunto de parâmetros, tais como a precisão, a média de raiz ao quadrado erro, precisão, devolução de chamada ou pontuação f.
4. **Preparar, avaliar e comparar**: para cada combinação de valores de parâmetros exclusivos, validação cruzada é realizada por e com base na métrica de erro que define. Após a avaliação e comparação, pode escolher o modelo best-realizar.

A imagem seguinte ilustra mostra como este pode ser obtida no Azure Machine Learning.

![Localizar o conjunto de parâmetros melhores](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definir o espaço de parâmetro
Pode definir o parâmetro definido o passo de inicialização do modelo. O painel de parâmetros de todos os algoritmos do machine learning tem dois modos de trainer: *único parâmetro* e *parâmetro intervalo*. Escolha o modo de intervalo de parâmetro. No modo de intervalo de parâmetro, pode introduzir vários valores para cada parâmetro. Pode introduzir valores separados por vírgulas na caixa de texto.

![Árvore de decisões elevada de duas classes, parâmetro único](./media/algorithm-parameters-optimize/fig2.png)

 Em alternativa, pode definir os pontos de máximo e mínimos de grelha e o número total de pontos para ser gerada com **utilize intervalo Builder**. Por predefinição, os valores de parâmetros são gerados uma escala linear. Mas se **registo escala** estiver marcada, os valores são gerados na escala de registo (ou seja, o rácio dos pontos adjacentes é constante em vez dos respetivos diferença). Para os parâmetros de número inteiro, pode definir um intervalo utilizando um hífen. Por exemplo, "1-10" significa que se todos os números inteiros entre 1 e 10 (ambos inclusive) formam o conjunto de parâmetros. O modo misto também é suportado. Por exemplo, defina o parâmetro "-1 a 10, 20, 50" inclui números inteiros 1 a 10, 20 e 50.

![Árvore de decisões elevada de duas classes, intervalo de parâmetro](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definir subconjuntos de validação de validação cruzada
O [partição e amostras] [ partition-and-sample] módulo pode ser utilizado para atribuir aleatoriamente subconjuntos de validação para os dados. A seguinte configuração de exemplo para o módulo, iremos definir subconjuntos de validação de cinco e aleatoriamente atribua um número de subconjuntos de validação para as instâncias de exemplo.

![Partição e amostras](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definir a métrica
O [modelo de sintonização] [ tune-model-hyperparameters] módulo fornece suporte para empirically escolher o melhor conjunto de parâmetros para um determinado algoritmo e o conjunto de dados. Para além de outras informações sobre o modelo de preparação a **propriedades** painel deste módulo inclui a métrica para determinar o conjunto de parâmetros melhor. Tem duas caixas de diferentes na lista pendente para classificação e regressão algoritmos, respetivamente. Se o algoritmo em consideração é um algoritmo de classificação, a métrica de regressão é ignorada e vice-versa. Neste exemplo específica, a métrica é **precisão**.   

![Parâmetros de paramétrico](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Preparar, avaliar e comparar
O mesmo [modelo de sintonização] [ tune-model-hyperparameters] módulo trains todos os modelos que correspondem para o conjunto de parâmetros, avalia várias métricas e, em seguida, cria o modelo best-treinado com base na métrica que escolher. Este módulo tem duas entradas obrigatórias:

* O learner untrained
* O conjunto de dados

O módulo tem também um conjunto de dados opcional de entrada. Ligar o conjunto de dados com informações de subconjuntos de validação para a entrada obrigatória do conjunto de dados. Se o conjunto de dados não está atribuído a quaisquer informações de subconjuntos de validação, em seguida, uma validação cruzada 10-fold é executada automaticamente por predefinição. Se a atribuição de subconjuntos de validação não é efetuada e é fornecido um conjunto de dados de validação, a porta de conjunto de dados opcional, em seguida, é escolhido um modo de teste de formação e o primeiro conjunto de dados é utilizado para preparar o modelo para cada combinação de parâmetro.

![Classificador de árvore de decisões elevada](./media/algorithm-parameters-optimize/fig6a.png)

O modelo, em seguida, é avaliado no conjunto de dados de validação. A porta de saída à esquerda do módulo mostra as métricas diferentes como funções de valores de parâmetros. A porta de saída à direita fornece o modelo treinado que corresponde ao modelo de best-execução, de acordo com a métrica escolhido (**precisão** neste caso).  

![Conjunto de dados de validação](./media/algorithm-parameters-optimize/fig6b.png)

Pode ver os parâmetros exatos escolhidos por visualizar na porta saída à direita. Este modelo pode ser utilizado na classificação de um conjunto de teste ou em serviços web operacionalizado depois de guardar como um modelo preparado.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
