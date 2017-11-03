---
title: "Prever uma resposta com um modelo de regressão simples - o Azure Machine Learning | Microsoft Docs"
description: "Como criar um modelo de regressão simples para prever um preço em dados de ciência para principiantes, vídeo 4. Inclui um regressão linear com dados de destino."
keywords: "criar um modelo, o modelo simples, a predição do preço, o modelo de regressão simples"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: a28f1fab-e2d8-4663-aa7d-ca3530c8b525
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: cgronlun
ms.openlocfilehash: 10a9608bc35d44a800acda9406390c3944a86c2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="predict-an-answer-with-a-simple-model"></a>Prever uma resposta com um modelo simples
## <a name="video-4-data-science-for-beginners-series"></a>Vídeo 4: Ciência de dados para a série de principiantes
Saiba como criar um modelo de regressão simples para prever o preço de um diamante em dados de ciência para principiantes, vídeo 4. Iremos vai desenhar um modelo de regressão com dados de destino.

Para tirar o máximo partido da série, veja todos eles. [Aceda à lista de vídeos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Outros vídeos nesta série
*Dados de ciência para principiantes* é uma introdução rápida aos ciência de dados em cinco vídeos curtos.

* Vídeo 1: [a 5 questões de respostas de ciência de dados](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 seg.)*
* Vídeo de 2: [está os seus dados pronto para ciência de dados?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 seg.)*
* Vídeo 3: [fazer uma pergunta, poderá responder com dados](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 seg.)*
* Vídeo 4: Prever uma resposta com um modelo simples
* Vídeo 5: [copiar trabalho outras pessoas fazer ciência de dados](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 seg.)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcript: Prever uma resposta com um modelo simples
Bem-vindo ao vídeo quarto de "dados de ciência para principiantes" série. Este um, vamos criar um modelo simple e efetuar uma predição.

A *modelo* é uma história simplificada sobre os nossos dados. Posso irá mostrar-lhe o que devo dizer.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Recolher relevante, exata, ligado, existem dados suficientes
Diga que pretendo cafetaria para um diamante. Tenho uma anel que pertencia a minha materna com uma definição para uma diamante 1.35 carat e pretender obter uma ideia dos quanto será custo. Devo efetuar um bloco de notas e caneta para o arquivo de jewelry e posso anote o preço de todos os diamonds as maiúsculas e minúsculas e a quantidade de pesar no carats. Começar com o primeiro diamante - 1.01 carats e $7,366.

Agora percorrer e efetuar este procedimento para todos os outros diamonds no arquivo.

![Colunas de dados de diamante](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Tenha em atenção que a nossa lista tem duas colunas. Cada coluna tem um atributo diferente - ponderação no carats e preços - e cada linha é um ponto de dados individual que representa um diamante único.

Na realidade, criámos um pequeno conjunto de dados aqui - uma tabela. Tenha em atenção de que cumpre os nossos critérios da qualidade:

* Os dados são **relevantes** -ponderação é sem dúvida relacionadas com a preços
* Tem **exata** -iremos double-checked os preços que iremos anotá
* Tem **ligado** -não existem sem espaços em branco em qualquer uma destas colunas
* E, como Iremos ver, tem **suficiente** dados para responder a nossa pergunta

## <a name="ask-a-sharp-question"></a>Fazer uma pergunta sharp
Agora iremos irá implicar nosso pergunta de forma sharp: "quanto será-lo de custos para comprar um diamante 1.35 carat?"

A nossa lista não tem um diamante 1.35 carat no mesmo, para que irá utilizar o resto dos nossos dados para obter uma resposta à pergunta.

## <a name="plot-the-existing-data"></a>Representar os dados existentes
A primeira coisa que deverá fazê-lo é desenhar uma linha número horizontal chamada um eixo, para as ponderações de gráfico. O intervalo de ponderações é 0 para 2, pelo que iremos vai desenhar uma linha que abrange que intervalo e colocar os batimentos para cada carat meio.

Em seguida, vai desenhar um eixo vertical para registar o preço e ligá-lo para o eixo horizontal ponderação. Esta será em unidades de utilizados no compromisso. Agora que temos um conjunto de eixos coordenados.

![Eixos de ponderação e preço](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vamos agora a colocar estes dados e ativá-la para um *desenho de gráfico de dispersão*. Esta é uma excelente forma de visualizar os conjuntos de dados numéricos.

O primeiro ponto de dados, vamos eyeball numa linha vertical no 1.01 carats. Em seguida, iremos eyeball numa linha horizontal no $7,366. Em que as mesmas cumprem, iremos desenhar um ponto. Isto representa a nossa diamante primeiro.

Agora vamos percorrer cada diamante nesta lista e fazer a mesma coisa. Quando estamos através de, este é o que obtemos: um bunch de pontos, um para cada diamante.

![Dispersão desenho](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Desenhar o modelo através dos pontos de dados
Agora se observar os pontos e squint, a coleção de aspeto de uma linha fat, difusa. Iremos pode demorar nosso marcador e desenhar uma linha direitas através do mesmo.

Ao desenhar uma linha, criámos um *modelo*. Considere-o como tendo o mundo real e fazer uma versão de simplistic cartoon do mesmo. Agora, o cartoon está errada - a linha não passar por todos os pontos de dados. No entanto, é uma simplificação útil.

![Linha de regressão linear](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

O facto de que todos os pontos não podem passar exatamente através da linha está OK. Cientistas de dados explicam isto por indicar que não há do modelo - o que é a linha - e, em seguida, cada ponto tem algumas *ruído* ou *variância* associados à mesma. Há a relação perfeita subjacente, e, em seguida, existe o mundo gritty, real que adiciona o ruído e uncertainty.

Porque estamos a tentar responder à pergunta *quanto?* chama-se um *regressão*. E porque estamos a utilizar uma linha simples, é um *regressão linear*.

## <a name="use-the-model-to-find-the-answer"></a>Utilizar o modelo para encontrar a resposta
Agora temos um modelo e vamos pedir nosso pergunta: quanto um diamante 1.35 carat será custo?

Responder a nossa perguntas, eyeball 1.35 carats e desenhar uma linha vertical. Em que atravesse-a linha de modelo, iremos eyeball uma linha horizontal para o eixo dólar. Chegar à direita em 10 000. Boom! Que é a resposta: um diamante 1.35 carat custos sobre $10.000.

![Encontrar a resposta no modelo](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Criar um intervalo de confiança
É natural wonder preciso a forma como este predição é. É útil para saber se o diamante 1.35 carat será muito próximo $10.000, ou muito superior ou inferior. Para identificar este limite, vamos desenhar num envelope em torno da linha de regressão, que inclui a maioria nos pontos. Este envelope é designado por nossa *intervalo de confiança*: estamos pretty a certeza de que os preços coincidir com este envelope, porque o passado a maior parte dos mesmos ter. Iremos pode desenhar duas mais horizontal a partir de onde a linha 1.35 carat atravesse na parte superior e inferior do que envelope.

![Intervalo de confiança](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Agora podemos dizer algo sobre o nosso intervalo de confiança: estamos pode dizer confidencialidade que é o preço de um diamante 1.35 carat sobre $10.000 - mas poderá ser como tão baixo como $8000 e poderá ser tão elevado como $ 12 000.

## <a name="were-done-with-no-math-or-computers"></a>Iremos estiver pronto, sem qualquer bibliotecas ou computadores
Fizemos que cientistas de dados obterem paga fazer e fizemos apenas por desenho:

* Intermédias pedidas uma pergunta que iremos foi responder com dados
* Criámos uma *modelo* utilizando *regressão linear*
* Foi efetuada uma *predição*, completa com uma *intervalo de confiança*

E não utilizamos bibliotecas ou computadores para fazê-lo.

Agora se tinha tivemos obter mais informações, como...

* o corte do diamante
* variações de cor (como fechar o diamante está a ser branco)
* o número das inclusões no diamante

… .then que teria mais colunas. Nesse caso, bibliotecas torna-se útil. Se tiver mais de duas colunas, é difícil de desenhar pontos no documento. As contas permite-lhe ajustar muito bem dessa linha ou que plane aos seus dados.

Além disso, se, em vez de apenas alguns a diamonds, tivemos dois thousand ou milhões de duas, em seguida, pode fazer muito mais rapidamente do que funcionam com um computador.

Atualmente, tiver falamos sobre como fazê-lo regressão linear e Tornamos uma predição utilizando os dados.

Lembre-se de que veja vídeos em "Dados de ciência para principiantes" do Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Passos seguintes
* [Tente uma primeira experimentação de ciência de dados com Machine Learning Studio](create-experiment.md)
* [Obtenha uma introdução ao Machine Learning no Microsoft Azure](what-is-machine-learning.md)
