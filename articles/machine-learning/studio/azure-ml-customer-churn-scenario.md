---
title: Analisar cliente Churn com Machine Learning | Microsoft Docs
description: "Caso prático de desenvolver um modelo para analisar e a classificação de volume de alterações de cliente integrado"
services: machine-learning
documentationcenter: 
author: jeannt
manager: jhubbard
editor: cgronlun
ms.assetid: 1333ffe2-59b8-4f40-9be7-3bf1173fc38d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeannt
ms.openlocfilehash: b3dca9e75df2d057d7ee1b314faac490e5f10a08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Utilizar o Azure Machine Learning para Analisar a Taxa de Abandono de Clientes
## <a name="overview"></a>Descrição geral
Este artigo apresenta uma implementação de referência de um projeto de análise de volume de alterações de cliente que é criada utilizando o Azure Machine Learning. Neste artigo, vamos discutir modelos genéricos associados de forma holística resolver o problema de volume de alterações do cliente industriais. Podemos também medir a precisão dos modelos que são criados utilizando o Machine Learning e iremos avaliar as direções para o desenvolvimento ainda mais.  

### <a name="acknowledgements"></a>Confirmações
Esta fase experimental foi desenvolvido e testado pela Serge Berger, Scientist de dados Principal na Microsoft e Roger Barga, anteriormente o Gestor de produto do Microsoft Azure Machine Learning. A equipa de documentação do Azure gratefully reconhece os seus conhecimentos e obrigado-los para partilhar este documento técnico.

> [!NOTE]
> Os dados utilizados para esta fase experimental não estão disponíveis publicamente. Para obter um exemplo de como criar um modelo de machine learning para análise de volume de alterações, consulte: [revenda churn modelo](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1) no [galeria da Cortana Intelligence](http://gallery.cortanaintelligence.com/)
> 
> 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-problem-of-customer-churn"></a>O problema de volume de alterações do cliente
Empresas no mercado de consumidor e em todos os setores de enterprise tem de lidar com o volume de alterações. Por vezes, volume de alterações é excessivo e influencia as decisões de políticas. A solução tradicional é a prever churners propensity elevado e a abordar as respetivas necessidades através de um serviço concierge, marketing campanhas, ou ao aplicar dispensations especiais. Estas abordagens podem variar da indústria da indústria e mesmo a partir de um cluster de consumidor específica para outro dentro de uma indústria (por exemplo, telecomunicações).

O fator comum é que as empresas têm de minimizar os esforços de retenção estes cliente especiais. Assim, a metodologia natural seria para cada cliente com a probabilidade do volume de alterações de pontuação e resolver os principal N aqueles. Os clientes superiores poderão ser mais rentáveis aqueles; Por exemplo, em cenários mais sofisticados, uma função de lucros estiver utilizada durante a seleção de candidatos para dispensation especial. No entanto, estas considerações são apenas uma parte da estratégia holística para lidar com o volume de alterações. Empresas também tem de ter em risco de conta (e tolerância ao risco associado), o nível e os custos da intervenção e segmentação plausible cliente.  

## <a name="industry-outlook-and-approaches"></a>Outlook da indústria e abordagens
Processamento sofisticado volume de alterações é um sinal de um setor madura. O exemplo clássico é o setor do umts onde os subscritores são conhecidos frequentemente para mudar a partir de um fornecedor para outro. Este volume de alterações voluntário é uma preocupação prime. Além disso, fornecedores tem acumulados conhecimento significativo sobre *churn controladores*, quais são os fatores que os clientes para mudar de unidade.

Por exemplo, escolha handset ou o dispositivo é um controlador bem conhecido do volume de alterações no negócio telemóvel. Como resultado, uma política popular é subsidize o preço de um handset para novos subscritores e charging um preço completo para clientes existentes para uma atualização. Historicamente, esta política gerou hopping a partir de um fornecedor para outro aos clientes obter um desconto nova, que por sua vez, foi-lhe pedido que Fornecedores para refinar as respetivas estratégias.

Elevada volatility no ofertas handset é um fator que invalida muito rapidamente modelos do volume de alterações que se baseiam em modelos de handset atual. Além disso, telemóveis não são apenas os dispositivos telecommunication; Também estão declarações de forma (considere o iPhone), e estas redes sociais predictors estão fora do âmbito dos conjuntos de dados do umts regular.

O resultado para a modelação net é que não é possível devise uma política de som, simplesmente ao conhecido razões para volume, eliminando os. Na verdade, uma estratégia de modelação contínua, incluindo modelos clássicos que quantificar categórico variáveis (tais como árvores de decisões), é **obrigatório**.

Utilizar conjuntos de macrodados nos respetivos clientes, as organizações estiver a efetuar análise de macrodados (em especial, deteção de volume de alterações com base nos dados de grandes) como uma abordagem eficaz para o problema. Pode encontrar mais informações sobre a abordagem de macrodados para o problema de volume de alterações nas recomendações na secção ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodologia para volume de alterações de cliente de modelo
Um processo de resolução de problemas comuns para resolver o volume de alterações do cliente é descrito no números 1 a 3:  

1. Um modelo de risco permite-lhe a ter em consideração o impacto das ações de probabilidade e risco.
2. Um modelo de intervenção permite-lhe a ter em consideração a forma como o nível de intervenção pode afetar a probabilidade do volume de alterações e a quantidade de cliente (CLV) do valor de duração.
3. Esta análise presta-se para uma análise de qualitative é escalada para uma campanha de marketing proativa que tenha como destino de segmentos de cliente para fornecer a oferta ideal.  

![][1]

Esta abordagem reencaminhar looking é a melhor forma para tratar do volume de alterações, mas vem com a complexidade: temos que desenvolver um modelo com múltiplos archetype e rastreio as dependências entre os modelos. A interação entre os modelos pode ser encapsulada conforme mostrado no diagrama seguinte:  

![][2]

*Figura 4: Unified archetype múltiplos modelo*  

Interação entre os modelos é chave se estamos fornecer uma abordagem holística a retenção de cliente. Cada modelo necessariamente degrada ao longo do tempo; Por conseguinte, a arquitetura é um ciclo implícito (semelhante ao archetype definido pela norma de extração de dados de CRISP DM, [***3***]).  

O ciclo de geral de marketing de decisão de risco segmentação/decomposição ainda é uma estrutura generalizada, o que é aplicável a muitos dos problemas empresariais. Análise de volume de alterações é simplesmente um representante forte deste grupo de problemas, porque-exhibits todos os traits um problema de comerciais complexos que não permite uma solução preditiva simplificada. Os aspetos da abordagem moderna para churn sociais não são particularmente realçados na abordagem, mas os aspetos de redes sociais são encapsulados em archetype modelação, como seriam em qualquer modelo.  

Um complemento interessante aqui é a análise de macrodados. Hoje telecommunication e as empresas de revenda recolhem dados exaustivas sobre os seus clientes e previr, pode facilmente que a necessidade de conectividade com múltiplos modelo irá tornar-se uma tendência comuns, determinada emergentes tendências, tais como a Internet das coisas e ubíqua dispositivos, que permite que o negócio recorrer a soluções smart em várias camadas.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementar o archetype modelação no Machine Learning Studio
Tendo em conta o problema descrito apenas, o que é a melhor forma de implementar uma integrada modelação e a abordagem de classificação? Nesta secção, vamos demonstrar como podemos conseguida isto utilizando o Azure Machine Learning Studio.  

A abordagem múltiplos modelo é um tem ao conceber uma archetype global para o volume de alterações. Mesmo a classificação (preditiva) parte da abordagem deve ser múltiplos modelo.  

O diagrama seguinte mostra o protótipo que foi criada, que utiliza quatro algoritmos classificação no Machine Learning Studio para prever o volume de alterações. O motivo para utilizar uma abordagem múltiplos modelo não é apenas criar um classificador de ensemble para aumentar a precisão, mas também para proteger contra ajuste superior e para melhorar a seleção de funcionalidades prescritiva.  

![][3]

*Figura 5: Protótipo de uma abordagem de modelação de volume de alterações*  

As secções seguintes fornecem mais detalhes sobre o protótipo da classificação de modelo que são implementados utilizando o Machine Learning Studio.  

### <a name="data-selection-and-preparation"></a>Seleção de dados e preparação
Os dados utilizados para criar os modelos e clientes de pontuação foi obtido a partir de uma solução de vertical CRM, com os dados ocultados para proteger a privacidade do cliente. Os dados contêm informações sobre 8.000 subscrições nos E.U.A. e combina três origens: aprovisionamento de dados (metadados de subscrição), dados de atividade (utilização do sistema) e dados de suporte de cliente. Os dados não incluem qualquer empresariais relacionados com informações sobre os clientes; Por exemplo, não inclua pontuações de metadados ou crédito loyalty.  

Para uma simplicidade ETL e processos de limpeza de dados estão fora do âmbito porque partimos do princípio que a preparação de dados já foi foi efetuados noutro local.   

A seleção de funcionalidades para a modelação é com base na classificação preliminar significância do conjunto de predictors, incluídos no processo do que utiliza o módulo de floresta aleatório. Para a implementação no Machine Learning Studio, iremos calcular a média, mediana e os intervalos para funcionalidades representativos. Por exemplo, adicionámos agregados para os dados qualitativa, tais como os valores mínimos e máximos para a atividade do utilizador.    

Podemos também capturadas informações temporais durante os mais recentes seis meses. Analisámos dados durante um ano e iremos estabelecido que, mesmo se existirem tendências estatisticamente significativas, o efeito no volume de alterações é significativamente o seu depois de seis meses.  

O ponto mais importante é que o processo completo, incluindo ETL, seleção de funcionalidades e modelação foi implementado no Machine Learning Studio, utilizando as origens de dados no Microsoft Azure.   

Os diagramas seguintes mostram os dados que foi utilizados.  

![][4]

*Figura 6: Excerpt da origem de dados (ocultada)*  

![][5]

*Figura 7: Funcionalidades extraídas da origem de dados*
 

> Tenha em atenção que estes dados privados e, por conseguinte, não não possível partilhar o modelo e os dados.
> No entanto, para um modelo semelhante utilizando dados publicamente disponíveis, consulte este exemplo experimentação no [galeria da Cortana Intelligence](http://gallery.cortanaintelligence.com/): [Telco cliente Churn](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Para saber mais sobre como implementar um modelo de análise de volume de alterações com o Cortana Intelligence Suite, também recomendamos [este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) pelo Gestor de programa sénior Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmos utilizados no protótipo
Utilizamos os seguintes quatro algoritmos do machine learning para criar o protótipo (nenhuma personalização):  

1. Regressão logística da (LR)
2. Árvore de decisões elevada (BT)
3. Média perceptron (AP)
4. Máquina de vetor com suporte (SVM)  

O diagrama seguinte ilustra uma parte da superfície de desenho a experimentação, que indica a sequência em que os modelos foram criados:  

![][6]  

*Figura 8: Criar modelos no Machine Learning Studio*  

### <a name="scoring-methods"></a>Métodos de classificação
Iremos classificada quatro modelos utilizando um conjunto de dados de formação identificados.  

Submetemos também o classificação do conjunto de dados para um modelo comparável criado utilizando a edição de ambiente de trabalho do SAS Enterprise Miner 12. Iremos medida a exatidão o modelo SAS e quatro modelos de Machine Learning Studio.  

## <a name="results"></a>Resultados
Nesta secção, iremos apresentar nosso findings sobre a precisão dos modelos, com base no conjunto de dados classificação.  

### <a name="accuracy-and-precision-of-scoring"></a>Precisão e a precisão da classificação
Geralmente, a implementação no Azure Machine Learning é atrás SAS precisão por cerca de 10 a 15% (área em curva ou AUC).  

No entanto, a métrica mais importante no volume de alterações é a taxa de misclassification: Isto é, de churners os principais N como previsto pelo classificador, qual deles, na verdade, de foi **não** churn e ainda recebeu um tratamento especial? O diagrama seguinte compara este taxa misclassification para todos os modelos:  

![][7]

*Figura 9: Área de protótipo Passau em curva*

### <a name="using-auc-to-compare-results"></a>Utilizar AUC para comparar os resultados
Área na curva (AUC) é uma métrica que representa uma medida global de *separability* entre as distribuições de pontuações das classificações para populações positivos e negativos. É semelhante ao gráfico Recetor operador característica (ROC) tradicionais, mas uma diferença importante é que a métrica AUC não necessita de escolher um valor de limiar. Em vez disso, este resume os resultados de ativação pós-falha **todos os** possíveis opções. Em contrapartida, o gráfico ROC tradicional mostra a taxa de positiva no eixo vertical e a taxa de positiva falsa no eixo horizontal e varia de acordo com o limiar de classificação.   

AUC é geralmente utilizado como uma medida de vale para diferentes algoritmos (ou sistemas diferentes) porque permite modelos a ser comparada através dos respetivos valores AUC. Esta é uma abordagem mais popular no indústrias como meteorology e biosciences. Assim, AUC representa uma ferramenta para avaliar o desempenho de classificador popular.  

### <a name="comparing-misclassification-rates"></a>Comparar as taxas de misclassification
Iremos comparar as taxas de misclassification no conjunto de dados em questão utilizando os dados do CRM de aproximadamente 8.000 subscrições.  

* A taxa de misclassification SAS foi 10 a 15%.
* A taxa de misclassification do Machine Learning Studio foi 15 a 20% para os churners 200 300 primeiros.  

No setor telecomunicações, é importante apenas esses clientes que tenham o risco mais elevado para churn por oferta de um serviço concierge ou outro tratamento especial de endereços. No que que respeita a implementação de Machine Learning Studio distribui resultados com o modelo SAS.  

Mesmas token, exatidão é mais importante à precisão porque, principalmente se estiver interessados em corretamente classificar churners potenciais.  

O diagrama seguinte do Wikipedia ilustra a relação de um gráfico lively, fácil de compreender:  

![][8]

*Figura 10: Compromisso entre precisão e a precisão*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultados de precisão e a precisão do modelo de árvore de decisões elevada
O gráfico seguinte apresenta os resultados em bruto de classificação utilizando o protótipo de Machine Learning para o modelo de árvore de decisões elevada que acontece ser mais exatos entre os modelos de quatro:  

![][9]

*Figura 11: Características de modelo de árvore de decisões elevada*

## <a name="performance-comparison"></a>Comparação de desempenho
Iremos comparar a velocidade a que dados foi classificados utilizando os modelos de Machine Learning Studio e um modelo comparável criado utilizando a edição de ambiente de trabalho do SAS Enterprise Miner 12.1.  

A tabela seguinte resume o desempenho dos algoritmos de:  

*Tabela 1. Desempenho geral (precisão) dos algoritmos de*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Modelo de média |O modelo melhores |Underperforming |Modelo de média |

Os modelos alojados no Machine Learning Studio outperformed SAS por 15 25% para a velocidade de execução, mas precisão foi grande parte na par.  

## <a name="discussion-and-recommendations"></a>Debate e recomendações
No setor telecomunicações, várias práticas tem emerged para analisar o volume de alterações, incluindo:  

* Deriva as métricas das quatro categorias fundamentais:
  * **Entidade (por exemplo, uma subscrição)**. Aprovisionar informações básicas sobre a subscrição e/ou o cliente que é o assunto do volume de alterações.
  * **Atividade**. Obter todas as informações de utilização possíveis que está relacionado com a entidade, por exemplo, o número de inícios de sessão.
  * **O suporte ao cliente**. Recolher informações a partir de registos de suporte de cliente para indicar se a subscrição tinha problemas ou interações com o suporte ao cliente.
  * **Os dados empresariais e competitivos**. Obter quaisquer informações possíveis sobre o cliente (por exemplo, pode estar indisponível ou disco rígido controlar).
* Utilize a importância a seleção de funcionalidades de unidade. Isto implica que o modelo de árvore de decisões elevada sempre é uma abordagem promising.  

A utilização destas categorias quatro cria a ilusão simples *determinista* abordagem, com base em índices formados nos fatores razoáveis por categoria, deve suffice para identificar clientes em risco para o volume de alterações. Infelizmente, embora este noção parece plausible, é uma compreensão falsa. O motivo é que o volume de alterações é um efeito temporal e os fatores que contribuem para churn são normalmente nos Estados transitórios. O que servem como um cliente a ter em consideração que sai hoje poderão ser diferente amanhã e certamente será diferente seis meses a partir de agora. Por conseguinte, um *probabilistic* modelo é uma necessidade.  

Este observação importante é muitas vezes ignorada na empresa, que geralmente prefers uma abordagem de business intelligence e orientado para a análise, sobretudo porque é um mais fácil propor e admits automatização simples.  

No entanto, a promessa de análise de self-service utilizando o Machine Learning Studio é que as quatro categorias de informações, graded por divisão ou departamento, uma origem para o machine learning sobre o volume de alterações importante.  

Outra funcionalidade e extraordinária futuras no Azure Machine Learning é a capacidade de adicionar um módulo personalizado para o repositório de módulos predefinidos que já estão disponíveis. Esta capacidade, essencialmente, cria uma oportunidade para selecionar bibliotecas e criar modelos para mercados verticais. É um differentiator importante do Azure Machine Learning no mercado local.  

Esperamos continue neste tópico no futuro, especialmente relacionadas com a análise de macrodados.
  

## <a name="conclusion"></a>Conclusão
Este documento descreve uma abordagem sensible para tackling o problema comum de fluxo de cliente através da utilização de uma estrutura genérica. É considerada um protótipo para modelos de classificação e implementado-lo utilizando o Azure Machine Learning. Por fim, vamos avaliado em matéria de precisão e o desempenho da solução protótipo em relação a algoritmos comparáveis em SAS.  

**Para obter mais informações:**  

Neste documento ajudá-lo? Dê-nos seus comentários. Diga-numa escala de 1 (fraco) para 5 (excelente), como classifica neste documento e por que motivo atribuiu-esta classificação? Por exemplo:  

* Está a classificação-elevada devido a ter bons exemplos, capturas de ecrã excelente, desmarque a escrever ou outro motivo?
* Está a classificação, baixa devido a fracas exemplos, capturas de ecrã difusa ou escrever claro?  

Estes comentários ajudam-na melhorar a qualidade dos técnicos que Lançamos.   

[Enviar comentários](mailto:sqlfback@microsoft.com).
 

## <a name="references"></a>Referências
[1] Análise Preditiva: para além da gestão de informações Predições, da McKnight, Julho/Agosto de 2011, p.18-20.  

[2] Wikipedia artigo: [precisão e a precisão](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Guia de extração de dados passo a passo](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing de macrodados: interagir com os seus clientes de forma mais eficaz e valor de unidade](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco churn modelo](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) no [galeria da Cortana Intelligence](http://gallery.cortanaintelligence.com/) 
 

## <a name="appendix"></a>Apêndice
![][10]

*Figura 12: Instantâneo de uma apresentação no protótipo de volume de alterações*

[1]: ./media/azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/azure-ml-customer-churn-scenario/churn-10.png
