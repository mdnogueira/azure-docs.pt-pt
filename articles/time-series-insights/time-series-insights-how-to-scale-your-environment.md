---
title: "Como dimensionar o ambiente de informações de séries de tempo do Azure | Microsoft Docs"
description: "Este artigo descreve como dimensionar o ambiente de informações de séries de tempo do Azure. Utilize o portal do Azure para adicionar ou subtrair capacidade dentro de um SKU de preço."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: edcd9561778998c4df09cc5014f8b8ba81c0e369
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como dimensionar o ambiente de informações de séries de tempo

Este artigo descreve como alterar a capacidade do seu ambiente o ambiente de informações de séries de tempo no portal do Azure. A capacidade é o multiplicador aplicado para a taxa de entrada, a capacidade de armazenamento e o custo associado à sua SKU selecionado. 

Pode utilizar o portal do Azure para aumentar ou diminuir a capacidade dentro de um determinado SKU de preço. 

No entanto, se alterar o escalão de preço SKU não é permitida. Por exemplo, um ambiente com um S1 preços SKU não é possível converter para um S2 e vice-versa. 


## <a name="s1-sku-ingress-rates-and-capacities"></a>Taxas de entrada S1 SKU e as capacidades

| S1 Capacidade SKU | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão de eventos) | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Taxas de entrada de SKU de S2 e as capacidades

| S2 Capacidade SKU | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões de eventos) | 3 TB (3 milhões de eventos) por mês |

As capacidades dimensionadas de forma linear, pelo que um SKU S1 com capacidade 2 suporta 2 GB (2 milhões) eventos por taxa de entrada do dia e 60 GB (60 milhões de eventos) por mês.

## <a name="change-the-capacity-of-your-environment"></a>Alterar a capacidade do seu ambiente
1. No portal do Azure, localize e selecione o seu ambiente de informações de séries de tempo. 

2. No menu para o seu ambiente Insighs de séries de tempo, selecione **configurar**.

   ![Configure.png](media/scale-your-environment/configure.png)

3. Ajustar o **capacidade** controlo de deslize para selecionar a capacidade que cumpra os requisitos para as taxas de entrada e a capacidade de armazenamento. Tenha em atenção o **taxa de entrada**, **capacidade de armazenamento**, e **estimado custo** atualização dinâmica para mostrar o impacto da alteração. 

   ![Controlo de deslize](media/scale-your-environment/slider.png)

   Em alternativa, pode escrever o número do multiplicador capacidade na caixa de texto à direita do controlo de deslize. 

4. Selecione **guardar** dimensionar o ambiente. O indicador de progresso é apresentado até que a alteração é consolidada, momentaneamente. 

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Certifique-se de que a nova capacidade suficiente para impedir a limitação](time-series-insights-diagnose-and-solve-problems.md).
