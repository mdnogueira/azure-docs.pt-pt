---
title: "Uma solução preditiva para risco de crédito com Machine Learning | Microsoft Docs"
description: "Instruções detalhadas sobre como criar uma solução de análise preditiva para a avaliação de riscos de crédito no Azure Machine Learning Studio."
keywords: "risco de crédito, solução de análise preditiva, avaliação de riscos"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aa64dc7f5bb3e928aac30987b0904435c603829c


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Instruções: desenvolver uma solução de análise preditiva para a avaliação de riscos de crédito no Azure Machine Learning
Suponhamos que tem de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação de riscos de crédito é um problema complexo, obviamente, mas vamos simplificar um pouco os parâmetros da pergunta. Podemos utilizá-la como um exemplo de como pode utilizar o Microsoft Azure Machine Learning com o Machine Learning Studio e o serviço web do Machine Learning para criar uma solução de análise preditiva.  

Nestas instruções detalhadas, iremos seguir o processo de desenvolvimento de um modelo de análise preditiva no Machine Learning Studio e, em seguida, implementá-lo como um serviço web do Azure Machine Learning. Iremos começar com dados de risco de crédito publicamente disponíveis, desenvolver e formar um modelo preditivo com base nesses dados e, em seguida, implementar o modelo como um serviço web que pode ser utilizado por terceiros para a avaliação de riscos de crédito.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

> [!TIP]
> Para transferir e imprimir um diagrama da descrição geral das funcionalidades do Machine Learning Studio, consulte o artigo [Diagrama da descrição geral das funcionalidades do Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md).
> 
> 

Para criar uma solução de avaliação do risco de crédito, teremos de executar os seguintes passos:  

1. [Criar uma área de trabalho do Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Carregar os dados existentes](machine-learning-walkthrough-2-upload-data.md)
3. [Criar uma nova experimentação](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Dar formação e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Implementar o serviço web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Aceder ao serviço web](machine-learning-walkthrough-6-access-web-service.md)

Estas instruções baseiam-se uma versão simplificada da [Classificação do binário: previsão do risco de crédito](http://go.microsoft.com/fwlink/?LinkID=525270) experimentação de exemplo na [Galeria da Cortana Intelligence](http://gallery.cortanaintelligence.com/).




<!--HONumber=Nov16_HO2-->


