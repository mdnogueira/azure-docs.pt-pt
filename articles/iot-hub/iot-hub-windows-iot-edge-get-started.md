---
title: "Introdução ao Azure IoT Edge (Windows) | Microsoft Docs"
description: "Como criar um gateway do Azure IoT Edge num computador Windows e obter informações sobre os conceitos fundamentais do limite do Azure IoT, tais como módulos e ficheiros de configuração JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>Explorar a arquitetura de limite de IoT do Azure no Windows

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Executar o exemplo

O **build.cmd** script gera o respetivo resultado no **criar** pasta na sua cópia local do **iot edge** repositório. Este resultado inclui muitos ficheiros, mas este exemplo centra-se em três:
- Dois módulos de limite de IoT: **criar\\módulos\\registador\\depurar\\logger.dll** e **criar\\módulos\\hello_world\\ Depurar\\Olá\_world.dll**. 
- Um ficheiro executável: **criar\\amostras\\Olá\_mundo\\depurar\\Olá\_mundo\_sample.exe**. Este processo cria um ficheiro de configuração JSON como um argumento da linha de comandos.

O ficheiro quarto que utilizar neste exemplo não se encontra na pasta de compilação, mas foi incluído quando clonar o repositório de limite de iot:
- Um ficheiro de configuração JSON: **amostras\\Olá\_mundo\\src\\Olá\_mundo\_win.json**. Este ficheiro contém os caminhos para os dois módulos. Também declara onde logger.dll escreve o respetivo resultado ao. A predefinição é **log.txt** no seu diretório de trabalho atual. 

   >[!NOTE]
   >Se mover os módulos de exemplo ou adicionar as suas próprias para testar, atualize o **module.path** valores no ficheiro de configuração para corresponder. Os caminhos do módulo são relativos ao diretório onde **Olá\_mundo\_sample.exe** está localizado. 

Para executar o exemplo, siga estes passos:

1. Navegue para o **criar** pasta na raiz da sua cópia local a **iot edge** repositório.

1. Execute o seguinte comando:

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. O seguinte resultado significa que a amostra é executada com êxito:

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. Prima a **Enter** tecla para parar o processo.


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
