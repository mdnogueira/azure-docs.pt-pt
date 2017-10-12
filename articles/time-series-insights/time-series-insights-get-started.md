---
title: Criar um ambiente do Azure Time Series Insights | Microsoft Docs
description: "Neste tutorial, irá aprender a criar o Ambiente de Time Series, ligá-lo a uma origem de evento e está preparado para analisar os dados de evento em minutos."
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
ms.openlocfilehash: 5a159ed0ba38899b21212518d41ff995895904ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Criar um novo ambiente do Time Series Insights no portal do Azure

O ambiente do Time Series Insights é um recurso do Azure com capacidade de receção e armazenamento. Os clientes aprovisionam os ambientes através do portal do Azure com a capacidade de que precisam.

## <a name="steps-to-create-the-environment"></a>Passos para criar o ambiente

Siga estes passos para criar o seu ambiente:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com).
2.  Clique no sinal ("+") no canto superior esquerdo.
3.  Procure "Time Series Insights" na caixa de pesquisa.

  ![Crie o ambiente do Time Series Insights](media/get-started/getstarted-create-environment1.png)

4.  Selecione "Time Series Insights", clique em "Criar".

  ![Crie o grupo de recursos do Time Series Insights](media/get-started/getstarted-create-environment2.png)

5.  Especifique o nome do ambiente. Este nome irá representar o ambiente no [explorador de séries de tempo](https://insights.timeseries.azure.com).
6.  Selecione uma subscrição. Escolha uma que contenha a origem do evento. O Time Series Insights pode detetar automaticamente recursos do Hub IoT do Azure e do Hub de Eventos existentes na mesma subscrição.
7.  Selecione ou crie um grupo de recursos. Um grupo de recursos é uma coleção de recursos do Azure utilizados em conjunto.
8.  Selecione uma localização de alojamento. Para evitar de mover dados entre os centros de dados, escolha a localização que contém a origem do evento.
9.  Selecione um escalão de preço.
10. Selecione a capacidade. Pode alterar a capacidade de um ambiente após a criação.
11. Crie o seu ambiente. Também pode afixar o ambiente ao dashboard para facilitar o acesso sempre que iniciar sessão.

  ![Crie o marcador do Time Series Insights para o dashboard](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>Passos seguintes

* [Definir políticas de acesso de dados](time-series-insights-data-access.md) para aceder ao seu ambiente no [Portal do Time Series Insights](https://insights.timeseries.azure.com)
* [Criar uma origem de eventos](time-series-insights-add-event-source.md)
* [Enviar eventos](time-series-insights-send-events.md) para a origem de eventos
