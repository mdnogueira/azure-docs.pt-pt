---
title: IoT Hub do Azure como | Microsoft Docs
description: "Como um programador, como utilizar as várias funcionalidades do IoT Hub?"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: b451ea2d42f0ff2f64746bcb296fb9128472fea8
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="how-to-use-azure-iot-hub"></a>Como utilizar o IoT Hub do Azure

Tem várias opções para aprender como desenvolver para o serviço de IoT Hub:

* Leia os artigos conceptuais que descrevem as funcionalidades do IoT Hub em detalhe.
* Siga um dos tutoriais que abrangem várias funcionalidades do IoT Hub.

## <a name="developer-guide"></a>Guia para programadores

Como um programador pode ler orientação conceptual detalhada sobre o IoT Hub no [guia para programadores][lnk-devguide]. Este guia inclui:

* Descrições detalhadas de todas as funcionalidades do IoT Hub para ajudar a saber como utilizá-los.
* Orientações sobre a escolha quando estão disponíveis várias opções.

## <a name="tutorials"></a>Tutoriais

Se preferir saber mais sobre funcionalidades específicas do IoT Hub ao trabalhar com exercícios práticos, existem várias tutoriais à sua escolha. Muitos destes tutoriais estão disponíveis em várias linguagens de programação. Estes tutoriais incluem:

- [Processar mensagens do dispositivo para a nuvem do IoT Hub utilizar rotas][lnk-routes-tutorial]. Este tutorial mostra como utilizar as regras de encaminhamento do IoT Hub para emitir mensagens do dispositivo para nuvem de uma forma fácil, baseadas na configuração.

- [Enviar mensagens da nuvem para o dispositivo com o IoT Hub][lnk-c2d-tutorial]. Este tutorial mostra como enviar mensagens da nuvem para o dispositivo através do IoT Hub e receber mensagens da nuvem para o dispositivo num dispositivo.

- [Carregar ficheiros a partir de dispositivos para a nuvem com o IoT Hub][lnk-upload-tutorial]. Este tutorial mostra como utilizar as capacidades de carregamento de ficheiros do IoT Hub.

- [Começar a utilizar dispositivos duplos][lnk-twin-tutorial]. Este tutorial apresenta-lhe dispositivos duplos, propriedades comunicadas, propriedades pretendidas e etiquetas. Utilizar dispositivos duplos para sincronizar os valores com os seus dispositivos.

- [Utilize métodos diretos][lnk-methods-tutorial]. Este tutorial mostra como utilizar métodos diretos. Adiciona um processador para um método direto no seu dispositivo simulado e invocar o método direto a partir do IoT Hub.

- [Introdução à gestão de dispositivos][lnk-dm-tutorial]. Este tutorial mostra como utilizar funcionalidades de gestão de chaves de dispositivo como duplos e métodos diretos. Utilize estas funcionalidades reiniciar remotamente o seu dispositivo simulado.

- [Utilize as propriedades do pretendido para configurar dispositivos][lnk-properties-tutorial]. Este tutorial mostra como para utilizar o dispositivo do duplo pretendida e reportado a propriedades, remotamente configurou o dispositivo.

- [Utilize tarefas de dispositivo para iniciar uma atualização de firmware do dispositivo][lnk-jobs-tutorial]. Este tutorial mostra como utilizar funcionalidades de gestão de chaves de dispositivo como duplos e métodos diretos. Saiba como utilizar estas funcionalidades para atualizar remotamente o firmware do seu dispositivo.

- [Tarefas de agenda e difusão][lnk-schedule-tutorial]. Este tutorial mostra como utilizar métodos diretos e propriedades pretendidas para interagir com vários dispositivos a uma hora agendada.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o serviço do IoT Hub, veja o [guia para programadores][lnk-devguide].

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md