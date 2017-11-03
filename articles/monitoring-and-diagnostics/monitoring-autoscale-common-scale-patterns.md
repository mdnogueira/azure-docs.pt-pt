---
title: "Descrição geral de padrões comuns de dimensionamento automático | Microsoft Docs"
description: "Saiba mais alguns de padrões comuns para automática escala os recursos no Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.openlocfilehash: fce51546e041c8989d813c3935e058c52b38ba77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-common-autoscale-patterns"></a>Descrição geral de padrões comuns de dimensionamento automático
Este artigo descreve algumas dos padrões comuns dimensionar o seu recurso no Azure.

Escala de automática de Monitor do Azure só se aplica a conjuntos de dimensionamento da Máquina Virtual (VMSS), cloud services, planos de serviço de aplicações e ambientes do app service. 

# <a name="lets-get-started"></a>Permite começar a utilizar

Este artigo pressupõe que está familiarizado com uma escala automática. Pode [começar aqui dimensionar o seu recurso][1]. Seguem-se alguns dos padrões comuns da escala.

## <a name="scale-based-on-cpu"></a>Escala com base na CPU

Tiver uma aplicação web (VMSS/nuvem função do serviço) e 

- Pretende escala out/escala em CPU com base no.
- Além disso, pretender certificar-se de que há um número mínimo de instâncias. 
- Além disso, pretender certificar-se de que defina um limite máximo para o número de instâncias que pode escalar.

![Escala com base na CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Dimensionar de forma diferente no fim de semana do dias da semana vs

Tiver uma aplicação web (VMSS/nuvem função do serviço) e

- Pretende 3 instâncias por predefinição (em dias da semana)
- Não espera que o tráfego no fim de semana e, por conseguinte, se pretender dimensionar para baixo até 1 instância no fim de semana.

![Dimensionar de forma diferente no fim de semana do dias da semana vs][3]

## <a name="scale-differently-during-holidays"></a>Dimensionar de forma diferente durante feriados

Tiver uma aplicação web (VMSS/nuvem função do serviço) e 

- Pretende dimensionar para cima/para baixo com base na utilização da CPU por predefinição
- No entanto, durante a época (ou os dias específicos que são importantes para a sua empresa) que pretende substituir as predefinições e mais capacidade de ter no seu disposal.

![Feriados de forma diferente em escala][4]

## <a name="scale-based-on-custom-metric"></a>Escala com base numa métrica personalizada

Tem um front-end da web e uma camada de API que comunica com o back-end. 

- Pretende dimensionar o escalão de API com base em eventos personalizados no front-end (exemplo: pretende dimensionar o processo de finalização da compra com base no número de itens no carrinho de compras)

![Escala com base numa métrica personalizada][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png