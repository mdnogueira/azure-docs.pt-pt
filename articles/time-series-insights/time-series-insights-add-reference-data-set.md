---
title: "Como adicionar um conjunto de dados de referência para o seu ambiente de informações de séries de tempo do Azure | Microsoft Docs"
description: "Este artigo descreve como adicionar um conjunto de dados de referência para o seu ambiente de informações de séries de tempo do Azure. Dados de referência são útil para associar a sua origem de dados para aumentar os valores."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Criar um conjunto de dados de referência para o seu ambiente de informações de séries de tempo no portal do Azure

Este artigo descreve como adicionar um conjunto de dados de referência para o seu ambiente de informações de séries de tempo do Azure. Dados de referência são útil para associar a sua origem de dados para aumentar os valores.

Um conjunto de dados de referência é uma coleção de itens que são aumentados com os eventos da origem de evento. O mecanismo de entrada do Time Series Insights une um evento da origem de evento com um item no seu conjunto de dados de referência. Esse evento aumentado fica então disponível para consulta. Essa união baseia-se nas chaves definidas no conjunto de dados de referência.

## <a name="add-a-reference-data-set"></a>Adicionar um conjunto de dados de referência

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente existente do Insights de séries de tempo. Clique em **todos os recursos** no menu no lado esquerdo do portal do Azure. Selecione o seu ambiente do Time Series Insights.

3. Sob o **topologia do ambiente** cabeçalho, selecione **conjuntos de dados de referência**.

    ![Criar o conjunto de dados de referência do Time Series Insights](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Selecione **+ adicionar** para adicionar um novo conjunto de dados de referência.

5. Especifique um único **o nome do conjunto de dados de referência**.

    ![Criar o conjunto de dados de referência do Time Series Insights - detalhes](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. Especifique o **nome da chave** no campo em branco e selecione o **tipo**. Este nome e tipo são utilizados para escolher a propriedade correta dos eventos de na sua origem de evento para associar os dados de referência. 

   Por exemplo, se fornecer o nome da chave como **DeviceId** e escreva como **cadeia**, em seguida, procura o motor de entrada Insights de séries de tempo para uma propriedade com o nome **DeviceId** do tipo **Cadeia** em cada evento de entrada parecer configurar e associar. Poderá fornecer mais de uma chave para adicionar ao evento. A correspondência do nome da chave é sensível a maiúsculas e minúsculas.

7. Selecione **Criar**.

## <a name="next-steps"></a>Passos seguintes
* [Gerir dados de referência](time-series-insights-manage-reference-data-csharp.md) através de programação.
* Para obter a referência completa de API, consulte o documento [API de dados de referência](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
