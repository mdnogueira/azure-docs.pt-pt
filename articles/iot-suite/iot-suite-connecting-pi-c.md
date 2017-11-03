---
title: "Aprovisionar Raspberry Pi para monitorização remota, utilizando C - do Azure | Microsoft Docs"
description: "Descreve como ligar um dispositivo Raspberry Pi pré-configuradas do Azure IoT Suite solução de monitorização remota utilizando uma aplicação de escrita no C."
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2017
ms.author: dobett
ms.openlocfilehash: 2f5915093a0d7984f0350af21aa438cdd588bbf2
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-c"></a>Ligar o seu dispositivo Raspberry Pi à solução pré-configurada monitorização remota (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como ligar um dispositivo físico a solução pré-configurada de monitorização remota. Tal como acontece com as aplicações mais incorporadas que são executadas em dispositivos restrita, o código de cliente para a aplicação de dispositivo Raspberry Pi é escrito em C. Neste tutorial, criar a aplicação num Raspberry Pi o SO Raspbian em execução.

### <a name="required-hardware"></a>Hardware necessário

Um computador de secretária que lhe permite ligar remotamente à linha de comandos no Raspberry Pi.

[Microsoft IoT Starter Kit para Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou componentes equivalentes. Este tutorial utiliza os seguintes itens do kit:

- Raspberry Pi 3
- Cartão MicroSD (com NOOBS)
- Um cabo USB Mini
- Um cabo de Ethernet

### <a name="required-desktop-software"></a>Software de ambiente de trabalho necessárias

Terá de cliente SSH no seu computador de secretária que lhe permite aceder remotamente a linha de comandos no Raspberry Pi.

- Windows não inclui um cliente SSH. Recomendamos que utilize [PuTTY](http://www.putty.org/).
- A maioria das distribuições de Linux e Mac OS incluem o utilitário da linha de comandos do SSH. Para obter mais informações, consulte [SSH utilizando o Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Software Raspberry Pi necessárias

Os passos seguintes mostram como preparar o seu Raspberry Pi para criar uma aplicação de C que estabelece ligação com a solução pré-configurada:

1. Ligar à sua utilização Raspberry Pi `ssh`. Para obter mais informações, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) no [site Raspberry Pi](https://www.raspberrypi.org/).

1. Utilize o seguinte comando para atualizar o seu Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Utilize o seguinte comando para adicionar as ferramentas de desenvolvimento necessária e bibliotecas à sua Raspberry Pi:

    ```sh
    sudo apt-get install g++ make cmake gcc git
    ```

1. Utilize os seguintes comandos para instalar as bibliotecas de cliente do IoT Hub:

    ```sh
    grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
    sudo apt-get update
    sudo apt-get install -y azure-iot-sdk-c-dev cmake libcurl4-openssl-dev git-core
    ```

1. Clone o parser de Parson JSON para o Raspberry Pi usando os seguintes comandos:

    ```sh
    cd ~
    git clone https://github.com/kgabis/parson.git
    ```

## <a name="create-a-project"></a>Criar um projeto

Conclua os seguintes passos, utilizando o `ssh` ligação ao seu Raspberry Pi:

1. Crie uma pasta denominada `remote_monitoring` na pasta raiz em Raspberry Pi. Navegue para esta pasta na sua linha de comandos:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Criar os ficheiros de quatro `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, e `CMakeLists.txt` no `remote_monitoring` pasta.

1. Criar uma pasta denominada `parson` no `remote_monitoring` pasta.

1. Copie os ficheiros `parson.c` e `parson.h` da sua cópia local do repositório Parson para o `remote_monitoring/parson` pasta.

1. No editor de texto, abra o `remote_monitoring.c` ficheiro. No Raspberry Pi, pode utilizar o `nano` ou `vi` editor de texto. Adicione as seguintes instruções `#include`:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="add-code-to-run-the-app"></a>Adicionar código para executar a aplicação

No editor de texto, abra o `remote_monitoring.h` ficheiro. Adicione o seguinte código:

```c
void remote_monitoring_run(void);
```

No editor de texto, abra o `main.c` ficheiro. Adicione o seguinte código:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Os passos seguintes descrevem como utilizar *CMake* para criar a sua aplicação de cliente.

1. No editor de texto, abra o **CMakeLists.txt** ficheiros o `remote_monitoring` pasta.

1. Adicione as seguintes instruções para definir como criar a aplicação de cliente:

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

1. No `remote_monitoring` pasta, crie uma pasta para armazenar o *tornar* ficheiros CMake gera. Em seguida, execute o **cmake** e **tornar** comandos da seguinte forma:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Executar a aplicação de cliente e enviar telemetria ao IoT Hub:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
