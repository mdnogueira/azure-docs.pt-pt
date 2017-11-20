---
title: "Soluções do Azure para a Internet das Coisas (IoT Edge) | Microsoft Docs"
description: "Descrição geral de uma amostra da arquitetura de solução IoT e como se relaciona com dispositivos, o serviço do Hub IoT do Azure, SDKs do dispositivo Azure IoT, SDKs de serviço do Azure IoT e outros serviços do Azure."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 587b733106d511ec63d71f67a06e520324a3e594
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Passos seguintes

O Azure IoT Edge é um serviço do Azure que permite o processamento de análises e dados na periferia. Com o IoT Edge, pode capacitar os seus dispositivos com código baseado em contentores que inclui lógica extraída diretamente dos serviços do Azure que já utiliza ou com o código específico da sua própria solução. Permite que os dispositivos:

* Ajam como dispositivos gateway, agregando e processando os dados de vários dispositivos de folha.
* Executem deteção de anomalias e reajam a alterações no ambiente, sem terem de aguardar por instruções da cloud.
* Minimizem os custos de largura de banda e armazenamento, ao pré-processar os dados e enviar os resultados. 

O IoT Edge também inclui uma interface na cloud que possibilidade a gestão remota de dispositivos. Sem ter de aceder fisicamente aos seus dispositivos, pode implementar código, monitorizar o estado e atualizá-lo. Pode gerir remotamente dispositivos individuais ou criar implementações que afetam grandes conjuntos de dispositivos que definir. Para obter mais informações, veja [Understand IoT Edge deployments for single devices or at scale][lnk-deployment] (Compreender as implementações do IoT Edge para dispositivos individuais ou em escala).

Para saber mais sobre os componentes que permitem o IoT Edge, veja [How Azure IoT Edge work7][lnk-overview] (Como funciona o Azure IoT Edge).

Se nunca utilizou o Hub IoT do Azure, poderá querer começar por ver a [Descrição geral do serviço Hub IoT do Azure][lnk-iot-hub].

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
