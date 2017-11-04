---
title: "Questões de ciência de 5 dados - dados de ciência para principiantes - Azure Machine Learning | Microsoft Docs"
description: "Ciência de dados para principiantes é informa os conceitos básicos do 5 vídeos curtos, começando com o respostas de ciência de dados do 5 perguntas. De aprendizagem do Azure."
keywords: "Se o fizer ciência de dados, principiantes de ciência de dados, ciência de dados para principiantes Noções básicas de ciência de dados, perguntas de ciência de dados, vídeo de ciência de dados, a introdução de ciência de dados"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: a01f93ee-01eb-4afe-abbd-cfa035c119b0
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: cgronlun
ms.openlocfilehash: 47257bdb054b069c864ebf545b0c00bdfb05a9ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Data Science for Beginners video 1: The 5 questions data science answers (Vídeo Ciência de Dados para Principiantes: As cinco perguntas às quais a ciência de dados dá resposta)
Obter uma introdução rápida aos ciência de dados de *dados de ciência para principiantes* em cinco vídeos abreviados de um scientist de dados superior. Estes vídeos são básico mas útil, se estiver interessado em efetuar ciência de dados ou funciona com cientistas de dados.

É este vídeo primeiro sobre os tipos de questões que pode responder a ciência de dados. Para tirar o máximo partido da série, veja todos eles. [Aceda à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
*Dados de ciência para principiantes* é uma introdução rápida aos dados a ciência demorar cerca de 25 minutos totais. Veja todos os cinco vídeos:

* Vídeo 1: Os 5 questões de respostas de ciência de dados
* Vídeo de 2: [está os seus dados pronto para ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 seg.)*
* Vídeo 3: [fazer uma pergunta, poderá responder com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 seg.)*
* Vídeo 4: [prever uma resposta com um modelo simples](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 seg.)*
* Vídeo 5: [copiar trabalho outras pessoas fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg.)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transcript: A 5 questões de respostas de ciência de dados
Olá! Bem-vindo aos série de vídeos *dados de ciência para principiantes*.

Ciência de dados pode ser difícil, pelo que posso irá apresentar as noções básicas sobre aqui sem as equações ou computador jargon de programação.

Neste vídeo primeiro, iremos falar sobre "a 5 questões respostas de ciência de dados."

Ciência de dados utiliza números e os nomes (também conhecido como categorias ou etiquetas) para prever as respostas a perguntas.

-Pode Surpreenda, mas *existem perguntas apenas cinco que respostas de ciência de dados*:

* É esta A ou B?
* Este é estranho?
* Quantidade – ou – quantos?
* Como é isto organizados?
* O que devo fazer em seguida?

Cada um destas perguntas está respondido por uma família de métodos de aprendizagem máquina, denominado algoritmos separada.

É útil pensar sobre um algoritmo como um receitas e os dados como os ingredients. Um algoritmo indica como combinar e misturar os dados para obter uma resposta. Os computadores são como um blender. Se efetuar a maioria do trabalho do algoritmo para si e são pretty rápida.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Pergunta 1: É esta A ou B? utiliza algoritmos de classificação
Vamos começar com a pergunta: É esta A ou B?

![Algoritmos de classificação: É esta A ou B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Esta família de algoritmos denomina-se a classificação de duas classes.

É útil para qualquer pergunta que tem apenas duas respostas possíveis.

Por exemplo:

* Este tire falhará nos seguintes quilómetros 1.000: Sim ou não?
* Que coloca em mais clientes: um coupon $5 ou um desconto de 25%?

Também pode ser rephrased esta questão para incluir mais de duas opções: É esta A ou o B ou a C ou o D, etc.?  Esta opção é denominada classificação várias classes e de úteis quando tem vários — ou vários thousand — respostas possíveis. Classificação de várias classes escolhe o mais provável um.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Pergunta 2: Este é estranho? utiliza algoritmos de deteção de anomalias
A questão seguinte pode responder a ciência de dados é: É esta estranho? Esta questão está respondida por uma família de algoritmos denominado deteção de anomalias.

![Algoritmos de deteção de anomalias: É esta estranho?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Se tiver um cartão de crédito, já tiver benefited da deteção de anomalias. A empresa de cartão de crédito analisa os seus padrões de compra, para que eles podem alertá-lo para possível fraude. Os encargos que são "estranho" podem ser uma compra num arquivo onde não faz normalmente compras ou comprar um item anormalmente pricey.

Esta questão pode ser útil em muitas formas. Por exemplo:

* Se tiver um carro com pressão medidores, pode querer saber: É deste medidor pressão ler normal?
* Se estiver a monitorização da internet, iria querer saber: esta mensagem da internet é típico?

Deteção de anomalias sinalizadores inesperados ou invulgares eventos ou comportamentos. Proporciona processáveis onde Verifique se existem problemas.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Pergunta 3: Quanto? ou como muitas? utiliza algoritmos de regressão
Aprendizagem também pode prever a resposta como muito? ou como muitas? A família de algoritmos que responde a esta questão é denominada regressão.

![Algoritmos de regressão: quanto? ou como muitas?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Algoritmos de regressão fazer predições numéricos, tais como:

* O que será a temperatura a terça-feira seguinte?  
* O que será a minha vendas trimestre quarta?

Podem ajudar a responder a qualquer pergunta que lhe pede para um número.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Pergunta 4: Como é isto organizados? utiliza algoritmos de clustering
Agora as dois últimos são perguntas pouco mais avançado.

Por vezes, pretende para compreender a estrutura de um conjunto de dados - como isto organizados? Para esta questão, não terá de exemplos que já conhece os resultados para.

Existem muitas formas de tease enviados a estrutura dos dados. Uma abordagem é clustering. Separa dados em natural "clumps," para interpretação fácil. Com clustering, há sem uma resposta à direita.

![Clustering de algoritmos: como é isto organizados?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

São exemplos comuns de clusters perguntas:

* Os visualizadores autorizados, como os mesmos tipos de filmes?
* Os modelos de impressora falharem da mesma forma?

Por compreender a forma como os dados são organizados, pode melhor compreender - e prever - eventos e comportamentos.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Pergunta 5: O que devo fazer agora? utiliza reinforcement algoritmos de aprendizagem
A última pergunta – o que devo fazer agora? – utiliza uma família de algoritmos chamado reinforcement learning.

Reinforcement learning foi inspired por como brains de rats e humans respondem punishment e recompensas. Estes algoritmos Saiba de resultados e decidir a ação seguinte.

Normalmente, reinforcement learning é uma boa opção para sistemas automatizados que tem de efetuar muitas das decisões pequenas sem orientação humana.

![Reinforcement algoritmos de aprendizagem: o que devo fazer em seguida?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Perguntas responde são sempre sobre que ação deverão ser executadas - normalmente, um computador ou um robot. Os exemplos são:

* Se sou um sistema de controlo de temperatura de um próxima: ajustar a temperatura ou deixe-a qual está a?  
* Se sou um carro automática despertar: um leve amarelo, brake ou acelerar?  
* Para um vacuum robot: mantenha vacuuming ou volte atrás para a estação charging?

Algoritmos de aprendizagem reinforcement recolher dados, estes passam, aprendizagem da versão de avaliação e erro.

Para que fique-lo - pode responder a ciência de dados de perguntas a 5.

## <a name="next-steps"></a>Passos seguintes
* [Tente uma primeira experimentação de ciência de dados com Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](what-is-machine-learning.md)
