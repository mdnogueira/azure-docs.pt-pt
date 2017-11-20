---
title: "Instruções da solução de manutenção preditiva - Azure | Microsoft Docs"
description: "Instruções sobre a solução pré-configurada de manutenção preditiva do Azure IoT."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: e9e0024c645d0e04e7cf9b17e440d7d8c10af232
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Instruções sobre a solução pré-configurada de manutenção preditiva

A solução pré-configurada de manutenção preditiva é uma solução ponto a ponto para um cenário de negócio que prevê o momento em que poderá ocorrer uma falha. Pode utilizar, de forma pró-ativa, esta solução pré-configurada para atividades como a manutenção de otimização. A solução combina os serviços principais do Azure IoT Suite, como uma Hub IoT, Stream Analytics e uma área de trabalho do [Azure Machine Learning][lnk-machine-learning]. Esta área de trabalho contém um modelo, com base num conjunto de dados de exemplo público, para prever a Vida Útil Remanescente (RUL) de um motor de aeronave. A solução implementa completamente o cenário empresarial IoT como um ponto de partida para que possa planear e implementar uma solução que satisfaça os seus próprios requisitos de negócio.

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos da solução pré-configurada:

![][img-architecture]

Os itens a azul são os serviços do Azure aprovisionados na região em que implementou a solução pré-configurada. Apresenta a lista de regiões onde pode implementar a solução pré-configurada na [página aprovisionamento][lnk-azureiotsuite].

O item a verde é um dispositivo simulado que representa o motor de uma aeronave. Pode saber mais sobre estes dispositivos simulados na secção [Dispositivos simulados](#simulated-devices) seguinte.

Os itens a cinzento representam componentes que implementam capacidades de *gestão do dispositivo*. A atual versão da solução pré-configurada de manutenção preditiva não aprovisiona estes recursos. Para saber mais sobre a gestão do dispositivo, consulte [remote monitoring pre-configured solution (solução pré-configurada de monitorização remota)][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Dispositivos simulados

Na solução pré-configurada, um dispositivo simulado representa o motor de uma aeronave. A solução é aprovisionada com dois motores que mapeiam para uma única aeronave. Cada motor emite quatro tipos de telemetria: Sensor 9, Sensor 11, Sensor 14 e Sensor 15, que fornecem os dados necessários para que o modelo do Machine Learning possa calcular a RUL desse motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria ao IoT Hub:

*Ciclo de contagem*. Um ciclo representa um voo concluído com uma duração entre duas a dez horas. Durante o voo, os dados telemétricos são capturados a cada meia hora.

*Telemetria*. Existem quatro sensores que representam os atributos do motor. Os sensores são geralmente denominados Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Estes quatro sensores representam telemetria suficiente para obter resultados úteis a partir do modelo de RUL. O modelo utilizado na solução pré-configurada é criado a partir de um conjunto de dados públicos, que inclui dados do sensor do motor. Para obter mais informações sobre a criação do modelo a partir do conjunto de dados original, consulte o artigo [Cortana Intelligence Gallery Predictive Maintenance Template (Modelo de Manutenção Preditiva do Cortana Intelligence Gallery)][lnk-cortana-analytics].

Os dispositivos simulados podem processar os seguintes comandos enviados a partir do hub IoT na solução:

| Comando | Descrição |
| --- | --- |
| StartTelemetry |Controla o estado da simulação.<br/>Inicia o dispositivo que envia a telemetria |
| StopTelemetry |Controla o estado da simulação.<br/>Para o dispositivo que envia a telemetria |

O IoT Hub reconhece o comando do dispositivo.

## <a name="azure-stream-analytics-job"></a>Tarefa do Azure Stream Analytics

**Tarefa: Telemetria** intervém no fluxo de telemetria de entrada do dispositivo através de duas instruções:

* A primeira seleciona toda a telemetria dos dispositivos e envia esses dados ao armazenamento de blobs. Aí, são visualizados na aplicação Web.
* A segunda calcula os valores médios do sensor numa janela deslizante e envia esses dados através do Hub de Eventos a um **processador de eventos**.

## <a name="event-processor"></a>Processador de eventos
O **anfitrião do processador de eventos** executa uma Tarefa Web do Azure. O **processador de eventos** obtém os valores médios do sensor para um ciclo concluído. Em seguida, transmite esses valores para uma API que expõe o modelo formado para calcular a RUL de um motor. A API é exposta por uma área de trabalho do Machine Learning que está aprovisionada como parte da solução.

## <a name="machine-learning"></a>Machine Learning
O componente de Machine Learning utiliza um modelo derivado dos dados recolhidos de motores de aeronave reais. Pode navegar para a área de trabalho do Machine Learning a partir do mosaico da sua solução na página [azureiotsuite.com][lnk-azureiotsuite]. O mosaico está disponível quando a solução está no estado **Pronto**.


## <a name="next-steps"></a>Passos seguintes
Agora que viu os principais componentes da solução de manutenção preditiva pré-configurada, deverá personalizá-los. Consulte [Guidance on customizing preconfigured solutions (Documentação de orientação sobre como personalizar soluções pré-configuradas)][lnk-customize].

Também pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

* [Perguntas mais frequentes sobre o IoT Suite][lnk-faq]
* [Segurança de IoT desde o início][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/