---
title: "Procedimento do Azure - Como utilizar diferentes modelos de segurança de hardware com o SDK do Cliente do Serviço Aprovisionamento de Dispositivos no Azure | Microsoft Docs"
description: "Procedimento do Azure - Como utilizar diferentes modelos de segurança de hardware com o SDK do Cliente do Serviço Aprovisionamento de Dispositivos no Azure"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 620d86b62cf43c3e1a5f7f5c724fcf00174f30e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>Como utilizar diferentes Módulos de Segurança de Hardware com o SDK do Cliente do Serviço Aprovisionamento de Dispositivos
Estes passos mostram como utilizar diferentes [Módulos de Segurança de Hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) com o SDK do Cliente do Serviço Aprovisionamento de Dispositivos em C mediante a utilização de dispositivos físicos e simuladores.  O serviço de aprovisionamento suporta dois modos de autenticação: X**.**509 e Trusted Platform Module (TPM).

## <a name="prerequisites"></a>Pré-requisitos

Prepare o seu ambiente de desenvolvimento de acordo com a secção denominada “Preparar o ambiente de desenvolvimento” do guia [Create and provision simulated device] (./quick-create-simulated-device.md) (Criar e aprovisionar um dispositivo simulado com o Serviço Aprovisionamento de Dispositivos no Hub IoT (pré-visualização)).

## <a name="enable-authentication-with-different-hsms"></a>Ativar a autenticação com diferentes HSMs

Antes de o dispositivo físico ou o simulador poderem ser inscritos no portal do Azure, o modo de autenticação (X**.**509 ou TPM) têm de estar ativados no mesmo.  Navegue para a pasta de raiz de azure-iot-sdk-c.  Execute o comando especificado, consoante o modo de autenticação que escolher.

### <a name="use-x509-with-simulator"></a>Utilizar X**.**509 com o simulador

O serviço de aprovisionamento inclui um emulador de Device Identity Composition Engine (DICE) que gera um certificado X**.**509 para autenticar o dispositivo.  Execute o comando seguinte para ativar a autenticação X**.**509:

```
cmake -Ddps_auth_type=x509 ..
```

Estão disponíveis informações sobre o hardware com DICE [aqui](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Utilizar X**.** 509 com hardware

O serviço de aprovisionamento pode ser utilizado com X**.**509 noutro hardware.  Para estabelecer a ligação, é necessário uma interface entre o hardware e o SDK.  Entre em contacto com o fabricante do seu HSM para obter informações sobre a interface.

### <a name="use-tpm"></a>Utilizar TPM

O serviço de aprovisionamento pode ligar-se a chips de TPM de hardware Windows e Linux com um Token SAS.  Execute o comando seguinte para ativar a autenticação TPM:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Utilizar TPM com o simulador

Se não tiver um dispositivo com chips de TPM, pode utilizar um simulador para fins de desenvolvimento no SO Windows.  Execute o comando seguinte para ativar a autenticação TPM e executar o simulador TPM:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Criar o SDK
Crie o SDK antes de criar a inscrição dos dispositivos.

### <a name="linux"></a>Linux
- Para criar o SDK no Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Para criar os binários de Depuração, adicione a opção CMake correspondente ao comando de geração de projeto, por exemplo:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Existem muitas [opções de configuração CMake](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) disponíveis para criar o SDK. Por exemplo, pode desativar uma das pilhas de protocolo disponíveis através da adição de um argumento para o comando de geração de projeto CMake:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Também pode criar e executar o teste da unidade:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Para criar o SDK no Windows, siga os passos seguintes para gerar ficheiros de projeto:
    - Abra uma “Linha de Comandos de Programador” para o VS2015
    - Execute os seguintes comandos de CMake a partir da raiz do repositório:
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    Este comando cria bibliotecas x86. Para criar para x64, modifique o argumento do gerador de cmake: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Se a geração do projeto for concluída com êxito, deverá ver um ficheiro de solução do Visual Studio (.sln) na pasta `cmake`. Para criar o SDK:
    - Abra **cmake\azure_iot_sdks.sln** no Visual Studio e crie-o **OU**
    - Execute o comando seguinte na linha de comandos que utilizou para gerar os ficheiros de projeto:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Para criar os binários de Depuração, utilize o argumento MSBuild correspondente: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Existem muitas opções de configuração CMake disponíveis para criar o SDK. Por exemplo, pode desativar uma das pilhas de protocolo disponíveis através da adição de um argumento para o comando de geração de projeto CMake:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Além disso, pode criar e executar testes de unidade:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Bibliotecas a incluir
- Estas bibliotecas devem ser incluídas no seu SDK:
    - O serviço de aprovisionamento: dps_http_transport, dps_client, dps_security_client
    - Segurança do Hub IoT: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-dps"></a>Criar uma entrada de inscrição de dispositivos no DPS

### <a name="tpm"></a>TPM
Se estiver a utilizar TPM, siga as instruções em [“Criar e aprovisionar um dispositivo simulado com o Serviço Aprovisionamento de Dispositivos no Hub IoT”](./quick-create-simulated-device.md) para criar uma entrada de inscrição de dispositivos no DPS e simular o primeiro arranque.

### <a name="x509"></a>X**.**509
1. Para inscrever dispositivos no serviço de aprovisionamento, tem de anotar a Chave de Endossamento e o ID de Registo de cada dispositivo, que são apresentados na Ferramenta de Aprovisionamento fornecida pelo SDK do Cliente. Execute o comando seguinte para imprimir o certificado de AC de raiz (para grupos de inscrição) e o certificado do signatário (para inscrições individuais):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Inicie sessão no portal do Azure, clique no botão **Todos os recursos**, no menu do lado esquerdo, e abra o serviço DPS.
   - Inscrição Individual X**.**509: no painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione o separador **Inscrições Individuais** e clique no botão **Adicionar**, na parte superior. Selecione **X**.**509** como o *Mecanismo* de atestado de identidades e carregue o certificado do signatário conforme indicado pelo painel. Quando tiver terminado, clique no botão **Guardar**. 
   - Inscrição em Grupo X**.**509: no painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione o separador **Inscrições em Grupo** e clique no botão **Adicionar**, na parte superior. Selecione **X**.**509** como o *Mecanismo* de atestado de identificações, introduza um nome para o grupo e um nome para a certificação e carregue o certificado de CA de raiz, conforme indicado pelo painel. Quando tiver terminado, clique no botão **Guardar**. 

## <a name="connecting-to-iot-hub-after-provisioning"></a>Ligar ao Hub IoT após o aprovisionamento

Após o dispositivo ter sido aprovisionado pelo serviço de aprovisionamento, esta API utiliza o modo de autenticação HSM para se ligar ao Hub IoT: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
