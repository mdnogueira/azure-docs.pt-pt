---
title: Como escolher algoritmos do machine learning | Microsoft Docs
description: "Como escolher o Azure Machine Learning algoritmos de aprendizagem supervisionada e não supervisionada nas experimentações clustering, classificação ou regressão."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a3b23d7f-f083-49c4-b6b1-3911cd69f1b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/25/2017
ms.author: garye
ms.openlocfilehash: 822bed8ffc43459dd053fda5b68f62f839b7d94b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Como escolher algoritmos para o Microsoft Azure Machine Learning
A resposta à pergunta "O algoritmo de aprendizagem devo utilizar?" é sempre "Depende." Depende do tamanho, qualidade e natureza dos dados. Depende das quais pretende fazer com a resposta. Depende de como as contas do algoritmo foi convertida instruções para o computador que estiver a utilizar. E depende quanto tempo tiver. Mesmo as cientistas de dados mais experiente não sabe qual algoritmo irá efetuar melhor antes de tentá-los.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>O algoritmo do Machine Learning Cheat folha
O **Microsoft Azure Machine Learning algoritmo Cheat folha** ajuda a escolher o direito de algoritmo de aprendizagem para as soluções de Análise Preditiva da biblioteca do Microsoft Azure Machine Learning dos algoritmos do computador.
Este artigo explica como utilizá-la.

> [!NOTE]
> Para transferir a folha de referência e seguir este artigo, aceda a [do Machine learning cábula de algoritmo para o Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Esta folha de referência tem um público-alvo muito específico em mente: um início dados scientist com nível de undergraduate aprendizagem, tentar escolher um algoritmo para começar no Azure Machine Learning Studio. Isto significa que que faz algumas generalizations e oversimplifications, mas aponta tem uma direção seguro. Também significa que existem muitos algoritmos não listados aqui. À medida que cresce Azure Machine Learning para abranger um conjunto mais completo dos métodos disponíveis, iremos adicioná-los.

Estas recomendações são compilados comentários e sugestões de várias cientistas de dados e de especialistas do machine learning. Iremos não aceita em tudo, mas tiver a tentou harmonize nosso opinions para um consenso aproximado. Na maioria das declarações de estado de disagreement começa com "Depende..."

### <a name="how-to-use-the-cheat-sheet"></a>Como utilizar a folha de referência
As etiquetas de caminho e o algoritmo no gráfico como de leitura "para  *&lt;etiqueta do caminho&gt;*, utilize  *&lt;algoritmo&gt;*." Por exemplo, "para *velocidade*, utilize *dois classe regressão logística da*." Por vezes, mais do que um ramo aplica-se.
Por vezes, nenhuma delas são uma opção perfeita. Está a se destina a ser recomendações de regra de botão, pelo que não se preocupe se a ser exato.
Vários cientistas de dados que posso talked com referida que a forma apenas se encontrar o algoritmo muito mais é para experimentar todos eles.

Eis um exemplo do [galeria da Cortana Intelligence](http://gallery.cortanaintelligence.com/) de uma experiência que tenta vários algoritmos contra os mesmos dados e compara os resultados: [comparar classificadores de classe multi: letra reconhecimento](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Para transferir e imprimir um diagrama da descrição geral das funcionalidades do Machine Learning Studio, consulte o artigo [Diagrama da descrição geral das funcionalidades do Azure Machine Learning Studio](studio-overview-diagram.md).
> 
> 

## <a name="flavors-of-machine-learning"></a>Tipos de aprendizagem
### <a name="supervised"></a>Supervisionado
Algoritmos de aprendizagem supervisionado fazer predições com base num conjunto de exemplos. Por exemplo, os preços as cotações histórico podem ser utilizadas para hazard guesses em futuros preços. Cada exemplo utilizado para formação é denominado com o valor de interesse — neste caso as cotações preço. Procura um algoritmo do learning supervisionado padrões em dessas etiquetas de valor. Pode utilizar todas as informações que podem ser relevantes — o dia da semana, a época, os dados da empresa financeiros, o tipo da indústria, a presença de eventos geopolíticas acontece — e procura de cada algoritmo para diferentes tipos de padrões. Após encontrar o padrão de melhor o algoritmo pode, utiliza esse padrão para fazer predições para dados de teste sem etiqueta — preços do amanhã.

Learning supervisionado é um tipo popular e mais útil de aprendizagem. Com uma exceção, todos os módulos no Azure Machine Learning são supervisionados Algoritmos de aprendizagem. Existem vários tipos específicos de learning supervisionado são representados no Azure Machine Learning: deteção de classificação, regressão e anomalias.

* **Classificação**. Quando os dados estão a ser utilizados para prever uma categoria, learning supervisionado também é denominado classificação. Este é o caso, ao atribuir uma imagem como uma imagem de um 'cat' ou 'preguiçoso'. Quando existem apenas duas opções, é denominado **duas classes** ou **classificação binomial**. Quando existirem mais categorias, como quando prever winner do tournament NCAA Março Madness, este problema é conhecido como **classe multi classificação**.
* **Regressão**. Quando está a ser prever um valor, tal como acontece com os preços as cotações, learning supervisionado é chamado regressão.
* **Deteção de anomalias**. Por vezes, o objetivo é para identificar os pontos de dados que são simplesmente invulgares. Na deteção de fraudes, por exemplo, os padrões de gastos do cartão de crédito bastante invulgar são suspeita. As possíveis variações são, por isso, vários e os exemplos de formação, por isso, alguns, que não é exequível para saber que atividade fraudulenta aspeto. A abordagem que demora a deteção de anomalias é simplesmente saiba que atividade normal aspeto (utilizando um transações não fraudulenta de histórico) e identificar tudo o que é significativamente diferentes.

### <a name="unsupervised"></a>Não supervisionado
No learning supervisionado, pontos de dados não têm nenhuma das etiquetas associadas. Em vez disso, o objetivo de um algoritmo de aprendizagem supervisionados é para organizar os dados de alguma forma ou para descrever a respetiva estrutura. Isto pode significar agrupá-lo para clusters ou ao localizar o formas diferentes de observar dados complexos para que seja apresentado mais simples ou mais organizados.

### <a name="reinforcement-learning"></a>Reinforcement learning
No reinforcement learning, o algoritmo obtém escolher uma ação em resposta a cada ponto de dados. O algoritmo do learning também recebe um sinal de reward um curto período de tempo mais tarde, que indica como bom foi da decisão.
Com base nisso, o algoritmo modifica a estratégia para alcançar o reward mais elevado. Atualmente não existem nenhum reinforcement learning algoritmo os módulos no Azure Machine Learning. Reinforcement learning é comuns em robotics, onde o conjunto das leituras dos sensores um ponto no tempo é um ponto de dados e o algoritmo tem de escolher a ação seguinte o robot. Também é que um natural caber para Internet das coisas aplicações.

## <a name="considerations-when-choosing-an-algorithm"></a>Considerações ao escolher um algoritmo
### <a name="accuracy"></a>Precisão
Obter a resposta mais exata possíveis nem sempre é necessário.
Por vezes, uma aproximação do seu é adequada, consoante o que pretende utilizá-lo para. Se for esse o caso, poderá conseguir cortar o significativamente o tempo de processamento por se mantenha com métodos aproximados mais. Outra vantagem de métodos aproximados mais é que estes naturalmente tendem a evitar [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Tempo de preparação
O número de minutos ou horas necessárias para preparar um modelo de great deal varia entre algoritmos. Tempo de preparação, muitas vezes, rigorosamente está associado a precisão — um normalmente acompanha o outro. Além disso, alguns algoritmos são mais confidenciais para o número de pontos de dados que outras pessoas.
Quando tempo é limitado, pode unidade a escolha de algoritmos, especialmente quando o conjunto de dados é grande.

### <a name="linearity"></a>Linearity
Muitos algoritmos de machine learning tornar a utilização de linearity. Algoritmos de classificação linear partem do princípio de que as classes podem ser separadas por uma linha direitas (ou respetiva analógica superior dimensional). Estes incluem regressão logística da e suportam máquinas de vetor (conforme implementado no Azure Machine Learning).
Algoritmos de regressão linear partem do princípio de que as tendências de dados, siga uma linha simples. Nesses pressupostos não são incorretos para alguns problemas, mas em outros colocar precisão para baixo.

![Limite de classe não linear][1]

***Limite de classe não linear*** *-depender de um algoritmo de classificação linear resultaria numa precisão baixa*

![Dados com uma tendência nonlinear][2]

***Dados com uma tendência nonlinear*** *-utilizando um método de regressão linear iria gerar muito erros maior do que o necessário*

Apesar dos perigos lineares algoritmos são muito populares como uma primeira linha do ataque. Estes tendem a ser algorithmically simples e rápida para preparar.

### <a name="number-of-parameters"></a>Número de parâmetros
Os parâmetros são botões que obtém um scientist de dados para ativar ao configurar um algoritmo. Estes são os números que afetam o comportamento do algoritmo, tais como a tolerância a erros ou o número de iterações ou opções entre variantes da forma como o algoritmo se comporta. A hora de formação e a precisão do algoritmo, por vezes, podem ser bastante confidenciais para obter apenas as definições corretas. Normalmente, os algoritmos com parâmetros de grandes quantidades requerem o maioria das e erro para encontrar uma boa combinação.

Em alternativa, há um [varrimento de parâmetro](algorithm-parameters-optimize.md) blocos de módulo no Azure Machine Learning, este tenta automaticamente todas as combinações de parâmetro qualquer granularidade que escolher. Embora seja uma excelente forma de certificar-se de que ter abrangido o espaço de parâmetro, o tempo necessário para preparar um modelo exponencialmente aumenta com o número de parâmetros.

O upside é que ter vários parâmetros normalmente indica que um algoritmo tem uma maior flexibilidade. Muitas vezes, pode obter uma precisão muito bom. Fornecida que pode encontrar a combinação certa de definições de parâmetro.

### <a name="number-of-features"></a>Número de funcionalidades
Para determinados tipos de dados, o número de funcionalidades pode ser muito grande em comparação com o número de pontos de dados. Isto é normalmente o caso de genetics ou dados textual. Grande número de funcionalidades pode bog baixo algumas algoritmos de aprendizagem automática, tornando formação unfeasibly longo do tempo. Máquinas de Vetor de suporte são particularmente adequadas para este cenário (ver abaixo).

### <a name="special-cases"></a>Casos especiais
Alguns algoritmos de aprendizagem tornar pressupostos específicos sobre a estrutura de dados ou os resultados pretendidos. Se pode encontrar um que se adeque às suas necessidades, este pode dar-lhe resultados mais úteis, predições mais exatas ou tempos mais rápidos de formação.

| **Algoritmo** | **Precisão** | **Tempo de preparação** | **Linearity** | **Parâmetros** | **Notas** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classificação de duas classes** | | | | | |
| [regressão logística da](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [floresta de decisão](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [jungle de decisão](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Requisitos de espaço de memória insuficiente |
| [árvore de decisões elevada](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Requisitos de espaço de memória grande |
| [rede neuronal](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[Personalização adicional, é possível](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [perceptron média](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [máquina de vetor com suporte](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Boa para conjuntos de funcionalidades grande |
| [máquina de vetor com suporte localmente profunda](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Boa para conjuntos de funcionalidades grande |
| [Máquina do ponto dos Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Classificação de classe multi** | | | | | |
| [regressão logística da](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [floresta de decisão](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [jungle de decisão](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Requisitos de espaço de memória insuficiente |
| [rede neuronal](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[Personalização adicional, é possível](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [uma-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Ver as propriedades do método de duas classes selecionado |
| **Regressão** | | | | | |
| [linear](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Bayesiano linear](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [floresta de decisão](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [árvore de decisões elevada](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Requisitos de espaço de memória grande |
| [quantile floresta rápida](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Distribuições em vez de ponto predições |
| [rede neuronal](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[Personalização adicional, é possível](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [POISSON](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Tecnicamente registo-linear. Para prever contagens |
| [ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Para prever a ordenação de classificação |
| **Deteção de anomalias** | | | | | |
| [máquina de vetor com suporte](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Especialmente ideais para conjuntos de funcionalidades grande |
| [Deteção de anomalias baseado em PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [Significa K](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |Um algoritmo de clustering |

**Propriedades de algoritmo:**

**●** -mostra precisão excelente, tempos de preparação rápida e a utilização de linearity

**○** -mostra boa precisão e tempos de preparação moderada

## <a name="algorithm-notes"></a>Notas de algoritmo
### <a name="linear-regression"></a>Regressão linear
Tal como mencionado anteriormente, [regressão linear](https://msdn.microsoft.com/library/azure/dn905978.aspx) se adequa a uma linha (ou plane ou hyperplane) para o conjunto de dados. É um workhorse, simple e rápida, mas poderá ser demasiado simplistic para alguns problemas.
Procure aqui uma [tutorial de regressão linear](linear-regression-in-azure.md).

![Dados com uma tendência linear][3]

***Dados com uma tendência linear***

### <a name="logistic-regression"></a>Regressão logística da
Embora confusingly inclui 'regressão' no nome, regressão logística da é, na verdade, uma ferramenta poderosa para [duas classes](https://msdn.microsoft.com/library/azure/dn905994.aspx) e [várias classes](https://msdn.microsoft.com/library/azure/dn905853.aspx) classificação. É simples e rápida. O facto de que utiliza de um '-em forma de curva em vez de uma linha direitas faz com que um ajuste natural para dividir os dados em grupos. Limites de classe linear fornecem de regressão logística da, por isso, quando o utiliza, certifique-se uma aproximação do seu linear algo que pode em direto com.

![Regressão logística aos dados de duas classes com apenas uma funcionalidade][4]

***Um regressão logística aos dados de duas classes com apenas uma funcionalidade*** *-o limite de classe é o ponto no qual a curva logística da é apenas tão próximos ambas as classes*

### <a name="trees-forests-and-jungles"></a>Árvores, florestas e dispõe
As decisões florestas ([regressão](https://msdn.microsoft.com/library/azure/dn905862.aspx), [duas classes](https://msdn.microsoft.com/library/azure/dn906008.aspx), e [várias classes](https://msdn.microsoft.com/library/azure/dn906015.aspx)), as decisões dispõe ([duas classes](https://msdn.microsoft.com/library/azure/dn905976.aspx) e [várias classes](https://msdn.microsoft.com/library/azure/dn905963.aspx)) e elevada árvores de decisões ([regressão](https://msdn.microsoft.com/library/azure/dn905801.aspx) e [duas classes](https://msdn.microsoft.com/library/azure/dn906025.aspx)) todos os baseiam-se em árvores de decisões, uma conceito de aprendizagem dos. Existem muitos variantes de árvores de decisões, mas são a mesma coisa — subdividir o espaço de funcionalidade em regiões com basicamente a mesma etiqueta. Estes podem ser regiões da categoria consistente ou do valor constante, dependendo se está a fazer classificação ou regressão.

![Árvore de decisões subdivides um espaço de funcionalidade][5]

***Uma árvore de decisão subdivides um espaço de funcionalidade em regiões de aproximadamente uniform valores***

Porque um espaço de funcionalidade pode ser subdividido em regiões arbitrariamente pequenas, é fácil imagine dividindo-lhe suficiente para ter um ponto de dados por região. Este é um exemplo de overfitting extremos. Para evitar isto, um grande conjunto de árvores são construídos com especial cuidado matemática decorrido que o árvores não estão correlacionados. A média desta "floresta de decisão" é uma árvore que evita overfitting. Florestas de decisão podem utilizar uma grande quantidade de memória. Dispõe de decisão é uma variante que consome menos memória tendo um período de tempo de formação ligeiramente superior.

Árvores de decisões elevada evitar overfitting ao limitar o número de vezes que podem subdividir e são permitidos como alguns pontos de dados em cada região. O algoritmo constrói uma sequência de árvores, cada um dos quais aprende para compensar o erro deixado por árvore antes. O resultado é um learner muito exata que tende a utilizar uma grande quantidade de memória. Para obter a descrição técnica completa, consulte [documento original de Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[A rápida regressão de quantile floresta](https://msdn.microsoft.com/library/azure/dn913093.aspx) é uma variação de árvores de decisões para o caso especial em que pretende saber não só o valor típico (mediano) dos dados dentro de uma região, mas também na forma de quantiles a distribuição da mesma.

### <a name="neural-networks-and-perceptrons"></a>As redes neurais e perceptrons
As redes neurais são "-brain"-inspired que abrangem de algoritmos de aprendizagem [várias classes](https://msdn.microsoft.com/library/azure/dn906030.aspx), [duas classes](https://msdn.microsoft.com/library/azure/dn905947.aspx), e [regressão](https://msdn.microsoft.com/library/azure/dn905924.aspx) problemas. Entram uma variedade de infinita, mas as redes neurais no Azure Machine Learning são todas a forma de direcionados gráficos Acíclico. Isto significa que as funcionalidades de entrada são transmitidas reencaminhar (nunca trás) através de uma sequência de camadas antes de a ser transformado saídas. Em cada camada entradas são ponderadas em várias combinações, somadas e transmitidas para a próxima camada. Esta combinação de cálculos simples resulta na capacidade de saber as tendências de limites e os dados de classe sofisticadas, seemingly por magic. Redes em camadas muitos desta ordenação efetuar a "aprendizagem profunda" que fuels, tanto que técnico de relatórios e ciência fiction.

Este elevado desempenho não são fornecidos gratuitamente, embora. As redes neurais podem demorar muito tempo a formação, especialmente para grandes conjuntos de dados com muitas das funcionalidades. Também têm mais parâmetros do que a maioria dos algoritmos, o que significa que varrimento de parâmetro expande o tempo de preparação de great deal.
E para esses overachievers quem pretendem [especificar as seus próprios estrutura da rede](http://go.microsoft.com/fwlink/?LinkId=402867), as possibilidades são inexhaustible.

![Limites adquiridos por redes neurais][6]
***os limites de redes neurais podem ser complexas e dados***

O [perceptron apresentou uma média de duas classes](https://msdn.microsoft.com/library/azure/dn906036.aspx) é resposta redes neurais skyrocketing tempos de preparação. Utiliza uma estrutura de rede que fornece os limites de classe linear. Está quase primitivo por normas de hoje, mas tem um histórico do trabalho robustly longo e suficientemente pequena para saber mais rapidamente.

### <a name="svms"></a>SVMs
Máquinas de vetor de suporte (SVMs) localizar os limites que separa classes por como ao nível uma margem quanto possível. Quando duas classes não podem ser claramente separadas, algoritmos de localizar o limite de melhor que estes requisitos podem. Como escritos no Azure Machine Learning, o [SVM de duas classes](https://msdn.microsoft.com/library/azure/dn905835.aspx) fazê-lo com apenas uma linha simples. (No enunciar SVM utiliza um kernel linear.) Uma vez que faz esta aproximação do seu linear, é capaz de executar razoavelmente depressa. Em que realmente shines é com funcionalidade intense dados, como o texto ou genomic. Nestes casos SVMs conseguem separar classes mais rapidamente e com menos overfitting a maioria dos outros algoritmos, para além das que requerem apenas uma modest quantidade de memória.

![Limite de classe de máquina de vetor de suporte][7]

***Um limite de classe de máquina de vetor de suporte típico maximiza a margem de separação de duas classes***

Outro produto da Microsoft Research, o [SVM localmente profunda de duas classes](https://msdn.microsoft.com/library/azure/dn913070.aspx) é uma variante não linear do SVM que mantém a maioria da eficiência de memória e velocidade da versão linear. Este é ideal para casos onde a abordagem linear não concede precisos suficientemente respostas. Os programadores mantida-rápido ao eliminar para baixo o problema para um bunch de pequenos problemas SVM lineares. Leia o [completa Descrição](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) para os detalhes sobre como possam solicitados desativar este truque.

Utilizar uma extensão clever de nonlinear SVMs, o [SVM de uma classe](https://msdn.microsoft.com/library/azure/dn913103.aspx) desenha limite que totalmente descreve o conjunto completo de dados. É útil para deteção de anomalias. Quaisquer novos pontos de dados que se inserem até que ponto fora de limites que são invulgares ser noteworthy.

### <a name="bayesian-methods"></a>Métodos de Bayesiano
Os métodos de Bayesiano ter uma elevada disponibilidade desejável qualidade: estes evitar overfitting. Tal, efetuar algumas pressupostos antecipadamente sobre a distribuição da resposta provável. Outro byproduct desta abordagem é que têm muito poucos parâmetros. Azure Machine Learning tem ambos os algoritmos de Bayesiano para ambos os classificação ([máquina do ponto dos Bayes de duas classes](https://msdn.microsoft.com/library/azure/dn905930.aspx)) e regressão ([regressão linear Bayesiano](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Tenha em atenção que estas partem do princípio de que os dados podem ser divididos ou caber com uma linha direitas.

Na nota histórica, as máquinas de ponto dos Bayes foram desenvolvidas junto da Microsoft Research. Têm de algum trabalho teórico exceptionally fantástica atrás-los. O estudante interessado é direcionado para o [artigo original na JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) e um [blogue insightful por Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmos especializados
Se tiver um objetivo muito específico que podem estar a ser luck. Dentro da coleção do Azure Machine Learning, existem algoritmos especialização em:

- classificar que predição ([regressão ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)),
- Contagem de predição ([regressão de Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx)),
- Deteção de anomalias (um com base no [analysis componentes principal](https://msdn.microsoft.com/library/azure/dn913102.aspx) e outra com base no [máquina de vetor com suporte](https://msdn.microsoft.com/library/azure/dn913103.aspx)s)
- clustering ([K significa](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![Deteção de anomalias baseado em PCA][8]

***Deteção de anomalias baseado em PCA*** *-a vasta maioria dos dados se enquadrar uma distribuição stereotypical; pontos deviating significativamente a partir dessa distribuição são suspeita*

![Conjunto de dados agrupados utilizando meios K][9]

***Um conjunto de dados é agrupado nas cinco clusters utilizando meios K***

Há também um ensemble [classificador de várias classes de uma-v-todas](https://msdn.microsoft.com/library/azure/dn905887.aspx), que interrompe o problema de classificação N classe para problemas de classificação de duas classes N-1. A precisão, tempo de formação e propriedades de linearity são determinadas pelos classificadores de duas classes utilizados.

![Classificadores de duas classes combinadas para formar um classificador de três classes][10]

***Um par de classificadores de duas classes de combinação para formar um classificador de três classes***

Azure Machine Learning também inclui acesso a uma estrutura de aprendizagem poderosas sob o título da [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW defies categorização aqui, desde que pode obter classificação e regressão problemas e até mesmo pode obter dados parcialmente sem etiqueta. Pode configurar para utilizar qualquer um dos vários algoritmos de otimização, funções de perda e algoritmos de aprendizagem. Foi concebido partir do zero cópias de segurança para ser extremamente rápida, eficiente e paralelas. Processa a funcionalidade seja extremamente grandes conjuntos com pouca esforço aparente.
Foi iniciada e guiado através da João Langford do Microsoft Research próprio, VW é uma entrada de uma fórmula num campo de algoritmos de gráfico de cotações carro. Nem todos os problemas se adequa VW, mas se sua, poderá ser vale seu tempo a climb a curva de aprendizagem na respetiva interface de rede. Também está disponível como [código fonte aberto autónomo](https://github.com/JohnLangford/vowpal_wabbit) em vários idiomas.

## <a name="more-help-with-algorithms"></a>Obter ajuda com algoritmos
* Para um gráfico informativo transferível que descreve algoritmos e fornece exemplos, consulte [transferível gráfico informativo: Noções básicas com exemplos do algoritmo de aprendizagem máquina](basics-infographic-with-algorithm-examples.md).
* Para obter uma lista por categoria de todos os os algoritmos de machine learning disponíveis no Azure Machine Learning Studio, consulte [inicializar modelo] [ initialize-model] no algoritmo do Machine Learning Studio e ajudar do módulo.
* Para obter uma lista alfabética completa de algoritmos e módulos no Azure Machine Learning Studio, consulte [lista A-Z dos módulos de Machine Learning Studio] [ a-z-list] no algoritmo do Machine Learning Studio e o módulo de ajuda.
* Para transferir e imprimir um diagrama que proporcione uma descrição geral das funcionalidades do Azure Machine Learning Studio, consulte [diagrama de descrição geral das capacidades do Azure Machine Learning Studio](studio-overview-diagram.md).


<!-- Reference links -->
[initialize-model]: https://msdn.microsoft.com/library/azure/dn905812.aspx
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx

<!-- Media -->

[1]: ./media/algorithm-choice/image1.png
[2]: ./media/algorithm-choice/image2.png
[3]: ./media/algorithm-choice/image3.png
[4]: ./media/algorithm-choice/image4.png
[5]: ./media/algorithm-choice/image5.png
[6]: ./media/algorithm-choice/image6.png
[7]: ./media/algorithm-choice/image7.png
[8]: ./media/algorithm-choice/image8.png
[9]: ./media/algorithm-choice/image9.png
[10]: ./media/algorithm-choice/image10.png
