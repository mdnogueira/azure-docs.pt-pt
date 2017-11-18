---
title: "Utilizar métricas para monitorizar o IoT Hub do Azure | Microsoft Docs"
description: "Como utilizar as métricas do IoT Hub do Azure para avaliar e monitorizar o estado de funcionamento geral os hubs IoT."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cafe5009b8e96b147b5bbed1957024f6d96feb58
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="understand-iot-hub-metrics"></a>Compreender as métricas do IoT Hub
Métricas de IoT Hub dão-lhe uma melhor dados sobre o estado dos recursos do Azure IoT na sua subscrição do Azure. Métricas de IoT Hub permitem-lhe avaliar o estado de funcionamento geral do serviço IoT Hub e dispositivos ligados ao mesmo. Estatísticas de destinada ao utilizador são importantes porque podem ajudar a ver de que está a suceder com problemas de causa raiz seu IoT hub e ajuda sem ser necessário contactar o suporte do Azure.

As métricas são ativadas por predefinição. Pode ver as métricas do IoT Hub do portal do Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Como ver as métricas do IoT Hub
1. Crie um hub IoT. Pode encontrar instruções sobre como criar um hub IoT a [começar] [ lnk-get-started] guia.
2. Abra o painel do seu IoT hub. A partir daí, clique em **métricas**.
   
    ![][1]
3. No painel métricas, pode ver as métricas para o seu hub IoT e criar vistas personalizadas de métricas. Pode optar por enviar os dados de métricas para um ponto de final do Event Hubs ou uma conta de armazenamento do Azure, clicando em **as definições de diagnóstico**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métricas de IoT Hub e como utilizá-los
IoT Hub disponibiliza várias métricas para lhe dar uma descrição geral do Estado de funcionamento do seu hub e o número total de dispositivos ligados. Pode combinar informações a partir de várias métricas para uma imagem do Estado do IoT hub maior de pintura. A tabela seguinte descreve as métricas que controla cada IoT hub e como cada métrica se relaciona com o estado geral do IoT hub.

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|d2c.telemetry.Ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem tentada a enviar ao seu IoT hub|
|d2c.telemetry.Ingress.Success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito ao seu IoT hub|
|c2d.Commands.egress.Complete.Success|Comandos concluídos|Contagem|Total|Número de comandos da nuvem para o dispositivo foi concluída com êxito pelo dispositivo|
|c2d.Commands.egress.Abandon.Success|Comandos abandonados|Contagem|Total|Número de comandos da nuvem para o dispositivo abandonadas pelo dispositivo|
|c2d.Commands.egress.Reject.Success|Comandos rejeitados|Contagem|Total|Número de comandos da nuvem para o dispositivo rejeitado pelo dispositivo|
|devices.totalDevices|Total de dispositivos|Contagem|Total|Número de dispositivos registados ao seu IoT hub|
|devices.connectedDevices.allProtocol|Dispositivos ligados|Contagem|Total|Número de dispositivos ligados ao seu IoT hub|
|d2c.telemetry.egress.Success|Mensagens de telemetria entregues|Contagem|Total|Número de vezes que as mensagens foram escritas com êxito para os pontos finais (total)|
|d2c.telemetry.egress.dropped|Mensagens de ignorados|Contagem|Total|Número de mensagens removida porque estes não corresponde a qualquer rotas e foi desativada a rota de contingência|
|d2c.telemetry.egress.orphaned|Mensagens órfãos|Contagem|Total|O número de mensagens não corresponde a qualquer rotas, incluindo a rota de contingência|
|d2c.telemetry.egress.Invalid|Mensagens inválidas|Contagem|Total|A contagem de mensagens não entregues devido a incompatibilidade com o ponto final|
|d2c.telemetry.egress.fallback|Mensagens de correspondência de condição de contingência|Contagem|Total|Número de mensagens escritas para o ponto final de contingência|
|d2c.Endpoints.egress.eventHubs|Mensagens entregues nos pontos finais de Hub de eventos|Contagem|Total|Número de vezes que as mensagens foram escritas com êxito aos pontos finais de Hub de eventos|
|d2c.Endpoints.latency.eventHubs|Latência de mensagem para pontos finais de Hub de eventos|Milissegundos|Média|A latência média entre a entrada de mensagem para o IoT hub e a entrada de mensagem para um ponto final de Hub de eventos, em milissegundos|
|d2c.Endpoints.egress.serviceBusQueues|Mensagens entregues nos pontos finais de fila do Service Bus|Contagem|Total|Número de vezes que as mensagens foram escritas com êxito aos pontos finais de fila do Service Bus|
|d2c.Endpoints.latency.serviceBusQueues|Latência de mensagem para pontos finais de fila do Service Bus|Milissegundos|Média|A latência média entre a entrada de mensagem para o IoT hub e a entrada de mensagem para um ponto final de fila do Service Bus, em milissegundos|
|d2c.Endpoints.egress.serviceBusTopics|Mensagens entregues nos pontos finais de tópico de barramento de serviço|Contagem|Total|Número de vezes que as mensagens foram escritas com êxito aos pontos finais de tópico de barramento de serviço|
|d2c.Endpoints.latency.serviceBusTopics|Latência de mensagem para pontos finais de tópico de barramento de serviço|Milissegundos|Média|A latência média entre a entrada de mensagem para o IoT hub e a entrada de mensagem para um ponto final de tópico de barramento de serviço, em milissegundos|
|d2c.Endpoints.egress.builtIn.Events|Mensagens entregues para o ponto final incorporado (mensagens/eventos)|Contagem|Total|Número de vezes que as mensagens foram escritas com êxito para o ponto final incorporado (mensagens/eventos)|
|d2c.Endpoints.latency.builtIn.Events|Latência de mensagem para o ponto final incorporado (mensagens/eventos)|Milissegundos|Média|A latência média entre a entrada de mensagem para o IoT hub e a entrada de mensagem para o ponto final incorporado (mensagens/eventos), em milissegundos |
|d2c.Twin.Read.Success|Leituras de duplo bem-sucedida dos dispositivos|Contagem|Total|A contagem de leituras de iniciadas por dispositivo duplo todos os com êxito.|
|d2c.Twin.Read.Failure|Não foi possível duplo leituras dos dispositivos|Contagem|Total|A contagem de todas as falha leituras iniciadas por dispositivo duplo.|
|d2c.Twin.Read.size|Tamanho da resposta de duplo lê a partir de dispositivos|Bytes|Média|A média, mínimo e máximo de todos os com êxito iniciadas por dispositivo duplo leituras.|
|d2c.Twin.Update.Success|Atualizações de duplo com êxito a partir de dispositivos|Contagem|Total|A contagem das atualizações de iniciadas por dispositivo duplo todos os com êxito.|
|d2c.Twin.Update.Failure|Falha de atualizações de duplo a partir de dispositivos|Contagem|Total|A contagem de todas as falha atualizações iniciadas por dispositivo duplo.|
|d2c.Twin.Update.size|Tamanho das atualizações de duplo a partir de dispositivos|Bytes|Média|A média, min e o tamanho máximo de todos os com êxito iniciadas por dispositivo duplo atualizações.|
|c2d.methods.Success|Invocações de método direto com êxito|Contagem|Total|A contagem de chamadas de método direto todos os com êxito.|
|c2d.methods.Failure|Não foi possível invocações de método direto|Contagem|Total|A contagem de todas as falha chamadas de método direto.|
|c2d.methods.requestSize|Tamanho do pedido de invocações de método direto|Bytes|Média|A média, mínimo e máximo de todos os com êxito direcionam os pedidos de método.|
|c2d.methods.responseSize|Tamanho da resposta de invocações de método direto|Bytes|Média|A média, mínimo e máximo de respostas de método direto todos os com êxito.|
|c2d.Twin.Read.Success|Leituras de duplo com êxito de back-end|Contagem|Total|A contagem de leituras de back-end-iniciada duplo todos os com êxito.|
|c2d.Twin.Read.Failure|Leituras de duplo falhada de back-end|Contagem|Total|A contagem de todas as falha leituras duplo iniciada em back-end.|
|c2d.Twin.Read.size|Tamanho da resposta de duplo lê a partir do back-end|Bytes|Média|O média, mínimo e máximo de todos os com êxito iniciada em back-end duplo leituras.|
|c2d.Twin.Update.Success|Atualizações de duplo com êxito a partir de back-end|Contagem|Total|A contagem das atualizações de back-end-iniciada duplo todos os com êxito.|
|c2d.Twin.Update.Failure|Atualizações de duplo falhada a partir de back-end|Contagem|Total|A contagem de todas as falha atualizações duplo iniciada em back-end.|
|c2d.Twin.Update.size|Tamanho de atualizações de duplo do back-end|Bytes|Média|A média, min e o tamanho máximo de todos os com êxito iniciada em back-end duplo atualizações.|
|twinQueries.success|Consultas de duplo com êxito|Contagem|Total|A contagem de todas as consultas de duplo com êxito.|
|twinQueries.failure|Consultas de duplo falhada|Contagem|Total|A contagem de todas as consultas de duplo falhada.|
|twinQueries.resultSize|Tamanho dos resultados consultas duplo|Bytes|Média|A média, mínimo e máximo, o tamanho dos resultados de todas as consultas de duplo com êxito.|
|jobs.createTwinUpdateJob.success|Criações com êxito das tarefas de atualização de duplo|Contagem|Total|A contagem de todos os criação com êxito das tarefas de atualização de duplo.|
|jobs.createTwinUpdateJob.failure|Falha de criações de tarefas de atualização de duplo|Contagem|Total|A contagem de todos os falha na criação de tarefas de atualização de duplo.|
|jobs.createDirectMethodJob.success|Criações com êxito das tarefas de invocação do método|Contagem|Total|A contagem de todos os criação com êxito das tarefas de invocação do método direto.|
|jobs.createDirectMethodJob.failure|Falha de criações de tarefas de invocação do método|Contagem|Total|A contagem de todos os falha na criação de tarefas de invocação do método direto.|
|jobs.listJobs.success|Chamadas bem-sucedidas para tarefas de lista|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para tarefas de lista.|
|jobs.listJobs.failure|Falha de chamadas para tarefas de lista|Contagem|Total|A contagem de todas as chamadas de falha para tarefas de lista.|
|jobs.cancelJob.success|Cancelamentos de tarefas com êxito|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para cancelar uma tarefa.|
|jobs.cancelJob.failure|Cancelamentos de tarefas com falhas|Contagem|Total|A contagem de todas as chamadas de falha para cancelar uma tarefa.|
|jobs.queryJobs.success|Consultas de tarefa com êxito|Contagem|Total|A contagem de todas as chamadas bem-sucedidas para tarefas de consulta.|
|jobs.queryJobs.failure|Falha na tarefa consulta|Contagem|Total|A contagem de todas as chamadas de falha para tarefas de consulta.|
|Jobs.completed|Tarefas concluídas|Contagem|Total|A contagem de todas as tarefas de conclusão.|
|Jobs.Failed|Tarefas falhadas|Contagem|Total|A contagem de todas as tarefas falhadas.|

## <a name="next-steps"></a>Passos seguintes
Agora que viu uma descrição geral das métricas do IoT Hub, siga esta ligação para obter mais informações sobre a gestão do Azure IoT Hub:

* [Operações de monitorização][lnk-monitor]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
