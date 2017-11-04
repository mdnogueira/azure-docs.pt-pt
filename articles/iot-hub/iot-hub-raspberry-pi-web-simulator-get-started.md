---
title: "Simulated Raspberry Pi nuvem (Node.js) - simulador de web Raspberry Pi estabelecer ligação ao IoT Hub do Azure | Microsoft Docs"
description: Ligar o simulador de web Raspberry Pi ao IoT Hub do Azure para Raspberry Pi enviar dados para a nuvem do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: simulador raspberry pi, do azure iot raspberry pi, iothub raspberry pi, dados de envio de raspberry pi na nuvem, raspberry pi na nuvem
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/28/2017
ms.author: xshi
ms.openlocfilehash: 3b80bf35d6af91d5bdb196d97668dc0f837b92cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Ligar o simulador online Raspberry Pi ao IoT Hub do Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, comece por aprender as noções básicas do trabalho com o simulador online Raspberry Pi. Em seguida, aprenderá a forma totalmente integrada ligar o simulador de instalador de plataforma para a nuvem, utilizando o [IoT Hub do Azure](iot-hub-what-is-iot-hub.md). 

Se tiver dispositivos físicos, visite [ligar Raspberry Pi ao IoT Hub do Azure](iot-hub-raspberry-pi-kit-node-get-started.md) para começar a utilizar. 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3_banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6_button_default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5_button_click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6_button_default.png';">
</div>

## <a name="what-you-do"></a>O que fazer

* Aprender as noções básicas do simulador online Raspberry Pi.
* Crie um hub IoT.
* Registe um dispositivo para Pi no seu IoT hub.
* Execute um exemplo de aplicação no instalador de plataforma para enviar dados de sensores simulados ao seu IoT hub.

Ligar simulada Raspberry Pi a um IoT hub que criou. Em seguida, execute um exemplo de aplicação com o simulador para gerar dados de sensores. Por fim, enviar os dados de sensor ao seu IoT hub.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT do Azure e obter a cadeia de ligação do novo dispositivo. Se não tiver uma conta do Azure, [criar uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Como trabalhar com simulador online Raspberry Pi.
* Como enviar dados de sensores ao seu IoT hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Descrição geral do simulador de web Raspberry Pi

Clique no botão para iniciar o simulador online Raspberry Pi.

> [!div class="button"]
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Iniciar o simulador Raspberry Pi</a>

Existem três áreas no simulador do web.
1. Área de assemblagem - o circuito predefinido é que um Pi estabelece ligação com um sensor BME280 e um LED. A área está bloqueada na versão de pré-visualização, por isso, atualmente não é possível efetuar a personalização.
2. Codificação área - um editor de código online por si para código com Raspberry Pi. A aplicação de exemplo predefinida ajuda-o para recolher dados de sensores do BME280 sensor e envia ao seu IoT Hub do Azure. A aplicação é totalmente compatível com dispositivos de Pi reais. 
3. Janela de consola integrada - mostra a saída do seu código. Na parte superior desta janela, existem três botões.
   * **Executar** -executar a aplicação na área de codificação.
   * **Repor** -repor a área de codificação à aplicação de exemplo de predefinição.
   * **Expandir/subconjuntos de validação** -no lado direito, há um botão de subconjuntos de validação/expandir a janela de consola.

> [!NOTE] 
O simulador de web Raspberry Pi está agora disponível numa versão de pré-visualização. Gostaríamos de ouvir os seus voz no [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). O código de origem é público no [Github](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Descrição geral do simulador online Pi](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>Execute um exemplo de aplicação no simulador do instalador de plataforma web

1. Na área de codificação, certifique-se de que está a trabalhar na aplicação de amostra de predefinição. Substitua o marcador de posição na linha 15 a cadeia de ligação do dispositivo do Azure IoT hub.
   ![Substitua a cadeia de ligação do dispositivo](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Clique em **executar** ou tipo `npm start` para executar a aplicação.


Deverá ver o seguinte resultado que mostra os dados de sensor e as mensagens que são enviadas para o seu IoT hub ![saída - dados de sensor enviados do Raspberry Pi ao seu IoT hub](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>Passos seguintes

Tiver de executar um exemplo de aplicação para recolher dados de sensores e enviá-lo ao seu IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
