<properties
    pageTitle="O que é o Machine Learning do Azure? | Microsoft Azure"
    description="Explica os conceitos básicos do serviço do Machine Learning totalmente gerido, uma tecnologia de nuvem que pode utilizar para criar, operacionalizar e monetizar soluções."
    keywords="what is machine learning,cloud technology,predictive,what is predictive analytics,operationalize"
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/28/2016"
    ms.author="cgronlun;tedway;olgali"/>


# Introdução ao Machine Learning no Microsoft Azure

## O que é o Machine Learning?

O Machine Learning utiliza computadores para executar modelos preditivos a partir dos dados existentes para prever futuros comportamentos, resultados e tendências.

Estes previsões ou predições do Machine Learning podem tornar as aplicações e dispositivos mais inteligentes. Quando faz compras online, o Machine Learning recomenda outros produtos que poderá gostar com base nos que já comprou. Quando o seu cartão de crédito é usado, o Machine Learning compara a transação com uma base de dados de transações e ajuda o banco na deteção de fraudes.

## O que é o Machine Learning no Microsoft Azure?

O Azure Machine Learning é um poderoso serviço de análise preditiva baseado na nuvem e que torna possível a criação e implementação rápidas dos modelos preditivos como soluções de análise.

O Azure Machine Learning não fornece apenas ferramentas para a análise preditiva dos modelos, mas fornece também um serviço totalmente gerido que pode utilizar para implementar os seus modelos preditivos como serviços web prontos a consumir. O Azure Machine Learning fornece ferramentas para criar soluções completas de análise preditiva na nuvem: criar, testar, operacionalizar e gerir modelos preditivos de forma rápida. Não é necessário comprar qualquer hardware nem gerir manualmente as máquinas virtuais.

![O que é o Machine Learning? Fluxo de trabalho básico para operacionalizar a análise preditiva no Azure Machine Learning.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## O que é a análise preditiva?

A análise preditiva utiliza várias técnicas estatísticas - neste caso, o Machine Learning - para analisar os dados recolhidos ou atuais para padrões ou tendências com o objetivo de prever eventos futuros.

O Azure Machine Learning é um modo particularmente poderoso de realizar a análise preditiva: pode trabalhar a partir de uma biblioteca de algoritmos pronta a utilizar, criar modelos num PC ligado à internet sem ter de comprar equipamento ou infraestruturas adicionais e implementar rapidamente a sua solução preditiva. Também pode encontrar exemplos e soluções prontos a utilizar no [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) ou na [Galeria da Cortana Intelligence](http://gallery.cortanaintelligence.com/).

## Criar soluções completas do Machine Learning na nuvem

O Azure Machine Learning tem tudo o que precisa para criar soluções de análise preditiva na nuvem desde uma grande biblioteca de algoritmos até um studio para criação de modelos e uma forma fácil de implementar o seu modelo como um serviço web.

### Machine Learning Studio: Criar modelos preditivos

Crie os modelos preditivos no [Machine Learning Studio](machine-learning-what-is-ml-studio.md), uma ferramenta baseada num browser, ao arrastar, largar e conectar os módulos.

![O que é a análise preditiva: exemplo de uma experimentação da análise preditiva no Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

* Utilize uma biblioteca de grandes dimensões de [algoritmos e módulos do Machine Learning](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) no Machine Learning Studio para iniciar os seus modelos preditivos. Escolha a partir de uma biblioteca de experimentações de exemplo, pacotes R e Python e os melhores algoritmos de empresas da Microsoft como a Xbox e o Bing. Expandir módulos do Studio com os seus próprios scripts de [R](machine-learning-r-quickstart.md) e [Python](machine-learning-execute-python-scripts.md) personalizados.
* Na [Galeria da Cortana Intelligence](machine-learning-gallery-how-to-use-contribute-publish.md), pode experimentar as soluções de análise criadas por outras pessoas ou contribuir para as suas próprias utilizando serviços do Azure, incluindo Machine Learning, HDInsight (Hadoop), Stream Analytics e Data Lake Analytics, bem como serviços de gestão de dados e armazenamento de grandes volumes de dados do Azure.  Publicar questões ou comentários sobre experimentações para a comunidade ou partilhar ligações para experimentações através de redes sociais como o LinkedIn e o Twitter.  

    ![Tente experimentações preditivas ou contribua na Galeria da Cortana Intelligence do Azure](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)

### Operacionalize soluções de análise preditiva: comprar serviços web ou fazer as suas próprias publicações

* Comprar serviços web prontos a consumir do [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning), tais como recomendações, análises de texto e deteção de anomalias.

* Operacionalize seus modelos de análise preditiva:
    * [Implementar os serviços web](machine-learning-publish-a-machine-learning-web-service.md)
    * [Dar formação e voltar a repeti-la através das APIs](machine-learning-retrain-models-programmatically.md)
    * [Gerir pontos finais do serviço web](machine-learning-create-endpoint.md)
    * [Escalar os serviços web](machine-learning-scaling-endpoints.md)
    * [Consumir os serviços web](machine-learning-consume-web-services.md)

## Principal terminologia e conceitos do Machine Learning
### Exploração de dados, análise descritiva e análise preditiva

**Exploração de dados** é o processo de recolha de informações sobre um conjunto de dados de grande volume e, muitas vezes, não estruturado para encontrar características para uma análise focada. **Extração de dados** refere-se a exploração de dados automática.

**Análise descritiva** é o processo de analisar um conjunto de dados para resumir o que aconteceu. A maioria das análises do negócio - como os relatórios de vendas, métricas da web e análise de redes sociais – são descritivas.

**Análise preditiva** é o processo de criação de modelos a partir dos dados históricos ou atuais para prever futuros resultados.


### Aprendizagem supervisionada e não supervisionada
 **Aprendizagem supervisionada** os algoritmos são preparados com dados com nome - por outras palavras, dados compostos por exemplos de respostas pretendidas. Por exemplo, um modelo que identifica a utilização fraudulenta de cartão de crédito seria preparado a partir de um conjunto de dados em que os pontos de dados identificam encargos fraudulentos e válidos. A maior parte do Machine Learning é supervisionada.

 **Learning não supervisionado** é utilizado em dados sem identificação e o objetivo é encontrar relações entre os dados. Por exemplo, pode querer localizar agrupamentos de dados demográficos do cliente com hábitos de aquisição semelhantes.

### Modelo de formação e avaliação
Um modelo do Machine Learning é uma abstração da pergunta a que está a tentar responder ou o resultado que pretende prever. Os modelos são ensinados e avaliados a partir dos dados existentes.

#### Formação a partir dos dados
No Azure Machine Learning, é criado um modelo a partir de um módulo de algoritmo que processa os dados de formação e os módulos funcionais, por exemplo, um módulo de pontuação.

No Learning supervisionado, se estiver a dar formação sobre um modelo de deteção de fraudes, irá utilizar um conjunto de transações que estão identificadas como fraudulentas ou válidas. Irá dividir aleatoriamente o seu conjunto de dados e utilizar parte do mesmo para preparar o modelo e outra parte para testar ou avaliar o modelo.

#### Dados de avaliação
Depois de ter um modelo de preparação, avalie o modelo utilizando os restantes dados de teste. Utilize dados dos quais já conhece os resultados para saber se o seu modelo prevê com precisão.

### Outros termos comuns do Machine Learning

* **algoritmo**: Um conjunto de regras autónomo para resolver problemas através do processamento de dados, cálculo ou raciocínio automatizado.
* **dados categóricos**: os dados que estão organizado por categorias e que podem ser divididos em grupos. Por exemplo, um conjunto de dados categórico para automóveis pode especificar o ano, a marca, o modelo e o preço.
* **classificação**: um modelo para organizar os pontos de dados em categorias com base num conjunto de dados para os agrupamentos da categoria que são já conhecidos.
* **engenharia da funcionalidade**: o processo de extração ou seleção das funcionalidades relacionadas com um conjunto de dados para melhorar o conjunto de dados e melhorar os resultados. Por exemplo, podem ser melhorados os dados das tarifas aéreas por dias da semana e feriados. Consulte o artigo [Seleção da funcionalidade e engenharia no Azure Machine Learning](machine-learning-feature-selection-and-engineering.md).
* **módulo**: Um elemento funcional no modelo Machine Learning Studio, tal como o módulo de introdução de dados que lhe permite introduzir e editar pequenos conjuntos de dados. Um algoritmo também é um tipo de módulo no Machine Learning Studio.
* **modelo**: Para um Learning supervisionado, um modelo é o produto de uma experimentação do Machine Learning composto por um conjunto de dados de formação, um módulo de algoritmo e módulos funcionais, por exemplo, um módulo do modelo de pontuação.
* **dados numéricos**: Dados que representam medidas (dados contínuos) ou contagens (dados discretos). Também referidos como *dados quantitativos*.
* **partição**: O método através do qual divide os dados em amostras. Consulte o artigo [Partição e amostras](https://msdn.microsoft.com/library/azure/dn905960.aspx) para obter mais informações.
* **predição**: Uma predição é uma previsão de um valor ou valores de um modelo do Machine Learning. Também poderá ver o termo "pontuação prevista"; no entanto, as pontuações previstas não são o resultado final de um modelo. Uma avaliação do modelo está de acordo com a pontuação.
* **regressão**: Um modelo para prever valor contínuo com base nas variáveis independentes, por exemplo, prever num período o preço de um carro com base no respetivo ano e marca.
* **pontuação**: Um valor previsto gerado a partir de uma classificação de preparação ou modelo de regressão, utilizando o [módulo do modelo de pontuação](https://msdn.microsoft.com/library/azure/dn905995.aspx) no Machine Learning Studio. Os modelos de classificação também devolvem uma pontuação para indicar a probabilidade do valor previsto. Assim que tiver gerado pontuações de um modelo, pode avaliar exatidão o modelo utilizando o [módulo do modelo de avaliação](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **exemplo**: Uma parte de um conjunto de dados que é representativo da totalidade. Exemplos podem ser selecionados aleatoriamente ou com base nas funcionalidades específicas do conjunto de dados.



## Passos seguintes
Pode saber as noções básicas de análise preditiva e do Machine Learning utilizando um [tutorial passo a passo](machine-learning-create-experiment.md) e [tendo amostras](machine-learning-sample-experiments.md).  


<!-- Module References -->
[learning com contagens]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/



<!--HONumber=Jun16_HO2-->


