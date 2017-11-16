---
title: Instalar o limite de IoT do Azure no IoT Core | Microsoft Docs
description: Instalar o runtime do Azure IoT Edge num dispositivo Windows IoT Core
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: be2a80645d23e709d6c5cfb3978498bbe85eca34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Instalar o runtime de limite de IoT no Windows IoT Core - pré-visualização

Pode executar o tempo de execução do limite de IoT do Azure, mesmo em dispositivos muito pequena do único quadro computador (SBC) que são muito prevalente atualmente da indústria de IoT. Este artigo explica o tempo de execução de aprovisionamento num [MinnowBoard Turbot] [ lnk-minnow] quadro de desenvolvimento com o Windows IoT Core.

## <a name="install-the-runtime"></a>Instalar o runtime

1. Instalar [Dashboard de núcleo do Windows 10 IoT] [ lnk-core] num sistema anfitrião.
1. Siga os passos no [configurar o seu dispositivo] [ lnk-board] para configurar o seu dashboard com a imagem de MinnowBoard Turbot/máx. de compilação 16299. 
1. Ativar no dispositivo, em seguida, [início de sessão remotamente com o PowerShell][lnk-powershell].
1. Na consola do PowerShell, [instalar os binários de Docker][lnk-docker-install].
1. Execute o seguinte comando na consola do PowerShell para instalar o runtime de limite de IoT e verificar a configuração:

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Este script fornece o seguinte: 
   * Python 3.6
   * O script de controlo contorno de IoT (iotedgectl.exe)

Poderá ver informativa saída da ferramenta de iotedgectl.exe vermelho na janela do PowerShell remota. Isto não necessariamente indicar erros. 

## <a name="next-steps"></a>Passos seguintes

Agora que tem um dispositivo com o tempo de execução do limite do IoT, saiba como [implementar e monitorizar os módulos de limite de IoT à escala][lnk-deploy].

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers