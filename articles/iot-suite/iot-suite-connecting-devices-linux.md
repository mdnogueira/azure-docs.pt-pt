---
title: "Aprovisionar dispositivos Linux para monitorização remota no C - do Azure | Microsoft Docs"
description: "Descreve como ligar um dispositivo à solução de monitorização remota do Azure IoT Suite pré-configurada utilizando uma aplicação de escrita no C em execução no Linux."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2017
ms.author: dobett
ms.openlocfilehash: 542d1e0c4c4d6cfa5d2fe9df90a7a34c72f19fc0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Ligar o seu dispositivo à solução pré-configurada monitorização remota (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como ligar um dispositivo físico a solução pré-configurada de monitorização remota.

## <a name="create-a-c-client-project-on-linux"></a>Criar um projeto de cliente do C no Linux

Tal como acontece com as aplicações mais incorporadas que são executadas em dispositivos restrita, o código de cliente para a aplicação de dispositivo é escrito nos C. Neste tutorial, tem de compilar a aplicação numa máquina com Ubuntu (Linux).

Para concluir estes passos, precisa de um dispositivo com Ubuntu versão 15.04 ou posterior. Antes de continuar, instale os pacotes de pré-requisitos no seu dispositivo Ubuntu utilizando o seguinte comando:

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Instalar as bibliotecas de cliente no seu dispositivo

As bibliotecas de cliente do IoT Hub do Azure estão disponíveis como um pacote, pode instalar no seu dispositivo de Ubuntu utilizando o **apt get** comando. Conclua os seguintes passos para instalar o pacote que contém os ficheiros de biblioteca e cabeçalho de cliente de IoT Hub no seu computador Ubuntu:

1. Numa shell, adicione o repositório de AzureIoT para o seu computador:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Instalar o pacote do azure-iot-sdk-c-dev

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Instalar o analisador de Parson JSON

As bibliotecas de cliente do IoT Hub utilizam o analisador de Parson JSON para analisar payloads de mensagem. Numa pasta adequada no seu computador, clone o repositório do Parson GitHub utilizando o seguinte comando:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Preparar o seu projeto

No seu computador Ubuntu, crie uma pasta denominada `remote_monitoring`. No `remote_monitoring` pasta:

- Criar os ficheiros de quatro `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, e `CMakeLists.txt`.
- Criar uma pasta denominada `parson`.

Copie os ficheiros `parson.c` e `parson.h` da sua cópia local do repositório Parson para o `remote_monitoring/parson` pasta.

No editor de texto, abra o `remote_monitoring.c` ficheiro. Adicione as seguintes instruções `#include`:

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
