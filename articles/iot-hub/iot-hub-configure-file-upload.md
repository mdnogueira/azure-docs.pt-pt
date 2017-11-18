---
title: Utilizar o portal do Azure para configurar o carregamento de ficheiros | Microsoft Docs
description: "Como utilizar o portal do Azure para configurar o seu IoT hub para ativar os carregamentos de ficheiros de dispositivos ligados. Inclui informações sobre como configurar o conta de armazenamento do Azure de destino."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: 2d875947297be5d47362369b96bc6ab0d90b3c93
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurar o IoT Hub carregamentos de ficheiros no portal do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Carregamento de ficheiros

Para utilizar o [funcionalidade de carregamento de ficheiros no IoT Hub][lnk-upload], primeiro tem de associar uma conta de armazenamento do Azure com o seu hub. Selecione **carregamento de ficheiros** para apresentar uma lista de propriedades de carregamento de ficheiros para o IoT hub que está a ser modificado.

![Ficheiro de IoT Hub da vista carregar definições no portal][13]

**Contentor de armazenamento**: utilizar o portal do Azure para selecionar um contentor do blob numa conta de armazenamento do Azure na sua subscrição do Azure atual para associar o seu IoT Hub. Se necessário, pode criar uma conta de armazenamento do Azure no **contas do Storage** painel e BLOBs de contentor no **contentores** painel. IoT Hub gera automaticamente SAS URIs com permissões de escrita para este contentor de BLOBs para os dispositivos a utilizar ao carregam estes ficheiros.

![Ver os contentores de armazenamento para o carregamento de ficheiros no portal][14]

**Receber notificações para os ficheiros carregados**: Ativar ou desativar notificações de carregamento de ficheiros através do botão de alternar.

**TTL de SAS**: esta definição é o tempo-to-live do URI de SAS devolvido para o dispositivo ao IoT Hub. Definido como uma hora, por predefinição, mas pode ser personalizado para outros valores utilizando o controlo de deslize.

**Notificação de definições predefinidas TTL ficheiros**: O time-to-live de um ficheiro a carregar notificação antes de expirar. Definido como um dia, por predefinição, mas pode ser personalizado para outros valores utilizando o controlo de deslize.

**Contagem máxima de entrega de notificação de ficheiros**: O número de vezes que o IoT Hub tenta fornecer um ficheiro de notificação de carregar. Definido para 10 por predefinição, mas pode ser personalizado para outros valores utilizando o controlo de deslize.

![Configurar o carregamento de ficheiros do IoT Hub no portal][15]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as capacidades de carregamento do ficheiro do IoT Hub, consulte [carregar ficheiros a partir de um dispositivo] [ lnk-upload] no guia de programadores do IoT Hub.

Siga estas ligações para saber mais sobre a gestão do Azure IoT Hub:

* [Em massa gerir dispositivos de IoT][lnk-bulk]
* [Métricas de IoT Hub][lnk-metrics]
* [Operações de monitorização][lnk-monitor]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]
* [Proteger a sua solução de IoT a partir do zero cópias de segurança][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
