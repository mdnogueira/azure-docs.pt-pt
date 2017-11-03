---
title: Raspberry Pi nuvem (C) - ligar Raspberry Pi ao IoT Hub do Azure | Microsoft Docs
description: Saiba como configurar e ligar Raspberry Pi ao Azure IoT Hub para Raspberry Pi enviar dados para a plataforma de nuvem do Azure, neste tutorial.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: Azure iot raspberry pi, iothub raspberry pi, dados de envio de raspberry pi na nuvem, raspberry pi na nuvem
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/12/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d643647d4103acd511ed270132c844da12f2ac9b
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Ligar Raspberry Pi IoT Hub (C) do Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, comece por aprender as noções básicas de trabalhar com Raspberry Pi que está a executar Raspbian. Em seguida, aprenderá a forma totalmente integrada ligar os seus dispositivos para a nuvem, utilizando o [IoT Hub do Azure](iot-hub-what-is-iot-hub.md). Para exemplos do Windows 10 IoT Core, visite o [Windows Dev Center](http://www.windowsondevices.com/).

Não tem um kit ainda? Tente [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Ou comprar um novo kit [aqui](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>O que fazer

* Crie um hub IoT.
* Registe um dispositivo para Pi no seu IoT hub.
* A configuração Raspberry Pi.
* Execute um exemplo de aplicação no instalador de plataforma para enviar dados de sensores ao seu IoT hub.

Ligar Raspberry Pi a um IoT hub que criou. Em seguida, execute um exemplo de aplicação no Pi para recolher dados relativos à temperatura e humidade de um sensor BME280. Por fim, enviar os dados de sensor ao seu IoT hub.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT do Azure e obter a cadeia de ligação do novo dispositivo.
* Como ligar Pi com um sensor BME280.
* Como recolher dados de sensores ao executar uma aplicação de exemplo no Pi.
* Como enviar dados de sensores ao seu IoT hub.

## <a name="what-you-need"></a>Do que precisa

![Do que precisa](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Placa Raspberry Pi 2 ou 3 do Raspberry Pi.
* Uma subscrição ativa do Azure. Se não tiver uma conta do Azure, [criar uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um monitor, um teclado USB e um rato que se ligam a Pi.
* Um Mac ou num PC com Windows ou Linux.
* Uma ligação à Internet.
* Um 16 GB ou superior cartão microSD.
* Um USB-cartão SD, placa ou microSD gravado a imagem do sistema operativo no cartão microSD.
* Forneça uma potência de 2 amp 5 volt com o cabo USB micro 6 online.

Os seguintes itens são opcionais:

* Um assembled Adafruit BME280 temperatura, pressão e humidade sensor.
* Um breadboard.
* Cablagem de jumper 6 F/M.
* Um diffused LED 10 mm.


> [!NOTE] 
Estes itens são opcionais, porque o suporte de exemplo de código simulated dados de sensores.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Configurar Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalar o sistema de operativo Raspbian para Pi

Prepare o cartão microSD para a instalação da imagem Raspbian.

1. Transferir Raspbian.
   1. [Transferir Raspbian Jessie com ambiente de trabalho](https://www.raspberrypi.org/downloads/raspbian/) (o ficheiro. zip).
   1. Extrair a imagem de Raspbian para uma pasta no seu computador.
1. Instale Raspbian para o cartão microSD.
   1. [Transfira e instale o utilitário de burner cartão Etcher SD](https://etcher.io/).
   1. Execute Etcher e selecione a imagem de Raspbian extraiu no passo 1.
   1. Selecione a unidade de cartão microSD. Tenha em atenção que Etcher poderá já selecionou correta da unidade.
   1. Clique em Flash instalar Raspbian para o cartão microSD.
   1. Remova o cartão microSD do seu computador quando a instalação estiver concluída. É seguro remover o cartão microSD diretamente porque Etcher automaticamente ejects ou unmounts o cartão microSD após a conclusão.
   1. Inserir o cartão microSD Pi.

### <a name="enable-ssh-and-spi"></a>Ativar o SSH e SPI

1. Ligar Pi para o monitor, teclado e rato, inicie o instalador de plataforma e, em seguida, inicie sessão no Raspbian utilizando `pi` como o nome de utilizador e `raspberry` como a palavra-passe.
1. Clique no ícone de Raspberry > **preferências** > **Raspberry Pi configuração**.

   ![O menu de Raspbian preferências](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. No **Interfaces** separador, defina **SPI** e **SSH** para **ativar**e, em seguida, clique em **OK**. Se não tiver sensores físicos e pretende utilizar dados de sensores simulados, este passo é opcional.

   ![Ativar SPI e SSH no Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Para ativar SSH e SPI, pode encontrar mais de referência de documentos no [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Ligar o sensor a Pi

Utilize a cablagem breadboard e jumper para ligar um LED e um BME280 a Pi da seguinte forma. Se não tiver o sensor [ignorar esta secção](#connect-pi-to-the-network).

![A ligação Raspberry Pi e sensor](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

O sensor BME280 pode recolher dados relativos à temperatura e humidade. E o LED será blink se houver uma comunicação entre o dispositivo e a nuvem. 

Para sensor pins, utilize o wiring seguinte:

| Iniciar (Sensor & LED)     | Fim (quadro)            | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Pin 5g)         | GPIO 4 (Pin 7)         | Cabo branco   |
| LED GND (Pin 6G)         | GND (Pin 6)            | Cabo preto   |
| VDD (Pin 18F)            | 3.3V PWR (Pin 17)      | Cabo branco   |
| GND (Pin 20F)            | GND (Pin 20)           | Cabo preto   |
| SCK (Pin 21F)            | SPI0 SCLK (Pin 23)     | Cabo laranja  |
| SDO (Pin 22F)            | SPI0 MISO (Pin 21)     | Cabo amarelo  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Cabo verde   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Cabo azul    |

Clique para ver [Raspberry Pi 2 e 3 mapeamentos de Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para referência.

Depois de ter ligado com êxito o BME280 ao seu Raspberry Pi, devem ser como abaixo imagem.

![Instalador de plataforma ligado e BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Ligar Pi à rede

Ative a Pi utilizando o micro cabo USB e a fonte de alimentação. Utilize o cabo de Ethernet para ligar Pi à sua rede com fios ou seguir o [instruções da base do Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) ligar Pi à sua rede sem fios. Depois da Pi foi ligada com êxito à rede, precisa de tomar nota do [endereço IP do seu Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Ligado à rede com fios](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Executar uma aplicação de exemplo no instalador de plataforma

### <a name="login-to-your-raspberry-pi"></a>Inicie sessão no seu Raspberry Pi

1. Utilize um dos seguintes clientes SSH do seu computador anfitrião para ligar ao seu Raspberry Pi.
   
   **Utilizadores do Windows**
   1. Transfira e instale [PuTTY](http://www.putty.org/) para Windows. 
   1. Copie o endereço IP do seu secção de instalador de plataforma para o nome de anfitrião (ou endereço IP) e selecione SSH como o tipo de ligação.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac e Ubuntu utilizadores**
   
   Utilize o cliente SSH incorporado no Ubuntu ou macOS. Poderá ter de executar `ssh pi@<ip address of pi>` ligar Pi através de SSH.
   > [!NOTE] 
   O nome de utilizador predefinido é `pi` , e a palavra-passe é `raspberry`.


### <a name="configure-the-sample-application"></a>Configurar a aplicação de exemplo

1. Clone a aplicação de exemplo executando o seguinte comando:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Execute o script de configuração:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Se lhe **não tem um BME280 físico**, pode utilizar '-simulated-data' como parâmetro de linha de comandos para simular dados temperatura e humidade. `sudo ./setup.sh --simulated-data`

### <a name="build-and-run-the-sample-application"></a>Compilar e executar a aplicação de exemplo

1. Crie a aplicação de exemplo, executando o seguinte comando:

   ```bash
   cmake . && make
   ```
   ![Saída de compilação](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Execute a aplicação de exemplo, executando o seguinte comando:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Certifique-se de que copiar-colar a cadeia de ligação do dispositivo para as plicas.


Deverá ver o seguinte resultado que mostra os dados de sensor e as mensagens que são enviadas para o seu IoT hub.

![Saída - dados de sensor enviados do Raspberry Pi ao seu IoT hub](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Passos seguintes

Tiver de executar um exemplo de aplicação para recolher dados de sensores e enviá-lo ao seu IoT hub. Para ver as mensagens que o Raspberry Pi foi enviado para os IoT hub ou enviar mensagens sua Raspberry Pi numa interface de linha de comandos, consulte o [gerir dispositivos de cloud messaging com tutorial de iothub Explorador](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
