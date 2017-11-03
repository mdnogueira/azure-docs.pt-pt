---
title: Depurar o seu modelo no Azure Machine Learning | Microsoft Docs
description: "Como depurar erros produzidos por módulos de modelo de formação e Pontuar modelo no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bradsev;garye
ms.openlocfilehash: e6e9e1a3b30f84d634592581ea24fb308dcb478e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="debug-your-model-in-azure-machine-learning"></a>Depurar o seu Modelo no Azure Machine Learning

Este artigo explica os possíveis motivos pelos quais por que motivo qualquer uma das seguintes duas falhas poderão ser encontrada durante a execução de um modelo:

* o [preparar modelo] [ train-model] módulo produz um erro 
* o [Pontuar modelo] [ score-model] módulo produz resultados incorretos 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>Módulo do modelo de formação produz um erro

![image1](./media/debug-models/train_model-1.png)

O [preparar modelo] [ train-model] módulo espera duas entradas:

1. O tipo de modelo do machine learning da coleção de modelos fornecida pelo Azure Machine Learning.
2. Os dados de formação com uma coluna de etiqueta especificada que especifica a variável para prever (outras colunas serão consideradas funcionalidades).

Este módulo pode produzir um erro nos seguintes casos:

1. A coluna de etiqueta foi especificada incorretamente. Isto pode acontecer se a mais do que uma coluna é selecionada como a etiqueta ou um índice de coluna incorretos está selecionado. Por exemplo, o segundo cenário seria aplicada se for utilizado um índice de coluna de 30 com um conjunto de dados de entrada que tem apenas 25 colunas.

2. O conjunto de dados não contém quaisquer colunas de funcionalidade. Por exemplo, se o conjunto de dados de entrada tem apenas uma coluna está marcada como a coluna de etiqueta, não haveria nenhum funcionalidades com o qual pretende criar o modelo. Neste caso, o [preparar modelo] [ train-model] módulo produz um erro.

3. O conjunto de dados entrado (funcionalidades ou etiqueta) contém Infinity como um valor.

## <a name="score-model-module-produces-incorrect-results"></a>Módulo do modelo de pontuação produz resultados incorretos

![image2](./media/debug-models/train_test-2.png)

Numa experimentação de preparação/teste típica para um learning supervisionado, o [dividir dados] [ split] módulo divide o conjunto de dados original em duas partes: uma parte é utilizada para preparar o modelo e uma parte é utilizada para pontuar o quão bem efetua o modelo treinado. O modelo treinado, em seguida, é utilizado para os dados de teste, após o qual os resultados são avaliados para determinar a precisão do modelo de pontuação.

O [Pontuar modelo] [ score-model] módulo requer duas entradas:

1. Um resultado de modelo treinado do [preparar modelo] [ train-model] módulo.
2. Classificação conjunto de dados que é diferente do conjunto de dados utilizado para preparar o modelo.

É possível que, mesmo a experimentação for bem sucedida, o [Pontuar modelo] [ score-model] módulo produz resultados incorretos. Vários cenários podem fazer com que isto deve ocorrer:

1. Se a etiqueta especificada é categórico e um modelo de regressão está preparado nos dados, uma saída incorreta seria produzida pelo [Pontuar modelo] [ score-model] módulo. Isto acontece porque regressão requer uma variável de resposta contínua. Neste caso, seria mais adequada utilizar um modelo de classificação. 

2. Da mesma forma, se um modelo de classificação, está preparado num conjunto de dados ter vírgula flutuante ponto números na coluna de etiqueta, poderá produzir resultados indesejáveis. Isto acontece porque classificação requer uma variável de resposta discreto que só permite especificar valores desse intervalo através de um conjunto finito e, normalmente, um pouco pequeno, das classes.

3. Se o conjunto de dados de pontuação não contém todas as funcionalidades utilizadas para preparar o modelo, o [Pontuar modelo] [ score-model] produz um erro.

4. Se uma linha no conjunto de dados classificação contém um valor em falta ou um valor de infinito para qualquer uma das respetivas funcionalidades, o [Pontuar modelo] [ score-model] não irá produzir qualquer saída correspondente a essa linha.

5. O [Pontuar modelo] [ score-model] pode produzir saídas idênticas para todas as linhas no conjunto de dados classificação. Por exemplo, isto pode ocorrer durante a tentativa de classificação com florestas de decisão se for escolhido o número mínimo de amostras por nó de folha para ser mais do que o número de exemplos de formação disponíveis.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

