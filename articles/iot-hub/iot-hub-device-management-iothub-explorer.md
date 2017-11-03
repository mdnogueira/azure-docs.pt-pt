---
title: "Gestão de dispositivos do IoT do Azure com o Explorador de iothub | Microsoft Docs"
description: "Utilize a ferramenta CLI do iothub Explorador para gestão de dispositivos do IoT Hub do Azure, com os métodos diretos e as opções de gestão do duplo propriedades pretendido."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "gestão de dispositivos do hub iot do azure, gestão de dispositivos iot do Azure, a gestão de dispositivos do iot hub e iot de gestão de dispositivos"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.openlocfilehash: 5b7a5057bdfb5920fbb5759bed1f5561cfa1d7e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Utilize o Explorador de iothub para gestão de dispositivos do IoT Hub do Azure

![Diagrama de ponto a ponto](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Explorador de iothub](https://github.com/azure/iothub-explorer) é uma ferramenta CLI que executar no anfitrião de um computador para gerir identidades de dispositivo no seu registo de hub IoT. É fornecido com as opções de gestão que pode utilizar para efetuar várias tarefas.

| Opção de gestão          | Tarefa                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Métodos diretos             | Se um dispositivo agir como iniciar ou parar o envio de mensagens ou iniciar o dispositivo.                                        |
| Propriedades de duplo pretendida    | Colocar um dispositivo em determinados Estados, como a definição de um LED como verde ou definir o intervalo de envio de telemetria e 30 minutos.         |
| Duplo comunicadas propriedades   | Obter o estado de um dispositivo reportado. Por exemplo, o dispositivo comunica que o LED é blinking agora.                                    |
| Etiquetas de duplo                  | Armazenar metadados específicos do dispositivo na nuvem. Por exemplo, a localização de implementação de uma máquina de distribuidores automáticos.                         |
| Mensagens da nuvem para dispositivo   | Envie notificações para um dispositivo. Por exemplo, "é muito provável que rain hoje. Não se esqueça de colocar um umbrella."              |
| Consultas do dispositivo duplo        | Consulta todos os dispositivos duplos, para obter as condições arbitrários, como identificar os dispositivos que estão disponíveis para utilização. |

Para obter mais explicação sobre as diferenças e as orientações sobre como utilizar estas opções, consulte [orientações de comunicação do dispositivo para nuvem](iot-hub-devguide-d2c-guidance.md) e [orientações de comunicação de nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). IoT Hub mantém um dispositivo duplo para cada dispositivo que se liga ao mesmo. Para obter mais informações sobre dispositivos duplos, consulte [começar a utilizar dispositivos duplos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que irá aprender

Aprender a utilizar o Explorador do iothub com várias opções de gestão no computador de desenvolvimento.

## <a name="what-you-do"></a>O que fazer

Execute o Explorador de iothub com várias opções de gestão.

## <a name="what-you-need"></a>Do que precisa

- Tutorial [configurar o seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluir que inclui os seguintes requisitos:
  - Uma subscrição ativa do Azure.
  - Um hub IoT do Azure na sua subscrição.
  - Uma aplicação de cliente que envia mensagens para o seu hub IoT do Azure.
- Certifique-se de que o dispositivo está em execução com a aplicação de cliente durante este tutorial.
- Explorador de iothub, [instalar iothub explorer](https://github.com/azure/iothub-explorer) no computador de desenvolvimento.

## <a name="connect-to-your-iot-hub"></a>Ligar ao seu IoT hub

Ligar ao seu IoT hub, executando o seguinte comando:

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>Utilize o Explorador de iothub com métodos diretos

Invocar o `start` método na aplicação do dispositivo para enviar mensagens ao seu IoT hub, executando o seguinte comando:

```bash
iothub-explorer device-method <your device Id> start
```

Invocar o `stop` método na aplicação do dispositivo para parar de enviar mensagens ao seu IoT hub, executando o seguinte comando:

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>Utilize o Explorador de iothub com propriedades pretendida do duplo

Definir um intervalo de propriedade pretendido = 3000 executando o seguinte comando:

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

Esta propriedade pode ser lidos pelo seu dispositivo.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>Utilize o Explorador de iothub com propriedades comunicado do duplo

Obter as propriedades do dispositivo comunicadas executando o seguinte comando:

```bash
iothub-explorer get-twin <your device id>
```

Uma das propriedades é $metadata. $lastUpdated que mostra a hora da última este dispositivo envia ou recebe uma mensagem.

## <a name="use-iothub-explorer-with-twins-tags"></a>Utilize o Explorador de iothub com etiquetas do duplo

Apresente as propriedades do dispositivo e etiquetas, executando o seguinte comando:

```bash
iothub-explorer get-twin <your device id>
```

Adicionar uma função de campo = temperatura e humidade para o dispositivo, executando o seguinte comando:

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"

```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>Utilize o Explorador de iothub com mensagens da nuvem para dispositivo

Envie uma mensagem de "Olá mundo" para o dispositivo, executando o seguinte comando:

```bash
iothub-explorer send <device-id> "Hello World"
```

Consulte [utilizar o Explorador de iothub para enviar e receber mensagens entre o dispositivo e o IoT Hub](iot-hub-explorer-cloud-device-messaging.md) para um cenário real de utilização deste comando.

## <a name="use-iothub-explorer-with-device-twins-queries"></a>Utilize o Explorador de iothub com consultas de duplos do dispositivo

Consultar os dispositivos com uma etiqueta de função = 'temperatura e humidade' executando o seguinte comando:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consultar todos os dispositivos, exceto aqueles com uma etiqueta de função = 'temperatura e humidade' executando o seguinte comando:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Passos seguintes

Aprendeu como utilizar o Explorador do iothub com várias opções de gestão.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
