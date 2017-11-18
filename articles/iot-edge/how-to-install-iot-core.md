---
title: Instalar o limite de IoT do Azure no IoT Core | Microsoft Docs
description: Instalar o runtime do Azure IoT Edge num dispositivo Windows IoT Core
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/17/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a78efef6bc82a837782c462bc3558a9582a8da7c
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Instalar o runtime de limite de IoT no Windows IoT Core - pré-visualização

Pode executar o tempo de execução do limite de IoT do Azure, mesmo em dispositivos muito pequena do único quadro computador (SBC) que são muito prevalente atualmente da indústria de IoT. Este artigo explica o tempo de execução de aprovisionamento num [MinnowBoard Turbot] [ lnk-minnow] quadro de desenvolvimento com o Windows IoT Core.

## <a name="install-the-runtime"></a>Instalar o runtime

1. Instalar [Dashboard de núcleo do Windows 10 IoT] [ lnk-core] num sistema anfitrião.
1. Siga os passos no [configurar o seu dispositivo] [ lnk-board] para configurar o seu dashboard com a imagem de MinnowBoard Turbot/máx. de compilação 16299. 
1. Ativar no dispositivo, em seguida, [início de sessão remotamente com o PowerShell][lnk-powershell].
1. Na consola do PowerShell, instale o tempo de execução do contentor: 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-17.06.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Este script do PowerShell é a partir do servidor de compilação do projeto de Moby e destina-se apenas a fins de avaliação. -Não testado, aprovadas ou suportada pelo Docker.

1. Instalar o runtime de limite de IoT e certifique-se a configuração:

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