---
title: "Utilizar regressão Linear no Machine Learning | Microsoft Docs"
description: "Uma comparação dos modelos de regressão linear no Excel e no Azure Machine Learning Studio"
metakeywords: 
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 417ae6ab-de4f-4bdd-957a-d96133234656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: kbaroni;garye
ms.openlocfilehash: 218f2b141e3551180a2152570f99fdb427980dd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-linear-regression-in-azure-machine-learning"></a>Utilizar a regressão linear no Azure Machine Learning
> *Kate Baroni* e *Bernardo Boatman* é enterprise os arquitetos de soluções dados Insights Centro de Excellence da Microsoft. Neste artigo, descrevem sua experiência de um conjunto de análise de regressão existente a migrar para uma solução baseada na nuvem utilizando o Azure Machine Learning. 
> 
> 

&nbsp; 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="goal"></a>Objetivo
Nosso projeto começar a utilizar dois objetivos em mente: 

1. Utilize a Análise Preditiva para melhorar a precisão dos projeções de receitas mensal nossa organização 
2. Utilizar o Azure Machine Learning para confirmar, otimizar, aumentar a velocidade e a escala da nossa resultados. 

Como muitas empresas, nossa organização passa através de uma processo de previsão de receitas mensal. A nossa equipa pequena dos analistas de negócios foi é incumbida a utilizar o Azure Machine Learning para suportar o processo e melhorar a exatidão da previsão. A equipa despendido vários meses, a recolha de dados de várias origens e em execução os atributos de dados através de análises que identifica os atributos de chave relevantes previsões de vendas de serviços. O passo seguinte foi para começar a modelos de regressão análises de fazer o protótipo nos dados no Excel. Dentro de algumas semanas, iremos tinha um modelo de regressão do Excel que foi outperforming o campo atual e o departamento financeiro processos de previsão. Isto ficou o resultado de predição de linha de base. 

Pegámos, em seguida, o passo seguinte para mover o nosso Análise Preditiva ao longo do Azure Machine Learning para saber como pode melhorar o Machine Learning no desempenho preditivo.

## <a name="achieving-predictive-performance-parity"></a>Alcançar paridade de desempenho preditiva
A nossa prioridade primeiro era alcançar paridade entre os modelos de regressão de Machine Learning e o Excel. Tendo em conta os mesmos dados e a divisão mesma para formação e testar dados, queremos alcançar paridade de desempenho preditiva entre o Excel e Machine Learning. Inicialmente falha. O modelo de Excel outperformed o modelo de Machine Learning. A falha se deveu a falta de compreensão da definição de base ferramenta no Machine Learning. Depois de uma sincronização com a equipa de produto do Machine Learning, vamos este adquiriu uma melhor compreensão sobre a definição necessária para a nossa conjuntos de dados de base e conseguido paridade entre dois modelos. 

### <a name="create-regression-model-in-excel"></a>Criar modelo de regressão no Excel
A nossa regressão Excel utilizado o modelo de regressão linear padrão encontrado no ToolPak de Analysis Services Excel. 

Iremos calculado *% absoluto média erro* e utilizar, como a medida de desempenho para o modelo. Demorou 3 meses para chegam a um modelo de trabalhar com o Excel. É colocada muito learning para a experimentação do Machine Learning Studio que, fundamentalmente, foi benéfico nos requisitos de compreender.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Criar comparável experimentação no Azure Machine Learning
Iremos seguido estes passos para criar o nosso experimentação no Machine Learning Studio: 

1. Carregou o conjunto de dados como um ficheiro csv para o Machine Learning Studio (muito pequeno ficheiro)
2. Criar uma nova experimentação e utilizado o [selecionar colunas no conjunto de dados] [ select-columns] módulo para selecionar as funcionalidades de dados mesmo utilizadas no Excel 
3. Utilizar o [dividir dados] [ split] módulo (com *expressão relativo* modo) para dividir os dados em conjuntos de dados de formação mesmo como tinha sido feito no Excel 
4. Experimented com o [regressão Linear] [ linear-regression] módulo (apenas opções predefinidas), documentados e, em comparação com os resultados para o nosso modelo de regressão do Excel

### <a name="review-initial-results"></a>Reveja os resultados iniciais
Em primeiro lugar, o modelo de Excel outperformed claramente o modelo de Machine Learning Studio: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Desempenho | | |
| <ul style="list-style-type: none;"><li>Tendo em conta os parênteses de R</li></ul> |0.96 |N/D |
| <ul style="list-style-type: none;"><li>Coeficiente de <br />Determinação</li></ul> |N/D |0.78<br />(precisão baixa) |
| Tem de estar verdadeiramente erros absolutos |$9. 5M |$ 19.4 M |
| Tem de estar verdadeiramente erros absolutos (%) |6.03% |12.2% |

Quando executamos nossos processo e resultados pela cientistas de dados e os programadores em equipa do Machine Learning, rapidamente fornecidas algumas dicas úteis. 

* Quando utiliza o [regressão Linear] [ linear-regression] módulo no Machine Learning Studio, dois métodos são fornecidos:
  * Online descendente de gradação: Pode ser mais adequada para problemas de grande escala
  * Comum de menos de quadrados: Este método é a que maioria das pessoas encaram quando estes ouvir regressão linear. Para conjuntos de dados pequenos comum menos quadrados pode ser uma escolha ideal mais.
* Considere ajuste o parâmetro L2 Regularization ponderação para melhorar o desempenho. Está definido para 0,001 por predefinição, mas o nosso pequeno conjunto de dados podemos configurá-lo para 0.005 para melhorar o desempenho. 

### <a name="mystery-solved"></a>Mystery resolvido!
Quando aplicada, as recomendações, foi alcançado o mesmo desempenho de linha de base no Machine Learning Studio como com o Excel: 

|  | Excel | Studio (inicial) | Studio com menos quadrados |
| --- |:---:|:---:|:---:|
| Valor identificado |Actuals (numérico) |mesmo |mesmo |
| Learner |Excel -> dados Analysis -> regressão |Regressão linear. |Regressão linear |
| Opções de learner |N/D |Predefinições |quadrados de menos comum<br />L2 = 0.005 |
| Conjunto de dados |26 linhas, 3 funcionalidades, 1 etiqueta. Todos os numérica. |mesmo |mesmo |
| Divisão: formação |Excel preparado no primeiro 18 linhas, testadas nas últimas 8 linhas. |mesmo |mesmo |
| Divisão: teste |Fórmula de regressão de Excel aplicada às linhas pela última vez 8 |mesmo |mesmo |
| **Desempenho** | | | |
| Tendo em conta os parênteses de R |0.96 |N/D | |
| Coeficiente de determinação |N/D |0.78 |0.952049 |
| Tem de estar verdadeiramente erros absolutos |$9. 5M |$ 19.4 M |$9. 5M |
| Tem de estar verdadeiramente erros absolutos (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Além disso, os coefficients Excel em comparação com também as ponderações de funcionalidade no modelo treinado do Azure:

|  | Excel Coefficients | Peso da funcionalidade do Azure |
| --- |:---:|:---:|
| Intercept/Bias |19470209.88 |19328500 |
| Funcionalidade A |0.832653063 |0.834156 |
| Funcionalidade B |11071967.08 |11007300 |
| Funcionalidade C |25383318.09 |25140800 |

## <a name="next-steps"></a>Passos Seguintes
Queremos consumir o serviço web do Machine Learning no Excel. A nossa os analistas de negócios baseiam-se no Excel e é necessária uma forma de chamar o serviço web do Machine Learning com uma linha de dados do Excel e o tiver devolver o valor previsto para o Excel. 

Também queremos otimizar o nosso modelo, utilizando as opções e algoritmos disponíveis no Machine Learning Studio.

### <a name="integration-with-excel"></a>Integração com o Excel
A nossa solução era operacionalizar o nosso modelo de regressão de Machine Learning através da criação de um serviço web do modelo treinado. Dentro de alguns minutos, o serviço web foi criado e foi a que chamamos diretamente a partir do Excel para devolver um valor previsto receitas. 

O *Dashboard de serviços Web* secção inclui um livro do Excel transferível. O livro vem previamente formatado com as web service API informações de esquema e incorporadas. Ao clicar em *Transferir livro do Excel*, abre o livro e pode guardá-lo para o seu computador local. 

![][1]

Com o livro aberto, copie os parâmetros predefinidos para a secção de parâmetro azul, conforme mostrado abaixo. Depois dos parâmetros são introduzidos, Excel chama a atenção ao serviço web do Machine Learning e irão apresentar as etiquetas classificadas previstas na secção valores previstos verde. O livro irá continuar a criar predições para os parâmetros com base no seu modelo treinado para todos os itens de linha introduzidos em parâmetros. Para obter mais informações sobre como utilizar esta funcionalidade, consulte [consumir um serviço de Web do Azure Machine Learning a partir do Excel](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Otimização e mais experimentações
Agora que Tivemos uma linha de base com o nosso modelo do Excel, podemos avançar movido para otimizar o nosso modelo de regressão Linear do Machine Learning. Utilizámos o módulo [filtro com base na seleção de funcionalidades] [ filter-based-feature-selection] para melhorar o nosso seleção de dados iniciais de elementos e ajudou-na alcançar uma melhoria do desempenho de 4.6% significa erros absolutos. Para projetos futuros utilizaremos esta funcionalidade que foi possível guardar nos semanas no repetir atributos de dados para localizar o conjunto de funcionalidades a utilizar para modelling correto. 

Em seguida planeamos incluir algoritmos adicionais, como [Bayesiano] [ bayesian-linear-regression] ou [árvores de decisões elevada] [ boosted-decision-tree-regression] na nossa experiência para comparar o desempenho. 

Se pretender experimentar regressão, um bom conjunto de dados para experimentar é o regressão de eficiência de energia exemplo conjunto de dados, que tem muitos dos atributos numéricos. O conjunto de dados é fornecido como parte dos conjuntos de dados de exemplo no Machine Learning Studio. Pode utilizar uma variedade de módulos de aprendizagem para prever a carga Heating ou arrefecimento carga. A tabela abaixo é que uma comparação de desempenho de regressão diferentes aprende contra o conjunto de dados de eficiência energética prever para a variável de destino Cooling carga: 

| Modelo | Tem de estar verdadeiramente erros absolutos | Erro ao quadrado de média de raiz | Erro relativo absoluto | Erro ao quadrado de caminho relativo | Coeficiente de determinação |
| --- | --- | --- | --- | --- | --- |
| Árvore de decisões elevada |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Regressão linear (descendente gradação) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| A rede neuronal regressão |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Regressão linear (quadrados de menos comum) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Chaves Takeaways
Iremos aprendido muito por regressão de Excel em execução e experimentações do Azure Machine Learning em paralelo. Criar o modelo de linha de base no Excel e comparando-o a modelos de Machine Learning [regressão Linear] [ linear-regression] ajudou-nos obter o Azure Machine Learning e iremos detetados oportunidades para melhorar o desempenho de seleção e o modelo de dados. 

Encontrámos também que é recomendado utilizar [filtro com base na seleção de funcionalidades] [ filter-based-feature-selection] para acelerar a projetos de predição futuras. Ao aplicar a seleção de funcionalidades aos seus dados, pode criar um modelo melhorado no Machine Learning com um melhor desempenho global. 

A capacidade para transferir o preditiva analíticas previsão do Machine Learning para o Excel systemically permite um aumento significativo na capacidade de fornecer com êxito os resultados para um público-alvo de utilizador do negócio abrangente. 

## <a name="resources"></a>Recursos
Seguem-se alguns recursos para ajudar a trabalhar com regressão: 

* Regressão no Excel. Se nunca tiver tentou regressão no Excel, este tutorial torna mais fácil: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regressão vs de previsão. Tyler Chessman escrito um artigo de blogue explicar como do Excel, que contém a descrição de um beginner boa de regressão linear de previsão de série de tempo. [http://sqlmag.com/SQL-Server-Analysis-Services/Understanding-Time-Series-forecasting-Concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Regressão Linear comum menos quadrados: Falhas, problemas e Pitfalls. Para uma introdução e debate de regressão: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

