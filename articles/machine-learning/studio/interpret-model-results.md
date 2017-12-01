---
title: Interpretar os resultados de modelo no Machine Learning | Microsoft Docs
description: "Como escolher o parâmetro ideal definido para um algoritmo a utilizar e visualizar saídas do modelo de pontuação."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 6230e5ab-a5c0-4c21-a061-47675ba3342c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: d6563d411e9f159399f9863a5b572365dc2b05cc
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="interpret-model-results-in-azure-machine-learning"></a>Interpretar os resultados de modelo no Azure Machine Learning
Este tópico explica como visualizar e interpretar os resultados de predição no Azure Machine Learning Studio. Depois de ter um modelo de preparação e feito predições seguir ("classificada o modelo"), tem de compreender e interpretar os resultados de predição.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Existem quatro tipos de principais de modelos no Azure Machine Learning de machine learning:

* Classificação
* Clustering
* Regressão
* Sistemas de recomendador

Os módulos utilizados para predição sobre estes modelos são:

* [Modelo de pontuação] [ score-model] módulo para classificação e regressão
* [Atribuir a Clusters] [ assign-to-clusters] módulo para clustering
* [Pontuar Matchbox Recomendador] [ score-matchbox-recommender] para sistemas de recomendação

Este documento explica como interpretar os resultados de predição para cada um destes módulos. Para obter uma descrição destes módulos, consulte [como escolher os parâmetros para otimizar o seu algoritmos no Azure Machine Learning](algorithm-parameters-optimize.md).

Este tópico aborda a interpretação de predição, mas não avaliação de modelo. Para obter mais informações sobre como avaliar o seu modelo, consulte [como avaliar o desempenho de modelo no Azure Machine Learning](evaluate-model-performance.md).

Se estiver familiarizado com o Azure Machine Learning e precisar de obter ajuda na criação de uma experimentação simples para começar, consulte [criar uma experimentação simple no Azure Machine Learning Studio](create-experiment.md) no Azure Machine Learning Studio.

## <a name="classification"></a>Classificação
Existem dois subcategorias dos problemas de classificação:

* Problemas com apenas dois classes (classificação de duas classes ou binária)
* Problemas com mais de duas classes (classe multi classificação)

O Azure Machine Learning tem módulos diferentes para lidar com cada um destes tipos de classificação, mas os métodos para interpretar os resultados de predição são semelhantes.

### <a name="two-class-classification"></a>Classificação de duas classes
**Experimentação de exemplo**

Um exemplo de um problema de classificação de duas classes é a classificação de iris flowers. A tarefa é classificar flowers iris com base nas respetivas funcionalidades. O conjunto de dados de Iris fornecido no Azure Machine Learning é um subconjunto do populares [conjunto de dados de Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) que contém instâncias de apenas dois flower Especifica (classes 0 e 1). Existem quatro funcionalidades para cada flower (comprimento sepal, largura sepal, comprimento petal e largura petal).

![Captura de ecrã da experimentação iris](./media/interpret-model-results/1.png)

Figura 1. Experimentação de problema de classificação de duas classes IRIS

Uma experimentação ter sido efetuada para resolver este problema, conforme mostrado na figura 1. Um modelo de árvore de decisões elevada de duas classes foi preparado e classificado. Agora pode visualizar os resultados de predição do [Pontuar modelo] [ score-model] módulo clicando na porta de saída do [Pontuar modelo] [ score-model] módulo e, em seguida, clicando em **visualizar**.

![Módulo do modelo de pontuação](./media/interpret-model-results/1_1.png)

Isto apresenta os resultados de classificação conforme mostrado na figura 2.

![Resultados da experimentação de classificação de duas classes iris](./media/interpret-model-results/2.png)

Figura 2. Visualizar um resultado de modelo de pontuação na classificação de duas classes

**Interpretação de resultado**

Existem seis colunas na tabela de resultados. As colunas de quatro à esquerda se as funcionalidades de quatro. As direito de duas colunas, etiquetas classificadas e probabilidades classificadas, são os resultados de predição. A coluna de probabilidades classificadas mostra a probabilidade que um flower pertence à classe positivo (classe 1). Por exemplo, o primeiro número na coluna (0.028571) significa existe é probabilidade 0.028571 que o primeiro flower pertence a 1 de classe. A coluna de etiquetas classificadas mostra a classe prevista para cada flower. Isto baseia-se na coluna probabilidades classificadas. Se a probabilidade de um flower classificada é maior do que 0,5, é prever como 1 de classe. Caso contrário, é prever como classe 0.

**Publicação de serviços Web**

Depois dos resultados de predição tem sido entendidos e judged som, é possível publicar a experimentação como um serviço web para que possa implementar em várias aplicações e chamá-lo a obter predições de classe em qualquer flower iris novo. Para saber como alterar uma experimentação de preparação para uma experimentação de classificação e publicá-lo como um serviço web, consulte [publicam o serviço web do Azure Machine Learning](walkthrough-5-publish-web-service.md). Este procedimento fornece uma experiência de classificação conforme mostrado na figura 3.

![Captura de ecrã da experimentação da classificação](./media/interpret-model-results/3.png)

Figura 3. A experimentação de problema de classificação de duas classes iris da classificação

Agora tem de definir a entrada e saída para o serviço web. A entrada é a porta de entrada à direita do [Pontuar modelo][score-model], que é o flower Iris funcionalidades de entrada. A escolha do resultado depende se estiver interessado em classe prevista (etiqueta classificada), a probabilidade classificada ou ambos. Neste exemplo, presume-se que está interessado em ambas. Para selecionar as colunas de saída pretendidas, utilize um [selecionar colunas no conjunto de dados] [ select-columns] módulo. Clique em [selecionar colunas no conjunto de dados][select-columns], clique em **Seletor de coluna**e selecione **etiquetas classificadas** e **probabilidades classificadas**. Depois de definir a porta de saída do [selecionar colunas no conjunto de dados] [ select-columns] e executá-lo novamente, estará preparado para publicar a experimentação de classificação como um serviço web clicando **publicar o serviço de WEB**. A experimentação final assemelha-se a figura 4.

![A experimentação de classificação de duas classes iris](./media/interpret-model-results/4.png)

Figura 4. Experimentação final de classificação de um problema de classificação de duas classes iris

Depois de executar o serviço web e introduzir alguns valores de funcionalidade de uma instância de teste, o resultado devolve dois números. O primeiro número é a etiqueta classificada e o segundo é a probabilidade classificada. Este flower é prever como 1 de classe com 0.9655 probabilidade.

![Testar o modelo de pontuação interpretar](./media/interpret-model-results/4_1.png)

![A classificação de resultados do teste](./media/interpret-model-results/5.png)

Figura 5. Resultado do serviço Web de classificação de duas classes iris

### <a name="multi-class-classification"></a>Classificação de classe multi
**Experimentação de exemplo**

Esta fase experimental, executar uma tarefa de reconhecimento de letra, como um exemplo de classificação de várias classes. O classificador tenta prever uma determinadas letra (classe) com base nos alguns valores de atributo escrito à mão extraídos a partir de imagens escrito à mão.

![Exemplo de reconhecimento de letra](./media/interpret-model-results/5_1.png)

Os dados de formação, existem 16 funcionalidades extraídas a partir de imagens de letra escrito à mão. As 26 letras formam nosso 26 classes. Figura 6 apresenta uma experimentação que vai dar formação sobre um modelo de classificação de várias classes para reconhecimento de letra e prever a mesma funcionalidade definido num conjunto de dados de teste.

![Experimentação de classificação de várias classes de reconhecimento de letra](./media/interpret-model-results/6.png)

Figura 6. Experimentação de problema de classificação de várias classes de reconhecimento de letra

Visualizar os resultados do [Pontuar modelo] [ score-model] módulo clicando na porta de saída de [Pontuar modelo] [ score-model] módulo e, em seguida, clicando em **visualizar**, deverá ver conteúdo, conforme mostrado na figura 7.

![Resultados de modelo de pontuação](./media/interpret-model-results/7.png)

A figura 7. Visualizar os resultados de modelo de pontuação numa classificação multi classe

**Interpretação de resultado**

As esquerdo 16 colunas representam os valores de funcionalidade do conjunto de teste. As colunas com nomes como probabilidades classificadas para a classe "XX" são apenas como a coluna de probabilidades classificadas no caso de duas classes. Mostram a probabilidade que a entrada correspondente que se insere numa determinada classe. Por exemplo, para a entrada primeiro, há probabilidade 0.003571 que é uma "A," probabilidade 0.000451 que é um "B" e assim sucessivamente. A última coluna (etiquetas classificadas) é igual à etiquetas classificadas no caso de duas classes. Seleciona a classe com a maior probabilidade de classificadas como a classe prevista da entrada correspondente. Por exemplo, para a entrada primeiro, a etiqueta classificada é "F", uma vez que tem a probabilidade de maior para ser um "F" (0.916995).

**Publicação de serviços Web**

Também pode obter a etiqueta classificada para cada entrada e a probabilidade de etiqueta classificada. A lógica básica é ao encontrar a maior probabilidade entre todas as probabilidades classificadas. Para tal, tem de utilizar o [executar Script do R] [ execute-r-script] módulo. O código do R é mostrado na figura 8, e o resultado da experimentação é apresentado na figura 9.

![Exemplo de código do R](./media/interpret-model-results/8.png)

Figura 8. Código do R para extrair etiquetas classificadas e as probabilidades das etiquetas associadas

![Resultado da experimentação](./media/interpret-model-results/9.png)

Figura 9. Experimentação final de classificação do problema de classificação de várias classes de reconhecimento de letra

Depois de publicar e executar o serviço web e introduzir alguns valores de funcionalidade de entrada, o resultado devolvido parece figura 10. Esta letra escrito à mão, com as suas 16 funcionalidades extraídas, é prever ser "T" com 0.9715 probabilidade.

![Módulo de pontuação de interpretar de teste](./media/interpret-model-results/9_1.png)

![Resultado do teste](./media/interpret-model-results/10.png)

A figura 10. Resultado do serviço Web de classificação de várias classes

## <a name="regression"></a>Regressão
Problemas de regressão são diferentes de problemas de classificação. Um problema de classificação, está a tentar prever classes discretas, como as classe um flower iris pertence. Mas como pode ver no seguinte exemplo de um problema de regressão, está a tentar prever uma variável contínua, como o preço de um carro.

**Experimentação de exemplo**

Utilize predição do preço automóvel como exemplo de regressão. Está a tentar prever o preço de um carro com base no respetivas funcionalidades, incluindo a marca, o tipo de fuel, o tipo de corpo e roda de unidade. A experimentação é apresentada na figura 11.

![Experimentação de regressão de preços para automóveis](./media/interpret-model-results/11.png)

Figura 11. Experimentação de problema de regressão de preços para automóveis

Visualizar o [Pontuar modelo] [ score-model] módulo, o resultado semelhante figura 12.

![Resultados de classificação de problema de predição do preço automóvel](./media/interpret-model-results/12.png)

Figura 12. A classificação de resultado para o problema de predição do preço automóvel

**Interpretação de resultado**

Etiquetas classificadas é a coluna de resultado este resultado de classificação. Os números são os preços previstos para cada carro.

**Publicação de serviços Web**

Pode publicar a experimentação de regressão para um serviço web e a chamada de predição do preço automóvel da mesma forma que o caso de utilização de classificação de duas classes.

![Experimentação da classificação de problema de regressão de preços para automóveis](./media/interpret-model-results/13.png)

Figura 13. A classificação de experimentação de um problema de regressão de preços para automóveis

Executar o serviço web, o resultado devolvido aspeto figura 14. O preço previsto para este carro é $15,085.52.

![Módulo de pontuação interpretar de teste](./media/interpret-model-results/13_1.png)

![A classificação de resultados do módulo](./media/interpret-model-results/14.png)

Figura 14. Resultado do serviço Web de um problema de regressão de preços para automóveis

## <a name="clustering"></a>Clustering
**Experimentação de exemplo**

Vamos utilizar o conjunto de dados Iris novamente para criar uma experimentação de clustering. Aqui pode filtrar as etiquetas de classe no conjunto de dados para que apenas tem funcionalidades e pode ser utilizado para clustering. Este iris utilizar caso, especifique o número de clusters para ser duas durante o processo de preparação, o que significa que seria de cluster as flowers em duas classes. A experimentação é apresentada na figura 15.

![Problema de clustering IRIS experimentação](./media/interpret-model-results/15.png)

Figura 15. Problema de clustering IRIS experimentação

Clustering difere de classificação em que o conjunto de dados de formação não tem zero truth etiquetas por si só. Grupos de clusters as instâncias do conjunto de dados de formação em clusters diferentes. Durante o processo de preparação, o modelo de etiquetas de entradas pelo learning as diferenças entre as respetivas funcionalidades. Depois disso, o modelo treinado pode ser utilizado para classificar mais entradas futuras. Existem duas partes do resultado que está interessados em dentro de um problema de clustering. A primeira parte é etiquetagem o conjunto de dados de formação e o segundo é classificar um novo conjunto de dados com o modelo treinado.

A primeira parte do resultado pode ser visualizada clicando na porta saída à esquerda do [preparar modelo de Clustering] [ train-clustering-model] e, em seguida, clicando em **visualizar**. A visualização desta é apresentada na figura 16.

![Clustering de resultado](./media/interpret-model-results/16.png)

Figura 16. Visualizar o clustering de resultado para o conjunto de dados de formação

O resultado da segunda parte, clustering novas entradas com o modelo treinado do clustering, é apresentado na figura 17.

![Visualizar o clustering de resultado](./media/interpret-model-results/17.png)

Figura 17. Visualizar clustering resultado num novo conjunto de dados

**Interpretação de resultado**

Embora os resultados de duas partes stem da experimentação diferentes fases, estes têm o mesmo aspeto e são interpretados da mesma forma. As primeiro quatro colunas são funcionalidades. A última coluna, atribuições, é o resultado de predição. As entradas atribuídas o mesmo número são prever estar no mesmo cluster, ou seja, partilham semelhanças de alguma forma (esta fase experimental utiliza a métrica de distância Euclidean predefinido). Uma vez que especificar o número de clusters para ser 2, as entradas no atribuições estão identificados como 0 ou 1.

**Publicação de serviços Web**

Pode publicar a experimentação de clustering para um serviço web e chamá-lo para clustering predições da mesma forma que a classificação de duas classes utilizar as maiúsculas e minúsculas.

![Experimentação da classificação de problema de clustering iris](./media/interpret-model-results/18.png)

Figura 18. Experimentação de um problema de clustering iris da classificação

Depois de executar o serviço web, o resultado devolvido aspeto 19 figura. Este flower é prever ser em cluster 0.

![Teste interpretar o módulo de pontuação](./media/interpret-model-results/18_1.png)

![Resultado do módulo da classificação](./media/interpret-model-results/19.png)

Figura 19. Resultado do serviço Web de classificação de duas classes iris

## <a name="recommender-system"></a>Sistema de recomendador
**Experimentação de exemplo**

Para sistemas de recomendador, pode utilizar o problema de recomendação restaurante como um exemplo: pode Recomendamos restaurants para clientes com base no respetivo histórico de classificação. Os dados de entrada constituem em três partes:

* Classificações de restaurante de clientes
* Dados de funcionalidade do cliente
* Dados da funcionalidade restaurante

Existem várias coisas que pode fazer com o [formação Matchbox Recomendador] [ train-matchbox-recommender] módulo no Azure Machine Learning:

* Prever as classificações para um item e o utilizador especificado
* Recomendamos itens para um utilizador especificado
* Localizar utilizadores relacionados com um utilizador especificado
* Localizar itens relacionados com um item fornecido

Pode escolher que pretender selecionando a partir das quatro opções no **tipo de predição de Recomendador** menu. Aqui pode percorrer todos os cenários de quatro.

![Recomendador matchbox](./media/interpret-model-results/19_1.png)

Uma experimentação do Azure Machine Learning típica para um sistema de recomendador aspeto figura 20. Para obter informações sobre como utilizar os módulos do sistema de recomendador, consulte [recomendador matchbox de formação] [ train-matchbox-recommender] e [recomendador matchbox de pontuação][score-matchbox-recommender].

![Experimentação de sistema de recomendador](./media/interpret-model-results/20.png)

Figura 20. Experimentação de sistema de recomendador

**Interpretação de resultado**

**Prever as classificações para um item e o utilizador especificado**

Ao selecionar **classificação predição** em **tipo de predição de Recomendador**, estão a pedir o sistema de recomendador para prever a classificação para um determinado utilizador e um item. A visualização do [pontuação Matchbox Recomendador] [ score-matchbox-recommender] saída aspeto 21 figura.

![Resultado do sistema de recomendador – predição de classificação de pontuação](./media/interpret-model-results/21.png)

Figura 21. Visualizar o resultado de modelo de pontuação do sistema de recomendador – predição de classificação

As primeiras duas colunas são os pares de item de utilizador fornecidos pelos dados de entrada. A terceira coluna é a classificação de um utilizador para um determinado item prevista. Por exemplo, na primeira linha, o cliente U1048 é prever para restaurante taxa 135026 como 2.

**Recomendamos itens para um utilizador especificado**

Ao selecionar **Item recomendação** em **tipo de predição de Recomendador**, está a solicitar que o sistema de recomendador para recomendar itens para um determinado utilizador. É o último parâmetro escolher neste cenário *recomendado item seleção*. A opção **de classificados itens de (para avaliação de modelo)** é principalmente para avaliação de modelo durante o processo de preparação. Para esta fase de predição, escolhemos **de todos os itens**. A visualização do [pontuação Matchbox Recomendador] [ score-matchbox-recommender] saída aspeto figura 22.

![Resultado do sistema de recomendador – recomendação do item de pontuação](./media/interpret-model-results/22.png)

Figura 22. Visualizar os resultados de pontuação do sistema de recomendador – recomendação do item

A primeira das seis colunas representa o utilizador indicado IDs para recomendar itens, conforme fornecida pelos dados de entrada. Outras cinco colunas representam os itens recomendados para o utilizador por ordem descendente de importância. Por exemplo, na primeira linha, o mais recomendado restaurante para cliente U1048 é 134986, seguido pela 135018, 134975, 135021 e 132862.

**Localizar utilizadores relacionados com um utilizador especificado**

Ao selecionar **utilizadores relacionados** em **tipo de predição de Recomendador**, está a solicitar que o sistema de recomendador para localizar utilizadores relacionados para um determinado utilizador. Utilizadores relacionados são os utilizadores que têm preferências semelhantes. É o último parâmetro escolher neste cenário *relacionadas com a seleção do utilizador*. A opção **de utilizadores que classificados itens (para avaliação de modelo)** é principalmente para avaliação de modelo durante o processo de preparação. Escolha **de todos os utilizadores** para esta fase de predição. A visualização do [pontuação Matchbox Recomendador] [ score-matchbox-recommender] saída aspeto figura 23.

![Pontuar o resultado do sistema de recomendador – os utilizadores relacionados](./media/interpret-model-results/23.png)

Figura 23. Visualizar os resultados de pontuação do sistema de recomendador – os utilizadores relacionados

A primeira das seis colunas mostra o utilizador indicado que IDs necessários para localizar utilizadores relacionados, conforme fornecida pelos dados de entrada. Outras cinco colunas armazenam os utilizadores previstos relacionados do utilizador por ordem de relevância descendente. Por exemplo, na primeira linha, o cliente mais relevante para o cliente U1048 é U1051, seguido pela U1066, U1044, U1017 e U1072.

**Localizar itens relacionados com um item fornecido**

Ao selecionar **itens relacionados** em **tipo de predição de Recomendador**, estão a pedir o sistema de recomendador para localizar itens relacionados a um item fornecido. Itens relacionados são os itens mais prováveis para gostou pelo mesmo utilizador. É o último parâmetro escolher neste cenário *relacionadas com a seleção do item*. A opção **de classificados itens de (para avaliação de modelo)** é principalmente para avaliação de modelo durante o processo de preparação. Escolhemos **de todos os itens** para esta fase de predição. A visualização do [pontuação Matchbox Recomendador] [ score-matchbox-recommender] saída aspeto figura 24.

![Pontuar o resultado do sistema de recomendador – itens relacionados](./media/interpret-model-results/24.png)

Figura 24. Visualizar os resultados de pontuação do sistema de recomendador – itens relacionados

A primeira das seis colunas representa o item fornecido IDs necessários para localizar itens relacionados, conforme fornecida pelos dados de entrada. Outras cinco colunas armazenam os itens relacionados previstos do item descendentemente em termos de relevância. Por exemplo, na primeira linha, o item mais relevante para o item 135026 é 135074, seguido pela 135035, 132875, 135055 e 134992.

**Publicação de serviços Web**

O processo de publicação destes experimentações como serviços web para obter predições é semelhante para cada um dos quatro cenários. Aqui iremos utilizar o segundo cenário (Recomendamos itens para um determinado utilizador) como um exemplo. Pode seguir o mesmo procedimento com as outras três.

Guardar o sistema de recomendador treinado como um modelo preparado e filtrar os dados de entrada para uma coluna de ID de utilizador único, conforme solicitado, pode ligar a experimentação que aparece a figura 25 e publicá-lo como um serviço web.

![A classificação de experimentação do problema de recomendação restaurante](./media/interpret-model-results/25.png)

Figura 25. A classificação de experimentação do problema de recomendação restaurante

Executar o serviço web, o resultado devolvido aspeto 26 figura. As cinco restaurants recomendadas para o utilizador U1048 são 134986, 135018, 134975, 135021 e 132862.

![Exemplo de serviço do sistema de recomendador](./media/interpret-model-results/25_1.png)

![Resultados da experimentação de exemplo](./media/interpret-model-results/26.png)

Figura 26. Resultado do serviço Web de problema de recomendação restaurante

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
