---
title: Compreender os SDKs IoT do Azure | Microsoft Docs
description: "Guia para programadores - informações sobre e ligações para os vários Azure SDKs IoT do serviço e dispositivo que pode utilizar para criar aplicações de dispositivos e aplicações de back-end."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e299de0953cefac925b0015a15983d25d456576f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-use-azure-iot-sdks"></a>Compreender e utilizar os SDKs IoT do Azure

Existem três categorias de kits de desenvolvimento de software (SDKs) para trabalhar com o IoT Hub:

* **SDKs do dispositivo** permitem-lhe criar aplicações que são executados nos seus dispositivos de IoT. Estas aplicações enviar telemetria ao seu IoT hub e, opcionalmente, recebem mensagens do seu IoT hub.

* **Serviço SDKs** permitem-lhe gerir o seu IoT hub e, opcionalmente, enviar mensagens para os seus dispositivos de IoT.

* **Limite de IoT do Azure** permite-lhe criar gateways para dispositivos que não utilizem um dos protocolos suportados. Gateways também podem processar mensagens no limite.

SDKs são fornecidos para suportar várias linguagens de programação.

## <a name="azure-iot-device-sdks"></a>SDKs do Azure do dispositivo IoT

O dispositivo do Microsoft Azure IoT SDKs contenham código que facilita a criação de dispositivos e aplicações que ligam ao e são geridas pelos serviços do IoT Hub do Azure.

Os seguintes dispositivos IoT do Azure SDKs são disponíveis para transferência a partir do GitHub:

* [Dispositivos de IoT do Azure SDK para .NET][lnk-dotnet-device-sdk]
* [Dispositivos de IoT do Azure SDK para Java][lnk-java-device-sdk]
* [Dispositivos de IoT do Azure SDK para Node.js][lnk-node-device-sdk]
* [Dispositivos de IoT do Azure SDK para Python][lnk-python-device-sdk]
* [Dispositivos de IoT do Azure SDK para C] [ lnk-c-device-sdk] escritos em ANSI C (C99) para portabilidade e compatibilidade de plataforma abrangente. Existem duas bibliotecas de cliente de dispositivo para C, de baixo nível **iothub_client** e **serializador**.

> [!NOTE]
> Consulte os ficheiros Leia-me no repositórios do GitHub para obter informações sobre como utilizar o idioma e gestores de pacote específico da plataforma para instalar os binários e dependências no computador de desenvolvimento.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilidade de plataforma e o hardware do SO

Para obter mais informações sobre a compatibilidade de SDK com dispositivos de hardware específicos, consulte o [Azure certificadas para o catálogo de dispositivos de IoT][lnk-certified].

## <a name="azure-iot-service-sdks"></a>SDKs de serviço do IoT do Azure

Os SDKs do serviço IoT do Azure contêm código para facilitar a criar aplicações que interagir diretamente com o IoT Hub para gerir dispositivos e segurança.

O seguinte serviço do Azure IoT-SDKs se disponível para transferência a partir do GitHub:

* [Serviço de IoT do Azure SDK para .NET][lnk-dotnet-service-sdk]
* [Serviço de IoT do Azure SDK para Java][lnk-java-service-sdk]
* [Serviço de IoT do Azure SDK para Node.js][lnk-node-service-sdk]
* [Serviço de IoT do Azure SDK para Python][lnk-python-service-sdk]
* [Serviço de IoT do Azure SDK para C][lnk-c-service-sdk]

> [!NOTE]
> Consulte os ficheiros Leia-me no repositórios do GitHub para obter informações sobre como utilizar o idioma e gestores de pacote específico da plataforma para instalar os binários e dependências no computador de desenvolvimento.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Limite de IoT do Azure contém a infraestrutura e os módulos para criar soluções de gateway de IoT. Pode expandir o limite de IoT para criar gateways adaptados para qualquer cenário de ponto a ponto.

Pode transferir [Azure IoT Edge] [ lnk-iot-edge] a partir do GitHub.

## <a name="online-api-reference-documentation"></a>Documentação de referência de API online

A lista seguinte contém ligações para documentação de referência de API online para o dispositivo, o serviço e bibliotecas de gateway do Azure IoT:

* [Internet das coisas (IoT) .NET][lnk-dotnet-ref]
* [Dispositivos de IoT do Azure SDK para Java][lnk-java-ref]
* [Serviço de IoT do Azure SDK para Java][lnk-java-service-ref]
* [Dispositivos de IoT do Azure SDK para Node.js][lnk-node-ref]
* [Serviço de IoT do Azure SDK para Node.js][lnk-node-service-ref]
* [Dispositivos de IoT do Azure SDK para C][lnk-c-ref]
* [O IoT Hub REST][lnk-rest-ref]
* [Limite de IoT do Azure][lnk-gateway-ref]

## <a name="next-steps"></a>Passos seguintes

Outros tópicos de referência neste guia para programadores do IoT Hub incluem:

* [Pontos finais de IoT Hub][lnk-devguide-endpoints]
* [Idioma de consulta do IoT Hub para dispositivos duplos, tarefas e o encaminhamento de mensagens][lnk-devguide-query]
* [Quotas e limitação][lnk-devguide-quotas]
* [Suporte de MQTT do IoT Hub][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
