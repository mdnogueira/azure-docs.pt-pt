---
title: "Análise de dados de sentimento com profunda Learning com o Azure Machine Learning | Microsoft Docs"
description: "Como efetuar uma análise de dados de sentimento com profunda learning do Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: miprasad
ms.openlocfilehash: 39ae2aa7217b45e8fab77f528b27b77a1b1256bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Análise de dados de sentimento com profunda Learning com o Azure Machine Learning

Análise de dados de sentimento é uma tarefa bem conhecida no realm de processamento de linguagem natural. Num determinado conjunto de textos, o objetivo consiste em determinar o sentimento do que texto. O objetivo desta solução é utilizar CNTK como o back-end para Keras (uma modelo nível biblioteca, fornecendo alto nível blocos modulares para desenvolver modelos learning profunda) e implementar a análise de dados de sentimento do filme revisões.

A solução está localizada em https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Ligação para o repositório do GitHub da Galeria

Siga esta ligação para o repositório do GitHub público:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Descrição geral de cenário de utilização

Explosão de dados e a proliferação de dispositivos móveis foi criada com muitas oportunidades para os clientes expressar as respetivas feelings e attitudes sobre todos e quaisquer dados em qualquer altura. Este opinião ou "sentimento", muitas vezes, é gerado através de redes sociais canais sob a forma de revisões, chats, partilha, likes, tweets, etc. O sentimento pode ser valiosas para as empresas que querem melhorar os produtos e serviços, tomar decisões mais informadas e melhor promove as marcas.

Para obter o valor da análise de dados de sentimento, as empresas tem de ter a capacidade de mine vasta arquivos de dados não estruturados de redes sociais para conhecimentos acionáveis. Neste exemplo, iremos irá desenvolver modelos learning avançada para efetuar análise de dados de sentimento do revisões de filmes com AMLWorkbench

## <a name="prerequisites"></a>Pré-requisitos

* Um [conta do Azure](https://azure.microsoft.com/free/) (gratuitas estão disponíveis).

* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguintes o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar uma área de trabalho.

* Para operationalization, é melhor se tiver o motor de Docker instalado e em execução localmente. Caso contrário, pode utilizar a opção de cluster. No entanto, a executar um serviço de contentor do Azure (ACS) pode ser dispendiosa.

* Esta solução assume que está a executar Workbench do Azure Machine Learning no Windows 10 com o motor de Docker instalada localmente. Num macOS as instruções em grande parte é o mesmo.

## <a name="data-description"></a>Descrição de dados

O conjunto de dados utilizado para este exemplo é um pequeno conjunto de dados mão-crafted e está localizado no [pasta dados](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

A primeira coluna contenha filmes revisões e a segunda coluna contém os dados de sentimento (0 - negativo e 1 - positivos). O conjunto de dados é utilizado apenas para fins de demonstração, mas normalmente para obter pontuações de sentimento robusta, precisa de um grande conjunto de dados. Por exemplo, o [IMDB filmes revê o problema de classificação de dados de sentimento](https://keras.io/datasets/#datasets ) de Keras é composta por um conjunto de dados de 25.000 filmes revisões de IMDB, etiquetados pelo sentimento (positivo ou negativo). A intenção este laboratório é para lhe mostrar como efetuar uma análise de dados de sentimento com learning profunda AMLWorkbench.

## <a name="scenario-structure"></a>Estrutura do cenário

A estrutura de pastas é disposta da seguinte forma:

1. Todos os o código relacionado para análise de dados de sentimento com AMLWorkbench é na pasta raiz
2. dados: contém o conjunto de dados utilizado na solução
3. documentos: contém todos os laboratórios prática

A ordem dos Hands-on laboratórios para realizar a solução é o seguinte:

| Ordem| Nome de ficheiro | Ficheiros relacionados |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKerasWithCNTKBackend.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKerasWithCNTKBackend.md) | 'SentimentExtraction.py' |
| 3 | [`SentimentAnalysisModelingDocker.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingDocker.md) | 'SentimentExtractionDocker.py' |
| 4 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | 'Operaionalization' |

## <a name="conclusion"></a>Conclusão

No conclusion, esta solução apresenta-lhe utilizar Learning avançada para executar uma análise de dados de sentimento com o Workbench do Azure Machine Learning. A solução apresentada é flexível para utilizar CNTK/Tensorflow como o back-end com Keras. Além disso, estamos também operacionalizar modelos HDF5 a utilizar.
