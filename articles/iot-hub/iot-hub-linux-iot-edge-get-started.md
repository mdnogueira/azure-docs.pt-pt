---
title: "Introdução ao limite de IoT do Azure (Linux) | Microsoft Docs"
description: "Como criar um gateway num computador com Linux e saber mais sobre os principais conceitos no Azure IoT Edge, como módulos e ficheiros de configuração JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb65e3c34d2b2a14370792d8506c13c8c5fb522e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>Explorar a arquitetura do Azure IoT Edge no Linux

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>Como executar o exemplo

O script **build.sh** gera o respetivo resultado na pasta **build** na sua cópia local do repositório **iot-edge**. Este resultado inclui os dois módulos de limite de IoT utilizados neste exemplo.

O script build coloca **liblogger.so** na pasta **build/modules/logger/** e **libhello\_world.so** na pasta **build/modules/hello_world/**. Utilize estes caminhos para o **do módulo caminho** valores conforme mostrado no ficheiro de definições JSON de exemplo.

O Olá\_mundo\_processo de exemplo utiliza o caminho para um ficheiro de configuração JSON como um argumento da linha de comandos. O ficheiro JSON de exemplo seguinte é disponibilizado no repositório do SDK, em **samples/hello\_world/src/hello\_world\_lin.json**. Este ficheiro de configuração funciona conforme é salvo se modificar o script de compilação para colocar os módulos de limite de IoT ou executáveis de exemplo em localizações não predefinidas.

> [!NOTE]
> Os caminhos dos módulos são relativos face ao diretório de trabalho atual a partir do qual o executável hello\_world\_sample é iniciado, não face ao diretório onde o executável está localizado. O ficheiro de configuração JSON de exemplo é predefinido para escrever “log.txt” no seu diretório de trabalho atual.

```json
{
    "modules" :
    [
        {
            "name" : "logger",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args" : {"filename":"log.txt"}
        },
        {
            "name" : "hello_world",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/hello_world/libhello_world.so"
            }
            },
            "args" : null
        }
    ],
    "links":
    [
        {
            "source": "hello_world",
            "sink": "logger"
        }
    ]
}
```

1. Navegue para o **criar** pasta na raiz da sua cópia local a **iot edge** repositório.

1. Execute o seguinte comando:

    ```sh
    ./samples/hello_world/hello_world_sample ../samples/hello_world/src/hello_world_lin.json
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]

