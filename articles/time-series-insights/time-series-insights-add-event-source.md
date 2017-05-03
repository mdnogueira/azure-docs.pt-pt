---
title: Adicionar uma origem de eventos ao ambiente do Azure Time Series Insights | Microsoft Docs
description: Neste tutorial, vai ligar uma origem de eventos ao seu ambiente do Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 9dc394c0da34a8ee2796c6114e7f2f647c5345a1
ms.lasthandoff: 04/25/2017

---

# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-azure-portal"></a>Criar uma origem de eventos para o seu ambiente do Time Series Insights com o portal do Azure

A Origem de Eventos do Time Series Insights deriva de um mediador de eventos, como os Hubs de Eventos do Azure. O Time Series Insights liga-se diretamente às Origens de Eventos, ingerindo o fluxo de dados sem que os utilizadores tenham de escrever uma única linha de código. Atualmente, o Time Series Insights suporta os Hubs de Eventos do Azure e o Hub IoT do Azure e serão adicionadas mais Origens de Eventos no futuro.

## <a name="steps-to-add-an-event-source-to-your-environment"></a>Passos para adicionar uma origem de eventos ao seu ambiente

1.    Inicie sessão no [Portal do Azure](https://portal.azure.com).
2.    Clique em "Todos os recursos" no menu no lado esquerdo do Portal do Azure.
3.    Selecione o seu ambiente do Time Series Insights.

  ![Criar a origem de eventos do Time Series Insights](media/add-event-source/getstarted-create-eventsource1.png)

4.    Selecione "Origens de Eventos", clique em "+ Adicionar”.

  ![Criar a origem de eventos do Time Series Insights - detalhes](media/add-event-source/getstarted-create-eventsource2.png)

5.    Especifique o nome da origem de eventos. Este nome é associado a todos os eventos provenientes desta origem de eventos e está disponível no momento da consulta.
6.    Selecione um hub de eventos a partir da lista de recursos do Hub de Eventos na subscrição atual ou escolha a opção de importação “Fornecer definições dos Hubs de Eventos manualmente”, para especificar um hub de eventos localizado noutra subscrição. Os eventos têm de ser publicados no formato JSON.
7.    Selecione a política que tem a permissão de leitura no hub de eventos.
8.    Especifique o grupo de consumidores do hub.

  > [!IMPORTANT]
  > Certifique-se de que este grupo de consumidores não é utilizado por nenhum outro serviço (como uma tarefa do Stream Analytics ou outro ambiente do Time Series Insights). Se for utilizado por outros serviços, a operação de leitura é afetada negativamente neste ambiente e nos outros serviços. A utilização de “$Default” como o grupo de consumidores pode levar à potencial reutilização por parte de outros leitores.

9.    Clique em “Criar”.

Depois de criar a origem de eventos, o Time Series Insights vai começar a transmitir dados para o seu ambiente automaticamente.

## <a name="next-steps"></a>Passos seguintes

* [Enviar eventos](time-series-insights-send-events.md) para a origem de eventos
* Ver o seu ambiente no [Portal do Time Series Insights](https://insights.timeseries.azure.com)

