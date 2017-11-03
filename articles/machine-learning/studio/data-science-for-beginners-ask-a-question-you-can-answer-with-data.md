---
title: "Peça uma data de pergunta pode responder - problemas de ciência de dados - Azure Machine Learning | Microsoft Docs"
description: "Saiba como formular uma pergunta de ciência de dados sharp nos dados de ciência para principiantes, vídeo 3. Inclui uma comparação de classificação e regressão perguntas."
keywords: "problemas de ciência de dados, perguntas de ciência de dados, formular perguntas, perguntas de regressão, perguntas de classificação, pergunta sharp"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: 5b9501e3-9964-417a-8ffc-8913103da77b
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: cgronlun
ms.openlocfilehash: 3423f704a435c3bab463461d05ede468a7a2dd93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Fazer perguntas que podem ser respondidas com dados
## <a name="video-3-data-science-for-beginners-series"></a>Vídeo 3: Ciência de dados para a série de principiantes
Saiba como formular um problema de ciência de dados para uma pergunta nos dados de ciência para principiantes, vídeo 3. Este vídeo inclui uma comparação de perguntas para classificação e regressão algoritmos.

Para tirar o máximo partido da série, veja todos eles. [Aceda à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
*Dados de ciência para principiantes* é uma introdução rápida aos ciência de dados em cinco vídeos curtos.

* Vídeo 1: [a 5 questões de respostas de ciência de dados](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 seg.)*
* Vídeo de 2: [está os seus dados pronto para ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 seg.)*
* Vídeo 3: Fazer uma pergunta, poderá responder com dados
* Vídeo 4: [prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seg.)*
* Vídeo 5: [copiar trabalho outras pessoas fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcript: Fazer uma pergunta, poderá responder com dados
Bem-vindo ao vídeo terceiro na série de "Dados de ciência para principiantes."  

Este um, obterá algumas sugestões para formulating uma pergunta, poderá responder com dados.

Poderá obter mais informações fora de neste vídeo, se o primeiro a ver os dois vídeos anteriores nesta série: "pode responder a ciência de dados 5 perguntas" e "É os dados estão prontos para ciência de dados?"

## <a name="ask-a-sharp-question"></a>Fazer uma pergunta sharp
Tiver abordámos como ciência de dados é o processo de utilização de nomes (também denominados categorias ou etiquetas) e números para prever uma resposta para uma pergunta. Mas não pode ser com qualquer pergunta; tem de ser um *sharp pergunta.*

Não tem uma pergunta vague a serem respondidas com um nome ou um número. Tem de ser uma pergunta sharp.

Imagine encontrado um lamp mágica com um genie quem puder sustentar irá responder a qualquer pergunta que faz. Mas é um genie mischievous e ele irá a tentar efetuar a resposta como vague e confuso como ele pode obter ausente com. Quer a afixá-lo para baixo com uma pergunta, por isso, airtight que ele não pode ajudar a mas indicam que pretende saber.

Se tiver que faça uma pergunta vague, como "Que está a suceder acontecer com os meus stock?", poderá responder a genie, "o preço será alterado". Que é uma resposta truthful, mas não é muito útil.

Mas se atualizasse fazer uma pergunta sharp, como "Quais preços de venda a minha gráfico de cotações serão próximas semanas?", o genie não é possível ajudar, mas dão-lhe específico responder e prever um preços de venda.

## <a name="examples-of-your-answer-target-data"></a>Exemplos da sua resposta: dados de destino
Depois de formular a sua pergunta, verifique se tem exemplos da resposta nos seus dados.

Se nosso pergunta "O que preços de venda a minha gráfico de cotações será próximas semanas?" em seguida, temos de certificar-se de que o histórico de preços as cotações os nossos dados incluem.

Se a nossa pergunta "que carro na minha frota vai falhar primeiro?" em seguida, temos de certificar-se de que a nossa dados incluem informações sobre falhas de anteriores.

![Dados de destino - exemplos da sua resposta. Formular uma pergunta de ciência de dados.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Estes exemplos de respostas são denominados um destino. Um destino é o que estamos a tentar prever sobre pontos de dados de futuro, se se trata de uma categoria ou um número.

Se não tiver quaisquer dados de destino, terá de obter alguns. Não será capaz de responder à sua questão sem ele.

## <a name="reformulate-your-question"></a>Reformulate sua pergunta
Por vezes, pode reword sua pergunta para obter uma resposta mais útil.

A pergunta "É esta A de ponto de dados ou B?" prevê a categoria (ou nome ou a etiqueta) de algo. Para respondê-lo, utilizamos uma *algoritmo de classificação*.

A pergunta "Quanto?" ou "Quantos?" prevê uma quantidade. Para responder a utilizamos uma *algoritmo de regressão*.

Para ver como podemos pode transformar estas, vamos ver a pergunta, "o bloco de notícias é mais interessante para este leitor?" Este pede-lhe uma predição de uma única opção de possibilidades muitas - por outras palavras "É esta A ou B ou C ou D?" - e pretende utilizar um algoritmo de classificação.

No entanto, esta questão, poderá ser mais fácil responder se lhe reword como "como interessante é cada bloco nesta lista para este leitor?" Agora, pode dar cada artigo uma pontuação numérica e, em seguida, é fácil de identificar o artigo classificação mais elevada. Este é um rephrasing da pergunta classificação para uma pergunta de regressão ou quanto?

![Reformulate sua pergunta. Pergunta de classificação vs pergunta de regressão.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

A forma como os que faz que uma pergunta é um clue para o algoritmo pode dar-lhe uma resposta.

Encontrará que algumas famílias de algoritmos - como aqueles no nosso exemplo de bloco de notícias - estão estreitamente relacionadas com. Pode reformulate sua pergunta para utilizar o algoritmo que dá-lhe a resposta mais úteis.

Mas, mais importante, fazer essa questão sharp - a pergunta que podem responder com dados. E certifique-se de que tem os dados adequados para responder a este.

Tiver abordámos algumas princípios básicos para solicitar uma pergunta, poderá responder com dados.

Lembre-se de que veja vídeos em "Dados de ciência para principiantes" do Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Passos seguintes
* [Tente uma primeira experimentação de ciência de dados com Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](what-is-machine-learning.md)
