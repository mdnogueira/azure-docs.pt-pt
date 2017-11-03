---
title: "Conversão de dados no gateway de IoT com limite de IoT do Azure | Microsoft Docs"
description: "Utilize o gateway de IoT para converter o formato dos dados de sensor através de um módulo personalizado a partir da extremidade do Azure IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "conversão de dados de gateway do IOT, transformação de dados do gateway de iot"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: d5c735a4adbc59e9526ec4fd40720c5ec136d63d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>Utilize o gateway de IoT para transformação de dados de sensor com limite de IoT do Azure

> [!NOTE]
> Antes de começar este tutorial, certifique-se de que concluiu as lições seguintes na sequência:
> * [Configurar o Intel NUC como um gateway do IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [Utilizar o gateway de IoT para ligar coisas para a nuvem - SensorTag ao IoT Hub do Azure](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

Um objetivo de um gateway de Iot é processar os dados recolhidos antes de a enviar para a nuvem. Limite de IoT do Azure introduz módulos que podem ser criados e assembled para formar o fluxo de trabalho de processamento de dados. Um módulo recebe uma mensagem, efetua algumas ações no mesmo e, em seguida, mova-o por outros módulos processar.

## <a name="what-you-learn"></a>O que irá aprender

Saiba como criar um módulo para converter as mensagens do SensorTag num formato diferente.

## <a name="what-you-do"></a>O que fazer

* Crie um módulo para converter uma mensagem recebida para o formato. JSON.
* O módulo de compilação.
* Adicione o módulo para a aplicação de exemplo var limite de IoT do Azure.
* Execute a aplicação de exemplo.

## <a name="what-you-need"></a>Do que precisa

* Os tutoriais seguintes foi concluídos em sequência:
  * [Configurar o Intel NUC como um gateway do IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [Utilizar o gateway de IoT para ligar coisas para a nuvem - SensorTag ao IoT Hub do Azure](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* Um cliente SSH que é executado no computador anfitrião. PuTTY recomenda-se no Windows. Linux e macOS já vêm com um cliente SSH.
* O endereço IP e o nome de utilizador e palavra-passe para o gateway de acesso do cliente SSH.
* Uma ligação à Internet.

## <a name="create-a-module"></a>Criar um módulo

1. No computador anfitrião, execute o cliente SSH e ligar ao gateway de IoT.
1. Clone os ficheiros de origem do módulo de conversão a partir do GitHub para o diretório raiz do gateway de IoT executando os seguintes comandos:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   Este é um módulo nativo do limite do Azure, escrito em linguagem de programação C. O módulo converte o formato de mensagens recebidas dos seguintes:

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>O módulo de compilação

Para compilar o módulo, execute os seguintes comandos:

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module/my_module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$//" build.sh
# run the build shell script
./build.sh
```

Obtém um `libmy_module.so` depois de concluída a compilação do ficheiro. Anote o caminho absoluto deste ficheiro.

## <a name="add-the-module-to-the-ble-sample-application"></a>Adicionar o módulo para a aplicação de exemplo var

1. Ir para a pasta de amostras executando o seguinte comando:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. Abra o ficheiro de configuração executando o seguinte comando:

   ```bash
   vi ble_gateway.json
   ```

1. Adicionar um módulo por inserir o seguinte código para o `modules` secção.

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. Substitua `[Your libmy_module.so path]` no código com o caminho absoluto do libmy_module.so' ficheiros.
1. Substitua o código no `links` secção com o seguinte:

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. Prima `ESC`e, em seguida, escreva `:wq` para guardar o ficheiro.

## <a name="run-the-sample-application"></a>Executar a aplicação de exemplo

1. Ligar o SensorTag.
1. Defina a variável de ambiente de SSL_CERT_FILE executando o seguinte comando:

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. Execute a aplicação de exemplo com o módulo adicionado executando o seguinte comando:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Passos seguintes

Já a utilizar com êxito o gateway de IoT para converter a mensagem de SensorTag para o formato. JSON.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
