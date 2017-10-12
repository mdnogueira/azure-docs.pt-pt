---
title: "Soluções Azure para Internet das Coisas (IoT Suite) | Microsoft Docs"
description: "Uma descrição geral do IoT no Azure, incluindo um exemplo de solução de arquitetura e a forma como se relaciona com o Azure IoT Suite e as soluções pré-configuradas."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 320190488bb4c7b8192421f9dd50a5264f558584
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Azure IoT Suite
O Microsoft Azure IoT Suite é uma solução de nível empresarial que lhe permite começar a utilizar rapidamente um conjunto de soluções pré-configuradas extensíveis. Estas soluções cobrem cenários de IoT comuns, como a [monitorização remota][lnk-preconfigured-solutions], a [manutenção preditiva][lnk-predictive-maintenance] e a [fábrica ligada][lnk-connected-factory]. Estas soluções são implementações da arquitetura da solução IoT descrita neste artigo.

As soluções pré-configuradas são soluções completas, funcionais e integrais que incluem:

- Dispositivos simulados para o ajudar a começar.
- Os serviços pré-configurados do Azure, como o [Azure IoT Hub][Azure IoT Hub], [Azure Event Hubs][Azure Event Hubs], [Azure Stream Analytics][Azure Stream Analytics], [Azure Machine Learning][Azure Machine Learning] e [armazenamento do Azure][Azure storage].
- Consolas de gestão específicas das soluções.

As soluções pré-configuradas contêm um código comprovado pronto para a produção que poderá personalizar e expandir para implementar os seus próprios cenários específicos do IoT.

Também poderá estar interessado no serviço [Azure IoT Hub][Azure IoT Hub] utilizado por muitas das soluções pré-configuradas. O [Azure IoT Hub][Azure IoT Hub] fornece comunicações bidirecionais seguras e fiáveis entre dispositivos e a cloud, utilizadas na arquitetura da solução pré-configurada.

## <a name="next-steps"></a>Passos seguintes
Explore estes recursos para saber mais sobre o IoT Suite e as soluções pré-configuradas:

* [O que é o Azure IoT Suite?][lnk-whatissuite]
* [Quais são as soluções pré-configuradas do Azure IoT Suite?][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md