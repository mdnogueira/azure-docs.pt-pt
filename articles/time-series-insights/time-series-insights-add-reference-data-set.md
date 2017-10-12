---
title: "Adicionar um conjunto de dados de referência ao ambiente do Azure Time Series Insights | Microsoft Docs"
description: "Neste tutorial, irá adicionar um conjunto de dados de referência ao seu ambiente do Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.openlocfilehash: 574f3c5b3a889733f47d729c795ec39009f2ef4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Criar um conjunto de dados de referência para o seu ambiente do Time Series Insights com o portal Ibiza

Um conjunto de dados de referência é uma coleção de itens que são aumentados com os eventos da origem de evento. O mecanismo de entrada do Time Series Insights une um evento da origem de evento com um item no seu conjunto de dados de referência. Esse evento aumentado fica então disponível para consulta. Essa união baseia-se nas chaves definidas no conjunto de dados de referência.

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>Passos para adicionar um conjunto de dados de referência ao seu ambiente

1. Inicie sessão no [portal Ibiza](https://portal.azure.com).
2. Clique em "Todos os recursos" no menu do lado esquerdo do portal Ibiza.
3. Selecione o seu ambiente do Time Series Insights.

    ![Criar o conjunto de dados de referência do Time Series Insights](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Selecione "Conjuntos de dados de referência", clique em "+ Adicionar".

    ![Criar o conjunto de dados de referência do Time Series Insights - detalhes](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. Especifique o nome do conjunto de dados de referência.
6. Especifique o nome da chave e o respetivo tipo. Este nome e tipo é usado para selecionar a propriedade correta do evento na origem de evento. Por exemplo, se fornecer o nome da chave como "DeviceId" e digitar como "String", então o mecanismo de entrada do Time Series Insights procura uma propriedade com o nome "DeviceId" do tipo "String" no evento de entrada. Poderá fornecer mais de uma chave para adicionar ao evento. A correspondência do nome da propriedade é sensível a maiúsculas e minúsculas.

     ![Criar o conjunto de dados de referência do Time Series Insights - detalhes](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. Clique em “Criar”.

## <a name="next-steps"></a>Passos seguintes

* [Gerir dados de referência](time-series-insights-manage-reference-data-csharp.md) através de programação.
* Para obter a referência completa de API, consulte o documento [API de dados de referência](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).