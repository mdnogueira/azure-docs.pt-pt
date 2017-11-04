---
title: Compreender as tarefas do IoT Hub do Azure | Microsoft Docs
description: "Guia para programadores - agendamento de trabalhos para executar em vários dispositivos ligados ao seu IoT hub. As tarefas podem atualizar as etiquetas e propriedades pretendidas e invocar métodos diretos em vários dispositivos."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: juanpere
ms.openlocfilehash: f90ecb70ad12ed05d5d40f8b26a0a4e461c9f835
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="schedule-jobs-on-multiple-devices"></a>Programar tarefas em vários dispositivos

IoT Hub do Azure permite que um número de blocos modulares, como [etiquetas e propriedades do dispositivo duplo] [ lnk-twin-devguide] e [direcionar métodos][lnk-dev-methods].  Normalmente, as aplicações de back-end ativam operadores e administradores de dispositivos atualizar e interagir com dispositivos de IoT em massa e a uma hora agendada.  As tarefas executar atualizações ao dispositivo duplo e métodos diretos face a um conjunto de dispositivos a uma hora agendada.  Por exemplo, um operador teria de utilizar uma aplicação de back-end que inicia e regista uma tarefa para reiniciar um conjunto de dispositivos na compilação 43 e piso 3 cada vez que não pretende ser incómoda para as operações de criar.

Considere a utilização de tarefas quando necessitar de agendar e controlar o progresso qualquer uma das seguintes atividades num conjunto de dispositivos:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

## <a name="job-lifecycle"></a>Ciclo de vida da tarefa
As tarefas são iniciadas pela solução de back-end e mantidas pelo IoT Hub.  Pode iniciar uma tarefa através de um URI de serviço com acesso à (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) e a consulta para progresso uma tarefa em execução através de um URI de serviço com acesso à (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`). Para atualizar o estado de tarefas em execução depois de uma tarefa é iniciada, execute uma consulta da tarefa.

> [!NOTE]
> Quando inicia uma tarefa, valores e nomes de propriedade podem apenas conter imprimível US-ASCII alfanuméricos, exceto qualquer no conjunto de seguinte: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`.

## <a name="jobs-to-execute-direct-methods"></a>Tarefas a executar métodos diretos
O fragmento seguinte mostra os detalhes do pedido de HTTPS 1.1 para executar um [método direto] [ lnk-dev-methods] num conjunto de dispositivos através de uma tarefa:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

A condição de consulta também pode ser um ID de dispositivo único ou uma lista de dispositivos IDs conforme mostrado nos exemplos seguintes:

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[Idioma de consulta do IoT Hub] [ lnk-query] abrange o idioma de consulta do IoT Hub no detalhes adicionais.

## <a name="jobs-to-update-device-twin-properties"></a>Tarefas para atualizar as propriedades do dispositivo duplo
O fragmento seguinte mostra os detalhes do pedido HTTPS 1.1 para propriedades do dispositivo duplo utilizando uma tarefa de atualização:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>Consultar o progresso das tarefas de
O fragmento seguinte mostra os detalhes do pedido de HTTPS 1.1 para [consultar tarefas][lnk-query]:

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

É fornecido o continuationToken da resposta.  

## <a name="jobs-properties"></a>Propriedades de tarefas
A lista seguinte mostra as propriedades e descrições correspondentes, que podem ser utilizadas ao consultar as tarefas ou resultados das tarefas.

| Propriedade | Descrição |
| --- | --- |
| **jobId** |Aplicação fornecido ID da tarefa. |
| **startTime** |Aplicação fornecido a hora de início (ISO-8601) para a tarefa. |
| **endTime** |IoT Hub fornecido data (ISO-8601) para quando a tarefa foi concluída. Válido apenas depois da tarefa de atinge o estado 'Concluído'. |
| **tipo** |Tipos de tarefas: |
| | **scheduledUpdateTwin**: uma tarefa utilizada para atualizar um conjunto de propriedades pretendidas ou etiquetas. |
| | **scheduledDeviceMethod**: uma tarefa utilizada para invocar um método de dispositivo num conjunto de dispositivos duplos. |
| **Estado** |Estado atual da tarefa. Valores possíveis para o estado: |
| | **pendente**: agendadas e a aguardar para ser selecionadas pelo serviço de tarefa. |
| | **agendada**: agendada para uma hora no futuro. |
| | **executar**: tarefa atualmente ativa. |
| | **Cancelado**: tarefa foi cancelada. |
| | **Não foi possível**: Falha na tarefa. |
| | **concluir**: tarefa foi concluída. |
| **deviceJobStatistics** |Estatísticas sobre a execução da tarefa. |
| | **deviceJobStatistics** propriedades: |
| | **deviceJobStatistics.deviceCount**: número de dispositivos na tarefa. |
| | **deviceJobStatistics.failedCount**: número de dispositivos em que a tarefa falhou. |
| | **deviceJobStatistics.succeededCount**: número de dispositivos em que a tarefa foi concluída com êxito. |
| | **deviceJobStatistics.runningCount**: número de dispositivos que atualmente estão a ser executada a tarefa. |
| | **deviceJobStatistics.pendingCount**: número de dispositivos que estão pendentes para executar a tarefa. |

### <a name="additional-reference-material"></a>Material de referência adicionais
Outros tópicos de referência no guia de programadores do IoT Hub incluem:

* [Pontos finais de IoT Hub] [ lnk-endpoints] descreve os vários pontos finais que cada IoT hub expõe para operações de gestão e de tempo de execução.
* [Limitação e quotas] [ lnk-quotas] descreve as quotas que se aplicam para o serviço de IoT Hub e o comportamento de limitação pode esperar quando utilizar o serviço.
* [Azure SDKs IoT do serviço e dispositivo] [ lnk-sdks] indica o idioma de vários SDKs que pode utilizar ao desenvolver aplicações de serviço e dispositivo que interagem com o IoT Hub.
* [Idioma de consulta do IoT Hub para dispositivos duplos, tarefas e o encaminhamento de mensagens] [ lnk-query] descreve o idioma de consulta do IoT Hub pode utilizar para obter informações a partir do IoT Hub sobre os dispositivos duplos e tarefas.
* [Suporte do IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes
Se pretender experimentar alguns dos conceitos descritos neste artigo, poderá estar interessado no tutorial seguinte do IoT Hub:

* [Tarefas de agenda e difusão][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
