---
title: "Opções de nuvem para o dispositivo IoT Hub do Azure | Microsoft Docs"
description: "Guia para programadores - orientações sobre quando utilizar métodos diretos, dispositivo duplo propriedades pretendidas ou mensagens da nuvem para o dispositivo para comunicações de nuvem para o dispositivo."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: elioda
ms.openlocfilehash: c0f9d0e13cb159188bdaf2b915c1bf6de73be855
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-to-device-communications-guidance"></a>Orientações de comunicações da nuvem para o dispositivo
IoT Hub fornece três opções para aplicações de dispositivos para expor a funcionalidade para uma aplicação de back-end:

* [Direcionar métodos] [ lnk-methods] para comunicações que necessitam de confirmação imediata do resultado. Métodos diretos são frequentemente utilizados para controlo interactivo dos dispositivos como ativar uma ventoinha.
* [Duplo do pretendido propriedades] [ lnk-twins] para comandos de longa execução que se destina a colocar o dispositivo para uma determinada pretendido estado. Por exemplo, defina o intervalo de envio de telemetria e 30 minutos.
* [Mensagens da nuvem para dispositivo] [ lnk-c2d] para unidirecionais notificações para a aplicação de dispositivo.

Eis uma comparação detalhada das várias opções de comunicação de nuvem para o dispositivo.

|  | Métodos diretos | Propriedades pretendidas do duplo | Mensagens da nuvem para dispositivo |
| ---- | ------- | ---------- | ---- |
| Cenário | Comando que precise de confirmação imediata, como ativar uma ventoinha. | Comandos de longa execução que se destina a colocar o dispositivo em que um determinado estado pretendido. Por exemplo, defina o intervalo de envio de telemetria e 30 minutos. | Unidirecionais notificações para a aplicação de dispositivo. |
| Fluxo de dados | Bidirecional. A aplicação de dispositivo pode responder para o método de imediato. O solução de back-end recebe o resultado reconhecimento do contexto para o pedido. | Unidirecional. A aplicação de dispositivo recebe uma notificação com a alteração de propriedade. | Unidirecional. A aplicação de dispositivo recebe a mensagem
| Durabilidade | Não são contactados dispositivos desligados. O solução de back-end é notificado de que o dispositivo não está ligado. | Os valores de propriedade são mantidos no dispositivo duplo. Dispositivo irão lê-lo no seguinte restabelecimento de ligação. Valores de propriedade são recuperável com o [idioma de consulta do IoT Hub][lnk-query]. | As mensagens podem ser mantidas pelo IoT Hub para até 48 horas. |
| Destinos | Utilização de único dispositivo **deviceId**, ou em vários dispositivos com [tarefas][lnk-jobs]. | Utilização de único dispositivo **deviceId**, ou em vários dispositivos com [tarefas][lnk-jobs]. | Único dispositivo por **deviceId**. |
| Tamanho | Até 8 KB pedidos e respostas de 8 KB. | Máximo pretendido tamanho de propriedades é de 8 KB. | Até 64 KB mensagens. |
| Frequência | Elevada. Para obter mais informações, consulte [limita do IoT Hub][lnk-quotas]. | Média. Para obter mais informações, consulte [limita do IoT Hub][lnk-quotas]. | Baixa. Para obter mais informações, consulte [limita do IoT Hub][lnk-quotas]. |
| Protocolo | Disponível através de MQTT ou AMQP. | Disponível através de MQTT ou AMQP. | Disponível em todos os protocolos. Dispositivo tem de inquirir quando através de HTTPS. |

Saiba como utilizar métodos diretos, propriedades pretendidas e mensagens da nuvem para dispositivo nos seguintes tutoriais:

* [Utilize métodos diretos][lnk-methods-tutorial], para métodos diretos;
* [Utilize as propriedades do pretendido para configurar dispositivos][lnk-twin-properties], para o dispositivo duplo do pretendido propriedades; 
* [Enviar mensagens da nuvem para dispositivo][lnk-c2d-tutorial], mensagens da nuvem para o dispositivo.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
