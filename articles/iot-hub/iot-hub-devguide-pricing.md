---
title: "Compreender o preço do IoT Hub do Azure | Microsoft Docs"
description: "Guia para programadores - informações sobre como medição e preços funciona com o IoT Hub, incluindo trabalhado exemplos."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: elioda
ms.openlocfilehash: 05006a78cc7d82bc048ec5706465f7140eb40e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-pricing-information"></a>Obter informações sobre preços IoT Hub do Azure

[Preços do IoT Hub do Azure] [ lnk-pricing] fornece as informações gerais sobre SKUs diferentes e preços para o IoT Hub. Este artigo contém detalhes adicionais sobre como as várias funcionalidades do IoT Hub são limitadas como mensagens pelo IoT Hub.

## <a name="charges-per-operation"></a>Encargos por operação

| Operação | Informações de faturação | 
| --------- | ------------------- |
| Operações de registo de identidade <br/> (criar, obter, listar, atualizar, eliminar) | Não é cobrado. |
| Mensagens do dispositivo para a cloud | As mensagens enviadas com êxito são-lhe cobradas em segmentos de 4 KB na entrada para o IoT Hub, por exemplo que uma mensagem de 6 KB é cobrada 2 mensagens. |
| Mensagens da nuvem para dispositivo | As mensagens enviadas com êxito são-lhe cobradas em segmentos de 4 KB, por exemplo uma mensagem de 6 KB é cobrada 2 mensagens. |
| Carregamentos de ficheiros | Transferência de ficheiros para o Storage do Azure não está limitada pelo IoT Hub. Mensagens de início e de conclusão de transferência do ficheiro são cobradas, tal como messaged medido em incrementos de 4 KB. Por exemplo, a transferência de um ficheiro de 10 MB é cobrada duas mensagens, para além do custo de armazenamento do Azure. |
| Métodos diretos | Pedidos de método com êxito, são-lhe cobrados em segmentos de 4 KB, respostas com corpos de vazios são-lhe cobradas em 4 KB como mensagens adicionais. Pedidos para dispositivos desligados são-lhe cobrados como mensagens de segmentos de 4 KB. Por exemplo, um método com um corpo de 6 KB que resulta numa resposta com nenhum corpo do dispositivo, é cobrado como duas mensagens; um método com um corpo de 6 KB que resulta numa resposta 1 KB do dispositivo é cobrado como duas mensagens para o pedido plus outra mensagem de resposta. |
| Leituras de dispositivo duplo | Dispositivo duplo lê a partir do dispositivo e a solução de back-end são-lhe cobrados como mensagens de segmentos de 512 bytes. Por exemplo, a leitura de um dispositivo de 6 KB duplo é cobrado como 12 mensagens. |
| Atualizações ao dispositivo duplo (etiquetas e propriedades) | Atualizações ao dispositivo duplo do dispositivo e o solução de back-end são-lhe cobradas como mensagens de segmentos de 512 bytes. Por exemplo, a leitura de um dispositivo de 6 KB duplo é cobrado como 12 mensagens. |
| Consultas do dispositivo duplo | As consultas são-lhe cobradas como mensagens, consoante o tamanho dos resultados em segmentos de 512 bytes. |
| Operações de tarefas <br/> (criar, atualizar, listar, eliminar) | Não é cobrado. |
| Operações por dispositivo de tarefas | Operações de tarefas (tais como atualizações ao dispositivo duplo e métodos) são-lhe cobradas como normal. Por exemplo, uma tarefa, resultando em chamadas de método de 1000 com pedidos de 1 KB e respostas de corpo vazio é cobrada 1000 mensagens. |

> [!NOTE]
> Todos os tamanhos de são calculados considerar o tamanho do payload em bytes (pacotes de protocolo é ignorada). Em caso de mensagens (que têm propriedades e corpo) o tamanho é calculado de forma desconhecidas do protocolo, conforme descrito no [mensagens guia para programadores do IoT Hub][lnk-message-size].

## <a name="example-1"></a>Exemplo #1

Um dispositivo envia uma mensagem de dispositivo para a nuvem de 1 KB por minuto ao IoT Hub, o que é, em seguida, lidos pelo Azure Stream Analytics. O solução de back-end invoca um método (com o payload de 512 bytes) no dispositivo cada dez minutos para acionar uma ação específica. O dispositivo responde ao método com um resultado de 200 bytes.

O dispositivo consome 1 mensagem * 60 minutos * 24 horas = 1440 mensagens por dia para as mensagens do dispositivo para a nuvem e 2 pedido e resposta * 6 vezes por hora * 24 horas = 288 mensagens para os métodos, para um total de mensagens de 1728 por dia.

## <a name="example-2"></a>Exemplo #2

Um dispositivo envia uma mensagem de dispositivo para a nuvem de 100-KB a cada hora. Também atualiza o dispositivo duplo com payloads de 1 KB a cada 4 horas. A solução de back-end, uma vez por dia, lê o 14 KB dispositivo duplo e atualiza-o com payloads de 512 bytes para alterar as configurações.

O dispositivo consome 25 mensagens de (100KB / 4KB) * 24 horas para mensagens do dispositivo para nuvem plus 2 mensagens (de 1KB/0,5 KB) * 6 horas por dia para atualizações ao dispositivo duplo, para um total de 612 mensagens por dia.
O solução de back-end consome 28 mensagens (KB de 14/0,5 KB) para ler o dispositivo duplo, mais 1 mensagem para a atualização, para um total de 29 de mensagens.

No total, o dispositivo e o solução de back-end consumam 641 mensagens por dia.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md
