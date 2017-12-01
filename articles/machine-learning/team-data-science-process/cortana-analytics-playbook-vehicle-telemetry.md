---
title: "Estado de funcionamento vehicle de prever e ocasionar hábitos - Azure | Microsoft Docs"
description: "Utilizar as capacidades do Cortana Intelligence para obter informações acerca de preditiva e em tempo real em estado de funcionamento vehicle e ocasionar hábitos."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: bradsev
ms.openlocfilehash: 8584cade8e12b4f06ae455227e702844a06f512a
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Manual de comunicação social vehicle solução de análise de telemetria
Liga menu-capítulos neste manual de comunicação social: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Descrição geral
Supercomputers movido fora do laboratório e agora são parqueados no garages. Estes automóveis inovadores contenham sensores conjunto, o qual conceder-lhes a capacidade de controlar e monitorizar milhões de eventos a cada segundo. Por 2020, a maioria destas veículos será ligada à Internet. Ao tocar em para este variedade de dados pode fornecer maior segurança, fiabilidade e uma melhor despertar experiência. Microsoft faz com que esta dream uma realidade com Cortana Intelligence.

Cortana Intelligence é um macrodados completamente geridos e suite análises avançadas que pode utilizar para transformar os dados para a ação inteligente. O modelo de solução de análise de telemetria do Cortana Intelligence Vehicle demonstra como dealerships carro, fabricantes automóveis e insurance empresas podem obter em tempo real e conhecimentos aprofundados preditivos acerca do Estado de funcionamento vehicle e ocasionar hábitos. 

A solução é implementada como um [padrão de arquitetura de lambda](https://en.wikipedia.org/wiki/Lambda_architecture), que mostra o completo potenciais da plataforma Cortana Intelligence para em tempo real e processamento em lote.

## <a name="architecture"></a>Arquitetura
A arquitetura de solução de análise de telemetria Vehicle é ilustrada neste diagrama:

![Diagrama de arquitetura de solução](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Esta solução inclui os seguintes componentes do Cortana Intelligence e showcases os respetivos integração de ponto a ponto:

* **Os Hubs de eventos do Azure** ingere milhões de eventos de telemetria vehicle no Azure.
* **O Azure Stream Analytics** fornece informações em tempo real sobre o estado de funcionamento do vehicle e persistir os dados para armazenamento a longo prazo para a análise de lote mais rico.
* **O Azure Machine Learning** Deteta anomalias em tempo real e utiliza o processamento em lote para fornecer informações preditivos.
* **O Azure HDInsight** transforma dados à escala.
* **O Azure Data Factory** processa orchestration, agendamento, gestão de recursos e monitorizar o pipeline de processamento em lote.
* **Power BI** fornece esta solução um dashboard avançado para dados em tempo real e visualizações de Análise Preditiva.

Esta solução acede duas origens de dados diferentes: 

* **Simulated vehicle sinais e diagnóstico**: um simulador de telematics vehicle emite informações de diagnóstico e sinais que correspondem para o estado do veículo e o padrão despertar num determinado ponto no tempo. 
* **Catálogo de vehicle**: este conjunto de dados de referência mapeia VINs para modelos.

