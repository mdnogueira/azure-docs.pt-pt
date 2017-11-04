---
title: "Criar modelos de análise de texto no Azure Machine Learning Studio | Microsoft Docs"
description: "Como criar modelos de análise de texto no Azure Machine Learning Studio com módulos de texto pré-processamentos, N-grams ou funcionalidade hash"
services: machine-learning
documentationcenter: 
author: rastala
manager: jhubbard
editor: 
ms.assetid: 08cd6723-3ae6-4e99-a924-e650942e461b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: roastala
ms.openlocfilehash: 5f45a500c7cc28b6d6ad3fcac780791d43067ff3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Criar modelos de análise de texto no Azure Machine Learning Studio
Pode utilizar o Azure Machine Learning para criar e operacionalizar modelos de análise de texto. Estes modelos podem ajudar a resolver, por exemplo, problemas de análise de classificação de recursos de dados de sentimento do documento.

Numa experimentação da análise de texto, normalmente, seria:

1. Limpar e processar previamente os do conjunto de dados de texto
2. Extrair vetores de funcionalidade numérico a partir de texto de pré-processado
3. Preparar modelo de classificação ou regressão
4. Pontuar e valide o modelo
5. Implementar o modelo para produção

Neste tutorial, aprende estes passos, iremos guiá-lo através de um modelo de análise de dados de sentimento utilizando o conjunto de dados do Amazon livro revisões (consulte neste documento research "Biographies, Bollywood, caixas de Boom e Blenders: Adaptation de domínio para classificação de dados de sentimento" pelo João Blitzer, Marcar Dredze e Fernando Pereira; Associação de Linguistics computacional (ACL), 2007.) Este conjunto de dados é constituído por pontuações de revisão (1-2 ou 4 a 5) e um texto livre. O objetivo é para prever a classificação de revisão: baixa (1 - 2) ou elevado (4-5).

Pode encontrar experimentações abrangidas neste tutorial na galeria da Cortana Intelligence:

[Prever revisões de livro](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Prever livro revisões - experimentação preditiva](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Passo 1: Limpar e processar previamente os do conjunto de dados de texto
Vamos começar a experimentação dividindo as pontuações de revisão para registos baixos e alto categórico para formular o problema como a classificação de duas classes. Utilizamos [Editar metadados](https://msdn.microsoft.com/library/azure/dn905986.aspx) e [valores Categórico grupo](https://msdn.microsoft.com/library/azure/dn906014.aspx) módulos.

![Criar etiqueta](./media/text-analytics-module-tutorial/create-label.png)

Em seguida, iremos limpar o texto a utilizar [processar previamente os texto](https://msdn.microsoft.com/library/azure/mt762915.aspx) módulo. A limpeza reduz o ruído no conjunto de dados, ajuda a localizar as funcionalidades mais importantes e melhorar a precisão do modelo final. Removemos palavras de paragem - palavras comuns, tais como "a" ou "a" - e números, carateres especiais, carateres de duplicados, endereços de correio eletrónico e URLs. Iremos também converter o texto para minúsculas, lemmatize as palavras e detetar limites de frases que, em seguida, são indicados por "|||" símbolo texto pré-processado.

![Processar previamente os texto](./media/text-analytics-module-tutorial/preprocess-text.png)

E se pretende utilizar uma lista personalizada de palavras de paragem? Pode passá-lo na como entrada opcional. Também pode utilizar c# sintaxe expressão regular personalizada para substituir subcadeias e remover palavras por parte de voz: substantivos, verbos ou adjectives.

Depois do pré-processamentos estiver concluída, iremos dividir os dados para formação e teste conjuntos.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Passo 2: Extrair vetores de funcionalidade numérico a partir de texto de pré-processado
Para criar um modelo de dados de texto, normalmente, tem de converter o texto de forma livre num vetores de funcionalidade numérico. Neste exemplo, utilizamos [extrair N grama as funcionalidades do texto](https://msdn.microsoft.com/library/azure/mt762916.aspx) módulo para transformar os dados de texto para esse formato. Este módulo executa uma coluna de valores separados por espaços em branco palavras e calcula um dicionário de palavras ou N-grams de palavras, o que aparecem no seu conjunto de dados. Em seguida, contagens quantas vezes cada word ou N-grama, é apresentado em cada registo e cria os vetores de funcionalidade das contagem. Neste tutorial, definimos grama N tamanho para 2, pelo que o nosso vetores de funcionalidade incluem palavras único e combinações de duas palavras subsequentes.

![Extrair N-grams](./media/text-analytics-module-tutorial/extract-ngrams.png)

Iremos aplicar TF * IDF (termo frequência inverso documento frequência) weighting para N grama contagens. Esta abordagem adiciona uma ponderação de palavras que aparecem frequentemente num único registo, mas são raro em todo o conjunto de dados. Outras opções incluem binary, TF e graph weighing.

Estas funcionalidades de texto, muitas vezes, têm dimensionalidades elevada. Por exemplo, se a sua corpus tiver 100 000 palavras exclusivas, o espaço de funcionalidade teria 100.000 dimensões ou mais se N-grams são utilizados. O módulo de funcionalidades de N grama extrair dá-lhe um conjunto de opções para reduzir dimensionalidade. Pode optar por excluir palavras que são curto ou longo ou demasiado invulgar ou demasiado frequente para ter um valor preditivo significativo. Neste tutorial, iremos excluir N-grams que são apresentados em menos de 5 registos ou em mais de 80% dos registos.

Além disso, pode utilizar a seleção de funcionalidades para selecionar apenas as funcionalidades que são mais correlacionado com o destino de predição. Utilizamos a seleção de funcionalidades qui quadrado para selecionar 1000 funcionalidades. Pode ver vocabulário de palavras selecionadas ou N-grams clicando a saída do módulo de extrair N-grams à direita.

Como uma abordagem alternativa à utilização de funcionalidades de N grama extrair, pode utilizar o módulo de funcionalidade hash. Tenha em atenção que que [funcionalidade hash](https://msdn.microsoft.com/library/azure/dn906018.aspx) não possui capacidades de seleção de funcionalidade em compilação ou TF * IDF weighing.

## <a name="step-3-train-classification-or-regression-model"></a>Passo 3: Preparar o modelo de classificação ou regressão
Agora, o texto ter sido transformado para colunas numéricas funcionalidade. O conjunto de dados contém ainda colunas de cadeia de fases anteriores, pelo que iremos utilizar selecionar colunas no conjunto de dados para os excluir.

Em seguida, utilizamos [duas classes de regressão logística da](https://msdn.microsoft.com/library/azure/dn905994.aspx) para prever a nossa destino: pontuação de revisão elevada ou baixa. Neste momento, o problema de análise de texto ter sido transformado um problema de classificação regular. Pode utilizar as ferramentas disponíveis no Azure Machine Learning para melhorar o modelo. Por exemplo, pode experimentar diferentes classificadores para saber como exatos resultados fornecem ou utilizar hyperparameter otimização para melhorar a precisão.

![Formação e Pontuar](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Passo 4: Pontuar e valide o modelo
Como é validar o modelo treinado? Iremos pontuá-lo contra o conjunto de dados de teste e avaliar a precisão. No entanto, o modelo aprendidas vocabulário de N-grams e as respetivas importâncias do conjunto de dados de formação. Por conseguinte, que devemos utilizar essa vocabulário e uma dessas ponderações quando extrair as funcionalidades de dados de teste, por oposição a criação de vocabulário anew. Por conseguinte, vamos adicionar o módulo de funcionalidades de N grama extrair para o ramo classificação da experimentação, ligar o vocabulário de saída do ramo de preparação e definir o modo de vocabulário como só de leitura. Podemos também desativar a filtragem de N-grams pela frequência, definindo o mínimo de 1 instância e o máximo para 100% e desativar a seleção de funcionalidade.

Depois de ter sido transformada a coluna de texto em dados de teste para colunas numéricas funcionalidade, iremos excluir as colunas de cadeia de fases anteriores, como no ramo de preparação. Podemos utilizar módulo de modelo de pontuação para tornar as predições e módulo de modelo de avaliação para avaliar a precisão.

## <a name="step-5-deploy-the-model-to-production"></a>Passo 5: Implementar o modelo para produção
O modelo está quase pronto para ser implementado para produção. Quando implementado como um serviço web, que demora a cadeia de texto de forma livre como entrada e devolver uma predição "Alta" ou "baixa". Utiliza o vocabulário N grama adquirido para transformar o texto para funcionalidades e o modelo de regressão logística da preparado para efetuar uma predição dessas funcionalidades. 

Para configurar a experimentação preditiva, primeiro guardamos o vocabulário N grama como conjunto de dados e o modelo de regressão logística da treinado do ramo de preparação da experimentação. Em seguida, iremos guardar a experimentação utilizando "Guardar como" para criar um gráfico de experimentação para experimentação preditiva. Remover o módulo de dividir dados e o ramo de preparação a experimentação. Mas, em seguida, ligar a N grama vocabulário guardado anteriormente e o modelo para extrair as funcionalidades de N grama e os módulos do modelo de pontuação, respetivamente. Removemos também o módulo de modelo de avaliação.

Iremos selecionar colunas de inserção de módulo de conjunto de dados antes do módulo de processar previamente os texto para remover a coluna de etiqueta e anule a seleção de opção "Coluna de modelo de pontuação acrescentar ao conjunto de dados" no módulo de pontuação. Dessa forma, o serviço web não pedir a etiqueta que está a tentar prever e não não eco a entrada funcionalidades na resposta.

![Experimentação preditiva](./media/text-analytics-module-tutorial/predictive-text.png)

Agora que temos uma experimentação que pode ser publicada como um serviço web e chamada utilizando a execução do pedido-resposta ou lote APIs.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o módulos de análise de texto de [documentação MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

