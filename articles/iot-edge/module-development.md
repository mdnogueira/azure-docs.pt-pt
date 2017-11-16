---
title: "Desenvolver módulos para limite de IoT do Azure | Microsoft Docs"
description: "Saiba como criar módulos personalizados para o limite de IoT do Azure"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95b1d5d4e5e11f96b6abb17f0aeba935cc65512d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules---preview"></a>Compreender os requisitos e ferramentas para o desenvolvimento de módulos de limite de IoT – pré-visualização

Este artigo explica que funcionalidades estão disponíveis ao escrever as aplicações que são executados como o módulo de IoT Edge e como tirar partido dos mesmos.

## <a name="iot-edge-runtime-environment"></a>Ambiente de tempo de execução de limite de IoT
O tempo de execução do limite de IoT fornece a infraestrutura para integrar a funcionalidade de vários módulos de limite de IoT e implementá-las em dispositivos de limite de IoT. Um nível elevado, qualquer programa pode ser compactado como um módulo de limite de IoT. No entanto, para tirar partido do limite de IoT comunicação funções e funcionalidades de gestão, um programa em execução no módulo pode estabelecer ligação ao hub IoT Edge local, integrado no tempo de execução de limite de IoT.

## <a name="using-the-iot-edge-hub"></a>Utilizar o hub IoT Edge
O hub IoT Edge fornece duas funcionalidades principais: proxy para o IoT Hub e comunicações locais.

### <a name="iot-hub-primitives"></a>Primitivos do IoT Hub
IoT Hub vê um módulo instância analogously num dispositivo, na medida em que as TI:

* tem um duplo de módulo, que é distinto e isolados do [dispositivo duplo] [ lnk-devicetwin] e outros duplos módulo desse dispositivo;
* Pode enviar [mensagens do dispositivo para nuvem][lnk-iothub-messaging];
* pode receber [direcionar métodos] [ lnk-methods] direcionada especificamente a respetiva identidade.

Atualmente, um módulo não é possível receber mensagens da nuvem para o dispositivo nem utilizar a funcionalidade de carregamento de ficheiros.

Quando escrever um módulo, pode simplesmente utilizar o [SDK de dispositivos do IoT do Azure] [ lnk-devicesdk] para estabelecer ligação ao hub IoT Edge e utilizar a funcionalidade acima, tal como faria para utilizar o IoT Hub com uma aplicação de dispositivo, o único diferença a ser que, da sua back-end de aplicação, tem de referir-se a identidade do módulo em vez da identidade do dispositivo.

Consulte [desenvolver e implementar um módulo de IoT Edge para um dispositivo simulado] [ lnk-tutorial2] para obter um exemplo de uma aplicação de módulo que envia mensagens do dispositivo para nuvem e utiliza o duplo de módulo.

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a cloud
Para poder ativar complexas de processamento de mensagens do dispositivo para a nuvem, hub IoT Edge fornece declarativa de encaminhamento de mensagens entre módulos e entre módulos e IoT Hub.
Isto permite que módulos de intercetar e processar mensagens enviadas por outros módulos e propagarem-los para pipelines complexas.
O artigo [composição do módulo] [ lnk-module-comp] explica como compor módulos para pipelines complexas com rotas.

Um módulo de limite de IoT diferente, uma aplicação de dispositivo IoT Hub normal, pode receber mensagens dispositivo-nuvem que estão a ser efetuadas pelo seu hub IoT Edge local, para processá-los.

Hub IoT Edge propaga as mensagens para o módulo com base nas rotas declarativas descritas a [composição do módulo] [ lnk-module-comp] artigo. Quando um módulo de limite de IoT a desenvolver, já pode receber estas mensagens, definindo os processadores de mensagens, conforme mostrado no tutorial [desenvolver e implementar um módulo de IoT Edge para um dispositivo simulado][lnk-tutorial2].

Para simplificar a criação de rotas, o limite de IoT adiciona o conceito de módulo *entrada* e *saída* pontos finais. Um módulo pode receber todas as mensagens do dispositivo para nuvem encaminhadas para a mesma sem especificar qualquer entrada e pode enviar mensagens do dispositivo para nuvem sem especificar qualquer saída.
Utilizar explícitas entradas e saídas, embora, faz com que as regras de encaminhamento mais simples de compreender. Consulte [composição do módulo] [ lnk-module-comp] para obter mais informações sobre regras de encaminhamento e pontos finais de entrada e de saída de módulos.

Por fim, mensagens dispositivo-nuvem processadas pelo Edge hub são carimbo de data / com as seguintes propriedades do sistema:

| Propriedade | Descrição |
| -------- | ----------- |
| $connectionDeviceId | O ID de dispositivo do cliente que enviou a mensagem |
| $connectionModuleId | O ID do módulo do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode estar vazio. |
| $outputName | A saída utilizada para enviar a mensagem. Pode estar vazio. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Ligar ao hub IoT limite a partir de um módulo
Ligar para o hub IoT Edge local a partir de um módulo envolve dois passos: utilizar a cadeia de ligação fornecida pelo tempo de execução do limite de IoT quando o módulo é iniciado e certifique-se a sua aplicação aceita o certificado apresentado pelo hub IoT Edge nesse dispositivo.

A cadeia de ligação a utilizar é injetada pelo tempo de execução de limite de IoT a variável de ambiente `EdgeHubConnectionString`. Isto torna disponível para qualquer programa que pretende utilizá-lo.

Analogously, o certificado a utilizar para validar a ligação do hub IoT Edge é injetado pelo runtime IoT Edge num ficheiro cujo caminho está disponível na variável de ambiente `EdgeModuleCACertificateFile`.

O tutorial [desenvolver e implementar um módulo de IoT Edge para um dispositivo simulado] [ lnk-tutorial2] mostra como Certifique-se de que o certificado no arquivo do computador na sua aplicação de módulo. Claramente, qualquer outro método para confiar ligações que funcionam de certificado a utilizar.

## <a name="packaging-as-an-image"></a>Empacotamento como uma imagem
Módulos de limite de IoT são reunidos as imagens de Docker.
Pode utilizar diretamente toolchain de Docker ou Visual Studio Code, conforme mostrado no tutorial [desenvolver e implementar um módulo de IoT Edge para um dispositivo simulado][lnk-tutorial2].

## <a name="next-steps"></a>Passos seguintes

Depois de desenvolver um módulo, saiba como [implementar e monitorizar os módulos de limite de IoT à escala][lnk-howto-deploy].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
