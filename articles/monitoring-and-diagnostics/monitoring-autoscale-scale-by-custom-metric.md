---
title: "Começar com uma escala automática da métrica personalizada no Azure | Microsoft Docs"
description: "Saiba como dimensionar o seu recurso da métrica personalizada no Azure."
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
ms.openlocfilehash: de8f7acadc282e4b81c657b1723f00fd3e5fd4f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Começar com uma escala automática da métrica personalizada no Azure
Este artigo descreve como dimensionar o seu recurso por uma métrica personalizada no portal do Azure.

Escala de automática de Monitor do Azure só se aplica a conjuntos de dimensionamento da Máquina Virtual (VMSS), cloud services, planos de serviço de aplicações e ambientes do app service. 

# <a name="lets-get-started"></a>Permite começar a utilizar
Este artigo pressupõe que tem uma aplicação web com o application insights configurados. Se ainda não tiver uma, pode [configurar o Application Insights para o seu Web site ASP.NET][1]

- Abra [portal do Azure][2]
- Clique no ícone de Monitor do Azure no painel de navegação esquerdo.
  ![Iniciar o Monitor do Azure][3]
- Clique na definição de dimensionamento automático para ver todos os recursos para os qual automática escala é aplicável, juntamente com o respetivo estado atual do dimensionamento automático ![detetar escala automática no monitor do Azure][4]
- Abra o painel de 'dimensionamento automático' no Monitor do Azure e selecione um recurso que pretende dimensionar
> Nota: Os passos abaixo utilizam um plano de serviço de aplicação associado uma aplicação web que tem informações de aplicação configuradas.
- No painel de definição de dimensionamento para o recurso, tenha em atenção que a contagem atual de instâncias é 1. Clique em 'Ativar dimensionamento automático'.
  ![Definição de dimensionamento para a nova aplicação web][5]
- Forneça um nome para a definição de dimensionamento e clique em "Adicionar uma regra". Tenha em atenção as opções de regra de escala que abre-se como um painel de contexto no lado direito. Por predefinição, define a opção para dimensionar a contagem de instâncias por 1 se o percetage de CPU do recurso exceder 70%. Alterar a origem de métrica na parte superior para "Application Insights", selecione o recurso de informações de aplicações na lista pendente 'Resource' e, em seguida, selecione a métrica personalizada com base no qual pretende dimensionar.
  ![Dimensionar por métrica personalizada][6]
- À semelhança do passo acima, adicione uma regra de escala que irá aumentar no e diminuir a contagem de escalas por 1 se a métrica personalizada está abaixo de um limiar.
  ![Escala com base na cpu][7]
- Definir o que os limites de instância. Por exemplo, se pretender dimensionar entre 2 a 5 instâncias, consoante as flutuações de métricas personalizadas, defina 'mínima' para '2', 'máximo' para '5' e 'default' para ' 2'
> Nota: no caso de existir um problema ao ler as métricas de recurso e a capacidade atual é inferior a capacidade predefinida, em seguida, para garantir a disponibilidade do recurso, dimensionamento automático irá aumentar horizontalmente o valor predefinido. Se a capacidade atual já for superior à capacidade predefinida, o dimensionamento automático não será dimensionado no.
- Clique em 'Guardar'

Parabéns! Agora agora com êxito criado a escala definição para auto dimensionamento da sua aplicação web com base numa métrica personalizada.

> Nota: Os mesmos passos são aplicáveis a começar com uma função de serviço VMSS ou nuvem.

<!--Reference-->
[1]: https://docs.microsoft.com/en-us/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
