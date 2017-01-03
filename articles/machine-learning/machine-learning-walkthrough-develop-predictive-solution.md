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
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Instruções: desenvolver uma solução de análise preditiva para a avaliação de riscos de crédito no Azure Machine Learning

Nestas instruções, faremos uma observação expandida ao processo de desenvolvimento de uma solução no Machine Learning Studio. Desenvolveremos um modelo de análise preditiva no Machine Learning Studio e, em seguida, implementá-lo como um serviço Web do Azure Machine Learning onde o modelo pode efetuar predições utilizando novos dados. 

> [!TIP]
> Estas instruções assumem que já tenha utilizado Machine Learning Studio pelo menos uma vez antes e que tem alguma compreensão dos conceitos de machine learning, embora assuma que não seja um perito.
> 
>Se nunca tiver utilizado o **Azure Machine Learning Studio** antes, poderá querer começar com o tutorial, [Create your first data science experiment in Azure Machine Learning Studio (Criar a primeira experimentação de ciência de dados no Azure Machine Learning Studio)](machine-learning-create-experiment.md). Tal tutorial guia-o ao longo do Machine Learning Studio pela primeira vez, mostrando-lhe as noções básicas, como arrastar e largar módulos na experimentação, ligá-los entre si, executar a experimentação e analisar os resultados.
>
>Se não estiver familiarizado com machine learning, a série de vídeos [Data Science for Beginners (Ciência de Dados para Principiantes)](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) pode ser um ponto de partida. Esta série de vídeos é uma excelente introdução ao machine learning, que utiliza uma linguagem e conceitos do dia a dia.
> 

## <a name="the-problem"></a>O problema

Suponhamos que tem de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação de riscos de crédito é um problema complexo, obviamente, mas vamos simplificar um pouco os parâmetros da pergunta. Vamos utilizá-la como um exemplo de como pode utilizar o Microsoft Azure Machine Learning com o Machine Learning Studio e o serviço web do Machine Learning para criar uma solução de análise preditiva.  

## <a name="the-solution"></a>A solução

Nesta introdução detalhada, iremos começar com dados de risco de crédito publicamente disponíveis, desenvolver e formar um modelo preditivo com base nesses dados e, em seguida, implementar o modelo como um serviço web que pode ser utilizado por terceiros para a avaliação de riscos de crédito.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para criar uma solução de avaliação do risco de crédito, teremos de executar os seguintes passos:  

1. [Criar uma área de trabalho do Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Carregar os dados existentes](machine-learning-walkthrough-2-upload-data.md)
3. [Criar uma nova experimentação](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Dar formação e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Implementar o serviço web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Aceder ao serviço web](machine-learning-walkthrough-6-access-web-service.md)

Estas instruções baseiam-se uma versão simplificada da [Classificação do binário: previsão do risco de crédito](http://go.microsoft.com/fwlink/?LinkID=525270) experimentação de exemplo na [Galeria da Cortana Intelligence](http://gallery.cortanaintelligence.com/).


> [!TIP]
> Para transferir e imprimir um diagrama da descrição geral das funcionalidades do Machine Learning Studio, consulte o artigo [Diagrama da descrição geral das funcionalidades do Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md).
> 
> 



<!--HONumber=Dec16_HO5-->


