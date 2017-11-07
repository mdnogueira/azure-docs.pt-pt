---
title: Ligar um dispositivo utilizando C no Linux | Microsoft Docs
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a5768041a13d5ddc355c054dc85ba651b0752aba
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Ligar o seu dispositivo à solução pré-configurada monitorização remota (Linux)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Compilar e executar um cliente de exemplo C Linux
Os passos seguintes mostram como criar uma aplicação cliente que comunica com a solução pré-configurada de monitorização remota. Esta aplicação é escrita no C e incorporada e execute Ubuntu Linux.

Para concluir estes passos, precisa de um dispositivo com Ubuntu versão 15.04 ou 15.10. Antes de continuar, instale os pacotes de pré-requisitos no seu dispositivo Ubuntu utilizando o seguinte comando:

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Instalar as bibliotecas de cliente no seu dispositivo
As bibliotecas de cliente do IoT Hub do Azure estão disponíveis como um pacote, pode instalar no seu dispositivo de Ubuntu utilizando o **apt get** comando. Conclua os seguintes passos para instalar o pacote que contém os ficheiros de biblioteca e cabeçalho de cliente de IoT Hub no seu computador Ubuntu:

1. Numa shell, adicione o repositório de AzureIoT para o seu computador:
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. Instalar o pacote do azure-iot-sdk-c-dev
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Instalar o analisador de Parson JSON
As bibliotecas de cliente do IoT Hub utilizam o analisador de Parson JSON para analisar payloads de mensagem. Numa pasta adequada no seu computador, clone o repositório do Parson GitHub utilizando o seguinte comando:

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>Preparar o seu projeto
No seu computador Ubuntu, crie uma pasta denominada **remoto\_monitorização**. No **remoto\_monitorização** pasta:

- Criar os ficheiros de quatro **Main**, **remoto\_monitoring.c**, **remoto\_monitoring.h**, e **CMakeLists.txt**.
- Criar uma pasta denominada **parson**.

Copie os ficheiros **parson.c** e **parson.h** da sua cópia local do repositório Parson para o **remoto\_monitorização/parson** pasta.

No editor de texto, abra o **remoto\_monitoring.c** ficheiro. Adicione as seguintes instruções `#include`:
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>Chamar remoto\_monitorização\_executar a função
No editor de texto, abra o **remote_monitoring.h** ficheiro. Adicione o seguinte código:

```
void remote_monitoring_run(void);
```

No editor de texto, abra o **Main** ficheiro. Adicione o seguinte código:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação
Os passos seguintes descrevem como utilizar *CMake* para criar a sua aplicação de cliente.

1. No editor de texto, abra o **CMakeLists.txt** ficheiros o **remote_monitoring** pasta.

1. Adicione as seguintes instruções para definir como criar a aplicação de cliente:
   
    ```
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
1. No **remote_monitoring** pasta, crie uma pasta para armazenar o *certifique-* ficheiros CMake gera e execute o **cmake** e **tornar** os comandos da seguinte forma:
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Executar a aplicação de cliente e enviar telemetria ao IoT Hub:
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

