---
title: "Do Machine learning cábula de algoritmo | Microsoft Docs"
description: "Uma cábula de algoritmo de aprendizagem imprimível ajuda-o a escolha certo algoritmo para o modelo preditivo no Azure Machine Learning Studio."
keywords: "algoritmo cábula, cábula, algoritmo do machine learning"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: garye
ms.openlocfilehash: 2ee58d5235e4d6954a21349d7b7714ee364c8854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Referência rápida de algoritmos do Machine Learning para o Microsoft Azure Machine Learning Studio
O **Microsoft Azure Machine Learning algoritmo Cheat folha** ajuda-o a escolher o algoritmo de direita para um modelo de Análise Preditiva.

[Azure Machine Learning Studio](https://studio.azureml.net/) tem uma grande biblioteca de algoritmos do ***regressão***, ***classificação***, ***clustering***, e ***deteção de anomalias*** famílias. Cada foi concebida para resolver um tipo diferente de problema do machine learning.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Transferência: Cábula de algoritmo de aprendizagem
**Transfira aqui a cábula: [Machine Learning algoritmo Cheat folha (11 x 17 pol.)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Cábula de algoritmo de aprendizagem máquina: Saiba como escolher um algoritmo de aprendizagem.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Transferir e imprimir o Machine Learning algoritmo Cheat folha no tamanho de tabloide para manter útil e obtenha ajuda para escolher um algoritmo.

> [!NOTE]
> Consulte o artigo [como escolher algoritmos para o Microsoft Azure Machine Learning](algorithm-choice.md) para obter um guia detalhado utilizando esta folha de referência.
> 
> 

## <a name="more-help-with-algorithms"></a>Obter ajuda com algoritmos
* Para obter ajuda na utilização desta cábula para escolher o algoritmo à direita, mais um debate mais aprofundado de diferentes tipos de algoritmos do machine learning e como são utilizados, consulte [como escolher algoritmos para o Microsoft Azure Machine Learning](algorithm-choice.md).
* Para um gráfico informativo transferível que descreve algoritmos e fornece exemplos, consulte [transferível gráfico informativo: Noções básicas com exemplos do algoritmo de aprendizagem máquina](basics-infographic-with-algorithm-examples.md).
* Para obter uma lista por categoria de todos os os algoritmos de machine learning disponíveis no Machine Learning Studio, consulte [inicializar modelo] [ initialize-model] no algoritmo do Machine Learning Studio e ajudar do módulo.
* Para obter uma lista alfabética completa de algoritmos e módulos no Machine Learning Studio, consulte [lista A-Z dos módulos de Machine Learning Studio] [ a-z-list] no algoritmo do Machine Learning Studio e o módulo de ajuda.
* Para transferir e imprimir um diagrama da descrição geral das funcionalidades do Machine Learning Studio, consulte o artigo [Diagrama da descrição geral das funcionalidades do Azure Machine Learning Studio](studio-overview-diagram.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Notas e definições terminológicas para o algoritmo de aprendizagem cheat folha

* As sugestões oferecidas nesta folha de referência do algoritmo são aproximado regras do botão. Alguns podem ser bent e alguns podem ser flagrantly violado. Destina-se sugerir um ponto de partida. Não possível afraid executar uma concorrência head-to-head entre vários algoritmos nos seus dados. Não é simplesmente sem substitute para compreender os princípios de cada algoritmo e compreender o sistema que geraram os seus dados.

* Cada algoritmo do machine learning tem o suas próprias estilo ou *inductive bias*. Para um problema específico, vários algoritmos poderão ser apropriados e um algoritmo pode ser uma opção melhor que outros. Mas não é sempre possível saber antecipadamente qual é a melhor opção. Nestes casos, vários algoritmos estão listados em conjunto na folha de referência. Uma estratégia de adequado seria tentar um algoritmo, se os resultados ainda não estão satisfatório, tente a outras pessoas. Eis um exemplo do [galeria da Cortana Intelligence](http://gallery.cortanaintelligence.com/) de uma experiência que tenta vários algoritmos contra os mesmos dados e compara os resultados: [comparar classificadores de classe multi: letra reconhecimento](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* Existem três categorias principais de aprendizagem: **aprendizagem supervisionada**, **learning não supervisionado**, e **reinforcement learning**.

  * No **aprendizagem supervisionada**, cada ponto de dados está assinalada como ou associado a um valor de interesse ou categoria.  Um exemplo de uma etiqueta categórico é atribuir uma imagem como 'cat' ou 'preguiçoso'.  Um exemplo de uma etiqueta de valor é o preços de venda associado um carro utilizado. O objetivo da learning supervisionado é estudar a muitos exemplos identificados como estas e, em seguida, para conseguir fazer predições sobre pontos de dados futuras. Por exemplo, identificar fotografias nova com o preferido correto ou atribuir os preços de venda precisos para outros carros utilizados. Este é um tipo popular e mais útil de aprendizagem. Todos os módulos no Azure Machine Learning são learning supervisionados Algoritmos, exceto para [K significa Clustering][k-means-clustering].

  * No **learning não supervisionado**, tem de pontos de dados sem identificação associadas aos mesmos. Em vez disso, o objetivo de um algoritmo de aprendizagem supervisionados é para organizar os dados de alguma forma ou para descrever a respetiva estrutura. Isto pode significar agrupá-lo para clusters, como sucede K significa ou ao localizar o formas diferentes de observar dados complexos para que seja apresentado mais simples.

  * No **reinforcement learning**, obtém o algoritmo de escolher uma ação em resposta a cada ponto de dados. É uma abordagem comum no robotics, onde o conjunto das leituras dos sensores um ponto no tempo é um ponto de dados e o algoritmo tem de escolher a ação seguinte o robot. Também é um natural para aplicações de Internet das coisas. O algoritmo do learning também recebe um sinal de reward um curto período de tempo mais tarde, que indica como bom foi da decisão. Com base nisso, o algoritmo modifica a estratégia para alcançar o reward mais elevado. Atualmente não existem nenhum reinforcement learning algoritmo os módulos no Azure ML.

* **Métodos de Bayesiano** assume de pontos de dados estatisticamente independentes. Isto significa que a variabilidade unmodeled num ponto de dados é uncorrelated com outras pessoas, ou seja, não é possível prever. Por exemplo, se os dados são registados são o número de minutos, até chega a formação subway seguinte, dois medidas tomadas por dia apart são estatisticamente independentes. No entanto, duas medidas tomadas um minuto apart não são estatisticamente independentes - o valor de um é altamente preditivo do valor das outras.

* **Elevada regressão de árvore de decisão** tira partido da sobreposição de funcionalidade ou interação entre as funcionalidades. Isto significa que, em qualquer ponto de dados fornecido, o valor de uma funcionalidade é um pouco preditivo do valor de outro. Por exemplo, dados de elevado/baixa temperatura diária, saber a temperatura baixa para o dia permite-lhe efetuar um estimado razoável, o elevado. As informações contidas nos dois recursos são um pouco redundantes.

* Classificar dados em mais de duas categorias pode ser feito utilizando um classificador inerentemente multi classe ou através da combinação de um conjunto de duas classes classificadores para um **ensemble**. A abordagem de ensemble, há um classificador de duas classes separado para cada classe - cada um deles separa os dados em duas categorias: "este class" e "não esta classe." Em seguida, estes classificadores votar na atribuição correta do ponto de dados. Este é o princípio operacional atrás [várias classes de um vs-todos os][one-vs-all-multiclass].

* Partem do princípio de vários métodos, incluindo regressão logística da e a máquina do ponto Bayes, **limites de classe linear**. Ou seja, estes partem do princípio de que os limites entre classes são aproximadamente direitas linhas (ou hyperplanes num caso mais geral). Muitas vezes, esta é uma característica dos dados não souber até depois tentou separá-lo, mas é algo que, normalmente, podem ser adquiridos por visualizar previamente. Se os limites de classe ter um aspeto muito dados, pen com árvores de decisões, as decisões dispõe, suporta máquinas de vetor ou as redes neurais.

* As redes neurais podem ser utilizadas com variáveis categórico criando um **variável fictício** para cada categoria, defini-la como 1 nos casos em que se aplica a categoria, 0, onde não.


<!-- This is how you can embed a link in an image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-docs-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
