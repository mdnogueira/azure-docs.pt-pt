---
title: "Como dimensionar o ambiente de informações de séries de tempo do Azure | Microsoft Docs"
description: "Este tutorial abrange como dimensionar o ambiente de informações de séries de tempo do Azure"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: ba6bd1ab05bb7e24dd1bc307218e7a772fbde601
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como dimensionar o ambiente de informações de séries de tempo

Este tutorial abrange como dimensionar o ambiente de informações de séries de tempo.

> [!NOTE]
> Não é permitido o trabalho de dimensionamento em tipos de sku. Não é possível converter um ambiente com um Sku S1 um ambiente de S2.

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

As capacidades dimensionadas de forma linear, pelo que um sku S1 com capacidade 2 suporta 2 GB (2 milhões) eventos por taxa de entrada do dia e 60 GB (60 milhões de eventos) por mês.

## <a name="changing-the-capacity-of-your-environment"></a>Alterar a capacidade do seu ambiente

1. No portal do Azure, selecione o ambiente cuja capacidade que pretende alterar.
1. Em definições, clique em configurar.
1. Utilize o controlo de deslize de capacidade para selecionar a capacidade que cumpra os requisitos para as taxas de entrada e a capacidade de armazenamento.

## <a name="next-steps"></a>Passos seguintes

* Certifique-se de que a nova capacidade é suficiente para impedir a limitação. Para obter mais detalhes, consulte o *ambiente poderá obter limitado* secção [aqui](time-series-insights-diagnose-and-solve-problems.md).