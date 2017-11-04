---
title: "Utilizar um dispositivo físico com o Azure IoT Edge | Microsoft Docs"
description: "Como utilizar um dispositivo da Texas Instruments SensorTag para enviar dados para um hub IoT através de um gateway de IoT Edge em execução num dispositivo Raspberry Pi 3. O gateway é criado com o Azure IoT Edge."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: andbuc
ms.openlocfilehash: b24828ee1a09ba8e5f657954e11936f124270173
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="forward-device-to-cloud-messages-to-iot-hub-using-azure-iot-edge-on-a-raspberry-pi"></a>Reencaminhar mensagens do dispositivo para nuvem ao IoT Hub, utilizando o Azure IoT Edge num Raspberry Pi

Estas instruções do [exemplo de baixa energia Bluetooth] [ lnk-ble-samplecode] mostra-lhe como utilizar [Azure IoT Edge] [ lnk-sdk] para:

* Reencaminhar telemetria do dispositivo para a nuvem ao IoT Hub a partir de um dispositivo físico.
* Comandos de rota do IoT Hub para um dispositivo físico.

Estas instruções abrangem:

* **Arquitetura**: as informações da arquitetura importantes sobre o Bluetooth de amostra de baixa energia.
* **Criação e execução**: os passos necessários para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura

As instruções mostram como compilar e executar um gateway de IoT em 3 de Pi um Raspberry que executa Raspbian Linux. O gateway é criado com o limite de IoT. Este exemplo utiliza um dispositivo da Texas Instruments SensorTag Bluetooth baixa energia (var) para recolher dados de temperatura.

Quando executa o gateway de IoT-lo:

* Estabelece ligação a um dispositivo de SensorTag utilizando o protocolo de Bluetooth baixa energia (var).
* Estabelece ligação ao IoT Hub, utilizando o protocolo HTTPS.
* Reencaminha telemetria do dispositivo SensorTag ao IoT Hub.
* Comandos de rotas do IoT Hub para o dispositivo SensorTag.

O gateway contém os seguintes módulos de limite de IoT:

* A *módulo var* que interaja com um dispositivo de var para receber dados de temperatura do dispositivo e enviar comandos para o dispositivo.
* A *nuvem var para o módulo de dispositivo* que traduz mensagens JSON enviadas a partir do IoT Hub para instruções var para o *módulo var*.
* A *módulo de registo* que regista todas as mensagens de gateway num ficheiro local.
* Um *módulo de mapeamento de identidades* que traduz entre endereços de MAC do dispositivo var e identidades de dispositivo do IoT Hub do Azure.
* Um *módulo do IoT Hub* que carrega dados de telemetria a um IoT hub e recebe comandos de dispositivo a partir de um hub IoT.
* A *módulo de impressora var* que interpreta telemetria do dispositivo var e impressões formatados dados para a consola para ativar a depuração e resolução de problemas.

### <a name="how-data-flows-through-the-gateway"></a>Como os dados fluem através do gateway

O diagrama de blocos seguinte ilustra o pipeline de fluxo de dados de carregamento de telemetria:

![Pipeline de gateway de carregamento de telemetria](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

Os passos que um item de telemetria demora estiverem em deslocação de um dispositivo var IoT Hub são:

1. O dispositivo var gera uma amostra de temperatura e envia-o sobre o Bluetooth para o módulo de var no gateway.
1. O módulo de var recebe o exemplo e publica-o para o mediador juntamente com o endereço MAC do dispositivo.
1. O módulo de mapeamento de identidades escolherá esta mensagem e utiliza uma tabela interna ao traduzir o endereço MAC do dispositivo para uma identidade de dispositivo IoT Hub. Uma identidade de dispositivo do IoT Hub é composta por um ID de dispositivo e a chave do dispositivo.
1. O módulo de mapeamento de identidades publica uma nova mensagem que contém as seguintes informações:
   - Dados de exemplo de temperatura
   - O endereço MAC do dispositivo
   - O ID de dispositivo
   - Chave do dispositivo  
1. O módulo de IoT Hub recebe esta mensagem nova (gerada pelo módulo de mapeamento de identidade) e publica-o ao IoT Hub.
1. O módulo de registo regista todas as mensagens do Mediador de um ficheiro local.

O diagrama de blocos seguinte ilustra o pipeline de fluxo de dados de comando do dispositivo:

![Pipeline de gateway do comando de dispositivo](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. O módulo do IoT Hub consulta periodicamente se existem o IoT hub para novas mensagens de comando.
1. Quando o módulo do IoT Hub recebe uma nova mensagem de comando, publica-o mediador.
1. O módulo de mapeamento de identidades escolherá a mensagem de comando e utiliza uma tabela interna ao traduzir o ID do dispositivo IoT Hub a um endereço MAC do dispositivo. Em seguida, publica uma nova mensagem, que inclui o endereço MAC do dispositivo de destino no mapa de propriedades da mensagem.
1. O módulo de var nuvem para o dispositivo escolherá esta mensagem e traduz-lo para a instrução var adequada para o módulo de var. Em seguida, publica uma nova mensagem.
1. O módulo de var escolherá esta mensagem e executa a instrução de e/s ao comunicar com o dispositivo var.
1. O módulo de registo regista todas as mensagens do Mediador de um ficheiro de disco.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição ativa do Azure.

> [!NOTE]
> Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Azure Free Trial (Avaliação Gratuita do Azure)][lnk-free-trial].

Terá de cliente SSH no seu computador de secretária que lhe permite aceder remotamente a linha de comandos no Raspberry Pi.

- Windows não inclui um cliente SSH. Recomendamos que utilize [PuTTY](http://www.putty.org/).
- A maioria das distribuições de Linux e Mac OS incluem o utilitário da linha de comandos do SSH. Para obter mais informações, consulte [SSH utilizando o Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

## <a name="prepare-your-hardware"></a>Prepare o hardware

Este tutorial parte do princípio de que está a utilizar um [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) dispositivo ligado a um 3 de Pi Raspberry Raspbian a executar.

### <a name="install-raspbian"></a>Instalar Raspbian

Pode utilizar qualquer uma das seguintes opções para instalar Raspbian no seu dispositivo Raspberry Pi 3.

* Para instalar a versão mais recente do Raspbian, utilize o [NOOBS] [ lnk-noobs] interface gráfica do utilizador.
* Manualmente [transferir] [ lnk-raspbian] e escrita a imagem do sistema de operativo Raspbian mais recente para um cartão SD.

### <a name="sign-in-and-access-the-terminal"></a>A iniciar sessão e aceder ao terminal

Tem duas opções para aceder a um ambiente de terminal no seu Raspberry Pi:

* Se tiver um teclado e monitor ligado à sua Raspberry Pi, pode utilizar o GUI Raspbian para aceder a uma janela de terminal.

* Aceder a linha de comandos no seu Raspberry Pi utilizando SSH a partir do seu computador de secretária.

#### <a name="use-a-terminal-window-in-the-gui"></a>Utilizar uma janela de terminal no GUI

As credenciais predefinidas Raspbian são username **pi** e palavra-passe **raspberry**. Na barra de tarefas no GUI, pode iniciar o **Terminal** utilitário utilizando o ícone que se assemelha um monitor.

#### <a name="sign-in-with-ssh"></a>Inicie sessão com SSH

Pode utilizar o SSH para o acesso da linha de comandos para sua Raspberry Pi. O artigo [SSH (Secure Shell)] [ lnk-pi-ssh] descreve como configurar SSH no seu Raspberry Pi e como ligar a partir de [Windows] [ lnk-ssh-windows] ou [ SO Linux & Mac][lnk-ssh-linux].

Inicie sessão com o nome de utilizador **pi** e palavra-passe **raspberry**.

### <a name="install-bluez-537"></a>Instalar BlueZ 5.37

Os módulos var comunicar com o hardware de Bluetooth através da pilha de BlueZ. Tem a versão 5.37 de BlueZ para os módulos funcionem corretamente. Estas instruções Certifique-se que a versão correta do BlueZ está instalada.

1. Pare o daemon de bluetooth atual:

    ```sh
    sudo systemctl stop bluetooth
    ```

1. Instale as dependências de BlueZ:

    ```sh
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf libtool glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```

1. Transferir o código de origem BlueZ partir bluez.org:

    ```sh
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```

1. Deszipe o código de origem:

    ```sh
    tar -xvf bluez-5.37.tar.xz
    ```

1. Altere os diretórios para a pasta recém-criada:

    ```sh
    cd bluez-5.37
    ```

1. Configure o código de BlueZ que serão criados:

    ```sh
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```

1. Crie BlueZ:

    ```sh
    make
    ```

1. Instalar BlueZ depois de estar concluído criação:

    ```sh
    sudo make install
    ```

1. Alterar a configuração do serviço systemd para bluetooth, pelo que aponta para o novo daemon de bluetooth no ficheiro `/lib/systemd/system/bluetooth.service`. Substitua a linha de 'ExecStart' com o seguinte texto:

    ```conf
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Ativar a conetividade para o dispositivo SensorTag do seu dispositivo Raspberry Pi 3

Antes de executar o exemplo, tem de confirmar que o seu 3 de Pi Raspberry pode ligar ao dispositivo SensorTag.

1. Certifique-se a `rfkill` utilitário está instalado:

    ```sh
    sudo apt-get install rfkill
    ```

1. Desbloquear bluetooth em 3 de Pi Raspberry e verificar se o número de versão é **5.37**:

    ```sh
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```

1. Para introduzir a shell de bluetooth interativa, inicie o serviço de bluetooth e executar o **bluetoothctl** comando:

    ```sh
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Introduza o comando **ativação** a potência se o controlador de bluetooth. O comando devolve o resultado semelhante ao seguinte exemplo:

    ```sh
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. Na shell do bluetooth interativa, introduza o comando **verificar em** para procurar dispositivos bluetooth. O comando devolve o resultado semelhante ao seguinte exemplo:

    ```sh
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. Se o dispositivo SensorTag detetável, premindo o botão pequeno (o LED verde deve flash). O 3 do Raspberry Pi deve detetar o dispositivo SensorTag:

    ```sh
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    Neste exemplo, pode ver que é o endereço MAC do dispositivo SensorTag **A0:E6:F8:B5:F6:00**.

1. Desativar a análise introduzindo o **analisar desativar** comando:

    ```sh
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. Ligar para o seu dispositivo SensorTag utilizando o seu endereço MAC introduzindo **ligar \<endereço MAC\>**. A saída de exemplo seguinte é abreviada para efeitos de clareza:

    ```sh
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```

    > Pode listar as características de GATT do dispositivo novamente utilizando o **lista atributos** comando.

1. Pode agora desligar do dispositivo com o **desligar** comando e, em seguida, saia do bluetooth shell com o **sair** comando:

    ```sh
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Agora, está pronto para executar o exemplo de limite de IoT var no seu 3 de Pi Raspberry.

## <a name="run-the-iot-edge-ble-sample"></a>Executar o exemplo de IoT Edge var

Para executar o exemplo de IoT Edge var, tem de concluir três tarefas:

* Configure dois dispositivos de exemplo no seu IoT Hub.
* Crie IoT Edge no seu dispositivo Raspberry Pi 3.
* Configure e execute o exemplo de var no seu dispositivo Raspberry Pi 3.

No momento da escrita, o limite de IoT só suporta módulos var em gateways em execução no Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurar dois dispositivos de exemplo no seu IoT Hub

* [Criar um hub IoT] [ lnk-create-hub] na sua subscrição do Azure, é necessário o nome do seu hub para concluir estas instruções. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Adicionar um dispositivo designado **SensorTag_01** ao seu IoT hub e tome nota da respetiva chave Id e o dispositivo. Pode utilizar o [Explorador de dispositivo ou no Explorador do iothub] [ lnk-explorer-tools] ferramentas para adicionar este dispositivo ao IoT hub que criou no passo anterior e ao obter a respetiva chave. Mapear este dispositivo para o dispositivo SensorTag quando configurar o gateway.

### <a name="build-azure-iot-edge-on-your-raspberry-pi-3"></a>Criar limite de IoT do Azure no seu Raspberry Pi 3

Instale dependências para o limite do Azure IoT:

```sh
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev libtool
```

Utilize os seguintes comandos para clonar o limite de IoT e todos os respetivos submodules para o diretório raiz:

```sh
cd ~
git clone https://github.com/Azure/iot-edge.git
```

Quando tiver uma cópia completa do repositório IoT Edge no seu 3 de Pi Raspberry, pode criá-lo utilizando o seguinte comando a partir da pasta que contém o SDK:

```sh
cd ~/iot-edge
./tools/build.sh  --disable-native-remote-modules
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Configurar e executar o exemplo de var no seu 3 de Pi Raspberry

Para o arranque e executar o exemplo, configure cada módulo de limite de IoT que participa no gateway. Esta configuração é fornecida um ficheiro JSON e tem de configurar todos os módulos de limite de IoT participantes cinco. Há um ficheiro JSON de exemplo no repositório denominado **gateway\_sample.json** que pode utilizar como ponto de partida para a criação do seu próprio ficheiro de configuração. Este ficheiro está a ser o **ble_gateway/samples/src** pasta na cópia local do repositório de limite de IoT.

As secções seguintes descrevem como editar este ficheiro de configuração para o exemplo de var. Iremos partem do princípio de que o repositório de limite de IoT é no **/home/pi/iot-edge /** pasta no seu 3 de Pi Raspberry. Se o repositório noutro local, ajuste os caminhos em conformidade.

#### <a name="logger-configuration"></a>Configuração de registo

Pressupondo que o repositório de gateway está localizado no **/home/pi/iot-edge /** pasta, configurar o módulo de registo da seguinte forma:

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>Configuração do módulo var

A configuração de exemplo para o dispositivo var assume um dispositivo da Texas Instruments SensorTag. Qualquer dispositivo var padrão que pode funcionar como um GATT periférico deverão funcionar, mas poderá ter de atualizar os IDs de características GATT e dados. Adicione o endereço MAC do dispositivo SensorTag:

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

Se não estiver a utilizar um dispositivo SensorTag, consulte a documentação para o seu dispositivo var determinar se necessita de atualizar o característica GATT IDs e valores de dados.

#### <a name="iot-hub-module"></a>Módulo do IoT Hub

Adicione o nome do seu IoT Hub. O valor de sufixo é normalmente **azure devices.net**:

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuração do módulo de mapeamento de identidades

Adicione o endereço MAC do seu dispositivo SensorTag e o ID de dispositivo e a chave do **SensorTag_01** dispositivo adicionado ao seu IoT Hub:

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuração do módulo var impressora

```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>Configuração do módulo BLEC2D

```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>Configuração de encaminhamento

A seguinte configuração assegura que o encaminhamento entre os módulos de limite de IoT seguintes:

* O **registador** módulo recebe e regista todas as mensagens.
* O **SensorTag** módulo envia mensagens para ambos os **mapeamento** e **var impressora** módulos.
* O **mapeamento** módulo envia mensagens para o **IoTHub** módulo a ser enviados ao seu IoT Hub.
* O **IoTHub** módulo envia mensagens de volta para o **mapeamento** módulo.
* O **mapeamento** módulo envia mensagens para o **BLEC2D** módulo.
* O **BLEC2D** módulo envia mensagens de volta para o **Sensor Tag** módulo.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

Para executar o exemplo, passar o caminho para o ficheiro de configuração JSON como parâmetro para o **var\_gateway** binário. O comando seguinte assume que está a utilizar o **gateway_sample.json** ficheiro de configuração. Executar este comando a partir de **iot edge** pasta Raspberry Pi:

```sh
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Poderá ter de clique no botão pequeno no dispositivo SensorTag para torná-lo detetável antes de executar o exemplo.

Quando executar o exemplo, pode utilizar o [Explorador de dispositivo](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ou [iothub explorer](https://github.com/Azure/iothub-explorer) ferramenta para monitorizar as mensagens que o gateway de IoT reencaminha do dispositivo SensorTag. Por exemplo, através do Explorador do iothub pode monitorizar as mensagens do dispositivo para a nuvem utilizando o seguinte comando:

```sh
iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
```

## <a name="send-cloud-to-device-messages"></a>Enviar mensagens da cloud para o dispositivo

O módulo de var também suporta enviar comandos do IoT Hub para o dispositivo. Pode utilizar o [Explorador de dispositivo](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ou [iothub explorer](https://github.com/Azure/iothub-explorer) ferramenta para enviar mensagens JSON que o módulo de gateway var encaminha para o dispositivo var.

Se estiver a utilizar o dispositivo da Texas Instruments SensorTag, pode ativar a vermelho LED, verde LED ou buzzer ao enviar comandos a partir do IoT Hub. Antes de enviar comandos a partir do IoT Hub, envie primeiro as seguintes duas mensagens JSON por ordem. Em seguida, pode enviar qualquer um dos comandos para ativar o lights ou buzzer.

1. Repor todas as LEDs e o buzzer (desligue-os):

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. Configure e/s como 'remoto':

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

Agora pode enviar qualquer um dos seguintes comandos para ativar o lights ou buzzer no dispositivo SensorTag:

* Ative o LED vermelho:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* Ative o LED verde:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* Ative o buzzer:

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

Se pretender obter uma compreensão mais avançada de limite de IoT e experimente alguns exemplos de código, visite as seguintes tutoriais para programadores e os recursos:

* [Limite de IoT do Azure][lnk-sdk]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
