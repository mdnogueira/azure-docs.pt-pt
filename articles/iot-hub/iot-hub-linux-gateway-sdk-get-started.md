---
title: "Introdução ao SDK do Gateway do Azure IoT (Linux) | Microsoft Docs"
description: "Como criar um gateway num computador com Linux e saber mais sobre os principais conceitos no SDK do Gateway do Azure IoT como módulos e ficheiros de configuração do JSON."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 856ffeeeb8f9d8296ba972a9e070686171f7fde8
ms.lasthandoff: 03/31/2017


---
# <a name="explore-the-iot-gateway-sdk-architecture-on-linux"></a>Explorar a arquitetura SDK do Gateway do IoT em Linux

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como criar o exemplo

Antes de começar, tem de [configurar o seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Linux.

1. Abra uma shell.
1. Navegue até à pasta raiz na sua cópia local do repositório **azure-iot-gateway-sdk**.
1. Execute o script **tools/build.sh**. Este script utiliza o utilitário **cmake** para criar uma pasta denominada **build** na pasta raiz da sua cópia local do repositório **azure-iot-gateway-sdk** e gerar um makefile. Em seguida, o script compila a solução, ignorando os teste de unidade e os testes completos. Adicione o parâmetro **--run-unittests** se quiser criar e executar os testes de unidade. Adicione o parâmetro **--run-e2e-tests** se quiser criar e executar os testes completos.

> [!NOTE]
> Sempre que executar o script **build.sh**, este elimina e, em seguida, recria a pasta **build** na pasta raiz da sua cópia local do repositório **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Como executar o exemplo

1. O script **build.sh** gera o respetivo resultado na pasta **build** na sua cópia local do repositório **azure-iot-gateway-sdk**. Este resultado inclui os dois módulos utilizados neste exemplo.

    O script build coloca **liblogger.so** na pasta **build/modules/logger/** e **libhello\_world.so** na pasta **build/modules/hello_world/**. Utilize estes caminhos para o valor **caminho do módulo**, conforme mostrado no ficheiro de definições JSON de exemplo seguinte.
1. O processo hello\_world\_sample aceita o caminho para um ficheiro de configuração JSON como argumento da linha de comandos. O ficheiro JSON de exemplo seguinte é disponibilizado no repositório do SDK, em **samples/hello\_world/src/hello\_world\_lin.json**. Este ficheiro de configuração funciona tal como está, a menos que modifique o script build para colocar módulos ou executáveis de exemplo em localizações não predefinidas.

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
1. Navegue para a pasta **build**.
1. Execute o seguinte comando:

   `./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

