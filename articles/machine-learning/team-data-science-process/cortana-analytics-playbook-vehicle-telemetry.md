---
title: "Estado de funcionamento vehicle de prever e ocasionar hábitos - Azure | Microsoft Docs"
description: "Utilizar as capacidades do Cortana Intelligence para obter informações acerca de preditiva e em tempo real em estado de funcionamento vehicle e ocasionar hábitos."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: af8b3d5bf891c93c30a05c5f02d86639a466dde5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Playbook de solução de análise de telemetria de veículos
Isto **menu** ligações para capítulos neste manual de comunicação social. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Descrição geral
Computadores superutilizadores movido fora do laboratório e agora são parqueados no nosso garage! Estes automóveis inovadores contêm um conjunto de sensores, concedendo-lhes a capacidade de controlar e monitorizar milhões de eventos a cada segundo. Esperamos que por 2020, a maioria destas carros irá ter foi ligada à Internet. Imagine tocar para este variedade de dados para fornecer maior segurança, fiabilidade e uma experiência despertar melhor! Microsoft efetuou esta dream uma realidade com Cortana Intelligence.

Cortana Intelligence da Microsoft é um macrodados completamente geridos e um conjunto de análise avançada permite-lhe transformar os dados em ação inteligente. Queremos apresentam o modelo de solução de análise de telemetria do Cortana Intelligence Vehicle. Esta solução demonstra como dealerships carro, fabricantes automóveis e insurance empresas podem utilizar as capacidades do Cortana Intelligence para obter em tempo real e conhecimentos aprofundados preditivos acerca do Estado de funcionamento vehicle e ocasionar hábitos. 

A solução é implementada como um [padrão de arquitetura de lambda](https://en.wikipedia.org/wiki/Lambda_architecture) que mostra o completo potenciais da plataforma Cortana Intelligence para em tempo real e processamento em lote. Solução: 

* Fornece um simulador Vehicle Telematics
* tira partido dos Event Hubs para ingestão de milhões de eventos de telemetria simulada vehicle relacionadas no Azure 
* utiliza o Stream Analytics para obter informações em tempo real no estado de funcionamento vehicle
* mantém os dados para armazenamento a longo prazo para a análise de lote mais rico. 
* tira partido da Machine Learning para deteção de anomalias em tempo real e processamento para obter informações acerca da preditiva do batch.
* tira partido do HDInsight para transformar dados em escala e a fábrica de dados orchestration, agendamento, gestão de recursos, e a monitorizar o pipeline de processamento em lote 
* fornece esta solução um dashboard avançado para dados em tempo real e visualizações de Análise Preditiva através do Power BI

## <a name="architecture"></a>Arquitetura
![Diagrama de arquitetura de solução](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*figura 1 – arquitetura de solução de análise de telemetria Vehicle*

Esta solução inclui o seguinte **componentes do Cortana Intelligence** e showcases os respetivos integração de ponto a ponto:

* **Os Event Hubs** para ingestão de milhões de eventos de telemetria vehicle relacionadas no Azure.
* **Stream Analytics** para obter informações em tempo real no estado de funcionamento vehicle e persistir os dados para armazenamento a longo prazo para a análise de lote mais rico.
* **Machine Learning** para deteção de anomalias em tempo real e processamento em lote para obter informações acerca de preditiva.
* **HDInsight** é utilizado para transformar dados à escala
* **Fábrica de dados** processa orchestration, agendamento, de gestão de recursos e monitorizar o pipeline de processamento em lote.
* **Power BI** fornece esta solução um dashboard avançado para dados em tempo real e visualizações de Análise Preditiva.

Acede a esta solução dois diferentes **origens de dados**: 

* **Simulated vehicle sinais e diagnóstico**: um simulador de telematics vehicle emite informações de diagnóstico e sinais que correspondem para o estado do veículo e o padrão despertar num determinado ponto no tempo. 
* **Catálogo de vehicle**: um conjunto de dados de referência que contém um VIN para mapeamento de modelo.

