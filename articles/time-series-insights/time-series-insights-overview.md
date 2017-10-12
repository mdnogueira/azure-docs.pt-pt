---
title: "Descrição Geral do Azure Time Series Insights | Microsoft Docs"
description: "Introdução ao Azure Time Series Insight, um serviço novo para análises de dados de séries de tempo e soluções de IoT"
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.openlocfilehash: 1814459e47280af62450a4093140ab6ab9b765fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-time-series-insights"></a>O que é o Azure Time Series Insights

O Azure Time Series Insights é um serviço cloud gerido com componentes de armazenamento, análise e visualização que fazem com que seja fácil ingerir, armazenar, explorar e analisar milhares de milhões de eventos em simultâneo. O Time Series Insights dá-lhe uma vista global dos seus dados, o que lhe permite validar rapidamente as suas soluções de IoT e evitar um período de indisponibilidade dispendioso para os dispositivos ao ajudá-lo a descobrir tendências e anomalias ocultas e a realizar análises da origem dos problemas praticamente em tempo real. O Time Series Insights ingere dados de séries de tempo a partir de mediadores de eventos (por exemplo, Hubs IoT ou Hubs de Eventos), indexa-os e extingue-os com base numa política de retenção configurável. Os utilizadores consomem os dados através de uma UX intuitiva ou de APIs REST Query.

![Descrição Geral do Time Series Insight](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Cenários principais

* Monitorizar e validar as soluções IoT em minutos.
* Visualizar e analisar dados de IoT em escala.
* Acelerar a análise de origens de problemas e a deteção de anomalias.
* Criar uma vista global de vários dispositivos, instalações e dados.

## <a name="capabilities-and-benefits"></a>Capacidades e vantagens

* **Fácil começar**: o Azure Time Series Insights não requer preparação prévia de dados e é incrivelmente rápido. Ligue a milhares de milhões de eventos no seu Hub IoT do Azure ou no seu Hub de Eventos em minutos. Depois de ligado, pode visualizar e interagir com os dados de sensores em segundos para validar rapidamente as suas soluções IoT. O Time Series Insights é fácil de utilizar. Pode interagir com os seus dados sem ter de escrever uma única linha de código.  Não tem de aprender linguagens novas, pois o Time Series Insights proporciona uma superfície de consultas granular e isenta de texto para utilizadores avançados, bem como exploração por “apontar e clicar” para todos os utilizadores.

* **Informações em tempo quase real**: o Time Series Insights pode ingerir centenas de milhões de eventos de sensores por dia, com latência de um minuto, para que possa reagir às alterações rapidamente. Proporciona-lhe informações mais detalhadas sobre os dados de sensores, ao ajudá-lo a detetar tendências e anomalias depressa, a realizar facilmente análises de origens de problemas complexas e a evitar tempos de indisponibilidade dispendiosos. Ao ativar a correlação cruzada entre os dados em tempo real e os dados históricos, o Time Series Insights ajuda-o a revelar tendências ocultas nos dados.

* **Criar soluções personalizadas**: incorpore dados do Azure Time Series Insights nas suas aplicações existentes ou crie soluções personalizadas novas com as APIs REST do Time Series Insights. Criar e partilhar vistas personalizadas que pode partilhar para que as outras pessoas possam explorar as suas descobertas.

* **Escalabilidade**: o Time Series Insights foi concebido para suportar IoT em escala. Na Pré-visualização, pode receber entre um milhão e cem milhões de eventos por dia, com um intervalo de retenção predefinido de 31 dias. Pode ver e analisar fluxos de dados em direto em tempo quase real, juntamente com grandes quantidades de dados históricos. No futuro, as taxas de receção e retenção vão aumentar, de modo a satisfazer a sempre crescente escala empresarial.

## <a name="time-series-insights-glossary"></a>Glossário do Time Series Insights

* **Ambiente**: um ambiente é um recurso do Azure com capacidade de receção e armazenamento.  Os clientes aprovisionam os ambientes através do portal do Azure com a capacidade de que precisam.
* **Origem de Eventos**: as Origens de Eventos do Time Series Insights derivam de um mediador de eventos, como os Hubs de Eventos do Azure.  O Time Series Insights liga-se diretamente às Origens de Eventos, ingerindo o fluxo de dados sem que seja escrita uma única linha de código. Atualmente, o Time Series Insights suporta Hubs de Eventos do Azure e Hubs IoT do Azure.
* **Dados de referência**: o Time Series Insights dá aos utilizadores a capacidade de combinar dados de séries de tempo com dados de referência.  Os dados de referência podem incluir metadados sobre dispositivos ou outros dados estáticos que são alterados de forma relativamente pouco frequente. O Time Series Insights combina os dados de referência com os fluxos de dados, permitindo aos utilizadores vê-los e analisá-los em tempo quase real.
