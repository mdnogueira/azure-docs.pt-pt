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
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: fe504d826b6c40099f1f8706ef7e8780eed5cf9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Instruções: desenvolver uma solução de análise preditiva para a avaliação de riscos de crédito no Azure Machine Learning

Nestas instruções, analisamos mais aprofundadamente o processo de desenvolvimento de uma solução de análise preditiva no Machine Learning Studio. Desenvolvemos um modelo simples no Machine Learning Studio e, em seguida, implementamo-lo como um serviço Web Machine Learning onde o modelo pode realizar predições com dados novos. 

Estas instruções partem do princípio de que já utilizou o Machine Learning Studio pelo menos uma vez e que compreende, de alguma forma, os conceitos de machine learning. Porém, não parte do princípio de que é um especialista.

Se nunca tiver utilizado o **Azure Machine Learning Studio** antes, poderá querer começar com o tutorial, [Create your first data science experiment in Azure Machine Learning Studio (Criar a primeira experimentação de ciência de dados no Azure Machine Learning Studio)](create-experiment.md). Esse tutorial orienta-o ao longo do Machine Learning Studio pela primeira vez. Mostra-lhe as noções básicas de como arrastar e largar módulos na experimentação, ligá-los entre si, executar a experimentação e analisar os resultados. Outra ferramenta que pode ser útil para começar a trabalhar é um diagrama que dá uma visão geral das funcionalidades do Machine Learning Studio. Pode transferir e imprimi-lo a partir daqui: [Diagrama de descrição geral das funcionalidades do Azure Machine Learning Studio](studio-overview-diagram.md).
 
Se não estiver familiarizado com o machine learning em geral, existe uma série em vídeo que poderá ser útil para si. Chama-se [Data Science for Beginners (Ciência de Dados para Principiantes)](data-science-for-beginners-the-5-questions-data-science-answers.md) e pode dar-lhe uma excelente introdução ao machine learning utilizando linguagem e conceitos do dia a dia.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>O problema

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação do risco de crédito é um problema complexo, mas podemos simplificá-lo um pouco para estas instruções. Iremos utilizá-lo como um exemplo de como pode criar uma solução de análise preditiva com o Microsoft Azure Machine Learning. Para tal, utilizamos o Azure Machine Learning Studio e um serviço Web Machine Learning.  

## <a name="the-solution"></a>A solução

Nestas instruções detalhadas, começamos com dados de risco de crédito disponíveis ao público e desenvolvemos e formamos um modelo preditivo com base nesses dados. Em seguida, implementamos o modelo como um serviço web, para que possa ser utilizado por outros utilizadores para avaliação de risco de crédito.

Para criar esta solução de avaliação do risco de crédito, temos de executar os seguintes passos:  

1. [Criar uma área de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Carregar os dados existentes](walkthrough-2-upload-data.md)
3. [Criar uma experimentação](walkthrough-3-create-new-experiment.md)
4. [Dar formação e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implementar o serviço web](walkthrough-5-publish-web-service.md)
6. [Aceder ao serviço web](walkthrough-6-access-web-service.md)

> [!TIP] 
> Pode encontrar uma cópia de trabalho da experimentação que desenvolvemos nestas instruções na [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com). Aceda a **[Walkthrough - Credit risk prediction](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)** e clique em **Open in Studio** para transferir uma cópia da experimentação para a área de trabalho do Machine Learning Studio.
> 
> Estas instruções baseiam-se numa versão simplificada da experimentação de exemplo, [Binary Classification: Credit risk prediction](http://go.microsoft.com/fwlink/?LinkID=525270), também disponível na [Gallery](http://gallery.cortanaintelligence.com/).
