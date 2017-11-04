---
title: Raspberry Pi nuvem (Python) - ligar Raspberry Pi ao IoT Hub do Azure | Microsoft Docs
description: Saiba como configurar e ligar Raspberry Pi ao Azure IoT Hub para Raspberry Pi enviar dados para a plataforma de nuvem do Azure, neste tutorial.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: Azure iot raspberry pi, iothub raspberry pi, dados de envio de raspberry pi na nuvem, raspberry pi na nuvem
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/31/2017
ms.author: xshi
ms.openlocfilehash: 1b1a9dc960846cbc15ce09d0fd106e1492937439
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-python"></a>Ligar Raspberry Pi ao IoT Hub do Azure (Python)

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

## <a name="set-up-raspberry-pi"></a>Configurar Raspberry Pi

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

### <a name="enable-ssh-and-i2c"></a>Ativar o SSH e I2C

1. Ligar Pi para o monitor, teclado e rato, inicie o instalador de plataforma e, em seguida, inicie sessão no Raspbian utilizando `pi` como o nome de utilizador e `raspberry` como a palavra-passe.
1. Clique no ícone de Raspberry > **preferências** > **Raspberry Pi configuração**.

   ![O menu de Raspbian preferências](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. No **Interfaces** separador, defina **I2C** e **SSH** para **ativar**e, em seguida, clique em **OK**. Se não tiver sensores físicos e pretende utilizar dados de sensores simulados, este passo é opcional.

   ![Ativar I2C e SSH no Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Para ativar SSH e I2C, pode encontrar mais de referência de documentos no [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Ligar o sensor a Pi

Utilize a cablagem breadboard e jumper para ligar um LED e um BME280 a Pi da seguinte forma. Se não tiver o sensor [ignorar esta secção](#connect-pi-to-the-network).

![A ligação Raspberry Pi e sensor](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

O sensor BME280 pode recolher dados relativos à temperatura e humidade. E o LED será blink se houver uma comunicação entre o dispositivo e a nuvem. 

Para sensor pins, utilize o wiring seguinte:

| Iniciar (Sensor & LED)     | Fim (quadro)            | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5g)             | 3.3V PWR (Pin 1)       | Cabo branco   |
| GND (Pin 7G)             | GND (Pin 6)            | Cabo castanha   |
| SDI (Pin 10g)            | I2C1 SDA (Pin 3)       | Cabo vermelho     |
| SCK (Pin 8G)             | I2C1 SCL (Pin 5)       | Cabo laranja  |
| LED VDD (Pin 18F)        | GPIO 24 (Pin 18)       | Cabo branco   |
| LED GND (Pin 17F)        | GND (Pin 20)           | Cabo preto   |

Clique para ver [Raspberry Pi 2 e 3 mapeamentos de Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para referência.

Depois de ter ligado com êxito o BME280 ao seu Raspberry Pi, devem ser como abaixo imagem.

![Instalador de plataforma ligado e BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Ligar Pi à rede

Ative a Pi utilizando o micro cabo USB e a fonte de alimentação. Utilize o cabo de Ethernet para ligar Pi à sua rede com fios ou seguir o [instruções da base do Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) ligar Pi à sua rede sem fios. Depois da Pi foi ligada com êxito à rede, precisa de tomar nota do [endereço IP do seu Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Ligado à rede com fios](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Certifique-se de que o Pi está ligado à mesma rede que o computador. Por exemplo, se o computador está ligado a uma rede sem fios enquanto Pi está ligado a uma rede com fios, não poderá ver o endereço IP na saída devdisco.

## <a name="run-a-sample-application-on-pi"></a>Executar uma aplicação de exemplo no instalador de plataforma

### <a name="install-the-prerequisite-packages"></a>Instalar os pacotes de pré-requisitos

Utilize um dos seguintes clientes SSH do seu computador anfitrião para ligar ao seu Raspberry Pi.
   
   **Utilizadores do Windows**
   1. Transfira e instale [PuTTY](http://www.putty.org/) para Windows. 
   1. Copie o endereço IP do seu secção de instalador de plataforma para o nome de anfitrião (ou endereço IP) e selecione SSH como o tipo de ligação.
   
   
   **Mac e Ubuntu utilizadores**
   
   Utilize o cliente SSH incorporado no Ubuntu ou macOS. Poderá ter de executar `ssh pi@<ip address of pi>` ligar Pi através de SSH.
   > [!NOTE] 
   O nome de utilizador predefinido é `pi` , e a palavra-passe é `raspberry`.


### <a name="configure-the-sample-application"></a>Configurar a aplicação de exemplo

1. Clone a aplicação de exemplo executando o seguinte comando:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-python-raspberrypi-client-app.git
   ```
1. Abra o ficheiro de configuração executando os seguintes comandos:

   ```bash
   cd iot-hub-python-raspberrypi-client-app
   nano config.py
   ```

   Existem 5 macros neste ficheiro, pode configurate. A primeira é `MESSAGE_TIMESPAN`, que define o intervalo de tempo (em milissegundos) entre duas mensagens que enviam para a nuvem. Segunda `SIMULATED_DATA`, que é um valor booleano para se deve utilizar dados de sensores simulados ou não. `I2C_ADDRESS`é o endereço de I2C que o sensor BME280 está ligado. `GPIO_PIN_ADDRESS`o endereço GPIO destina-se a sua LED. É o último `BLINK_TIMESPAN`, que definiu o período de tempo quando o LED está ativado em milissegundos.

   Se a **não tem o sensor**, defina o `SIMULATED_DATA` valor `True` para tornar a aplicação de exemplo, criar e utilizar dados de sensores simulados.

1. Guarde e saia, premindo O controlo > introduza > X de controlo.

### <a name="build-and-run-the-sample-application"></a>Compilar e executar a aplicação de exemplo

1. Crie a aplicação de exemplo, executando o seguinte comando. Dado que os SDKs IoT do Azure para Python wrappers sobre o SDK de C de dispositivos de IoT do Azure, terá de compilar as bibliotecas de C se pretende ou precisa gerar as bibliotecas de Python a partir do código de origem.

   ```bash
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```
   > [!NOTE] 
   Também pode especificar a versão que pretende executando `sudo ./setup.sh [--python-version|-p] [2.7|3.4|3.5]`. Se executar o script sem parâmetros, o script irá detetar automaticamente a versão do python instalado (sequência de pesquisa 2.7 -> 3.4 -> 3.5). Certifique-se a sua versão do Python mantém consistente durante a criação e em execução. 
   
   > [!NOTE] 
   Sobre como criar a biblioteca de clientes do Python (iothub_client.so) em dispositivos de Linux que tenham menos de 1GB de RAM, poderá ver compilar a acumular 98% durante a criação iothub_client_python.cpp conforme mostrado abaixo `[ 98%] Building CXX object python/src/CMakeFiles/iothub_client_python.dir/iothub_client_python.cpp.o`. Caso se depare com este problema, verifique o consumo de memória do dispositivo utilizar `free -m command` na outra janela de terminal durante esse período. Se estiver a ficar sem memória ao compilar ficheiros iothub_client_python.cpp, poderá ter temporariamente aumentar o espaço de comutação para obter mais memória disponível para criar com êxito o dispositivo do lado do cliente do Python biblioteca do SDK.
   
1. Execute a aplicação de exemplo, executando o seguinte comando:

   ```bash
   python app.py '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Certifique-se de que copiar-colar a cadeia de ligação do dispositivo para as plicas. E se utiliza o python 3, em seguida, pode utilizar o comando `python3 app.py '<your Azure IoT hub device connection string>'`.


   Deverá ver o seguinte resultado que mostra os dados de sensor e as mensagens que são enviadas para o seu IoT hub.

   ![Saída - dados de sensor enviados do Raspberry Pi ao seu IoT hub](media/iot-hub-raspberry-pi-kit-c-get-started/success.png
)

## <a name="next-steps"></a>Passos seguintes

Tiver de executar um exemplo de aplicação para recolher dados de sensores e enviá-lo ao seu IoT hub. Para ver as mensagens que o Raspberry Pi foi enviado para os IoT hub ou enviar mensagens sua Raspberry Pi numa interface de linha de comandos, consulte o [gerir dispositivos de cloud messaging com tutorial de iothub Explorador](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
