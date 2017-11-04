---
title: 'M0 na nuvem: Ligar Wi-Fi do Feather M0 ao IoT Hub do Azure | Microsoft Docs'
description: Saiba como configurar e ligar Adafruit Feather M0 Wi-Fi para o Azure IoT Hub para enviar dados para a plataforma de nuvem do Azure, neste tutorial.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/16/2017
ms.author: xshi
ms.openlocfilehash: 9b278735ce3af9e6e61a85c5e95ea218622361c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Ligar Adafruit Feather M0 Wi-Fi para o IoT Hub do Azure na nuvem
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Ligação entre um BME280, Feather M0 Wi-Fi e o IoT Hub](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

Neste tutorial, comece por aprender as noções básicas do trabalho com o seu painel Arduino. Em seguida, aprenderá a forma totalmente integrada ligar os seus dispositivos para a nuvem, utilizando o [IoT Hub do Azure](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>O que fazer

Ligar Adafruit Feather M0 Wi-Fi a um IoT hub que criou. Em seguida, execute um exemplo de aplicação no Wi-Fi de M0 para recolher os dados relativos à temperatura e humidade de um BME280. Por fim, enviar os dados de sensor ao seu IoT hub.


## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT e registar um dispositivo para M0 de Feather Wi-Fi
* Como ligar M0 de Feather Wi-Fi com o sensor e o seu computador
* Como recolher dados de sensores ao executar uma aplicação de exemplo no M0 de Feather Wi-Fi
* Como enviar os dados de sensor ao seu IoT hub

## <a name="what-you-need"></a>Do que precisa

![Componentes necessários para o tutorial](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, tem as seguintes partes do seu Feather M0 Wi-Fi Starter Kit:

* O quadro M0 de Feather Wi-Fi
* Uma USB Micro a cabo USB de um tipo

Também tem os seguintes procedimentos para o seu ambiente de desenvolvimento:

* Uma subscrição ativa do Azure. Se não tiver uma conta do Azure, [criar uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um Mac ou um PC com Windows ou Ubuntu.
* Uma rede sem fios para Feather o M0 Wi-Fi ligar a.
* Uma ligação à Internet para transferir a ferramenta de configuração.
* [Arduino IDE](https://www.arduino.cc/en/main/software) versão 1.6.8 ou posterior. Versões anteriores não funcionam com a biblioteca do IoT Hub do Azure.

Se não tiver um sensor, os itens seguintes são opcionais. Tem também a opção de utilização de dados de sensores simulados:

* Um sensor de temperatura e humidade BME280
* Um breadboard
* Cablagem jumper M/M

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Ligar M0 de Feather Wi-Fi com o sensor e o seu computador
Nesta secção, ligar os sensores a sua placa. Em seguida, tem de plug-in do dispositivo para o seu computador para utilização mais.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Ligar um sensor de temperatura e humidade DHT22 M0 de Feather Wi-Fi

Utilize a cablagem breadboard e jumper para efetuar a ligação. Se não tiver um sensor, ignore esta secção dado que pode utilizar os dados de sensores simulados em vez disso.

![Referência de ligações](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Para sensor pins, utilize o wiring seguinte:


| Início (sensor)           | Fim (quadro)            | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27A)            | 3 v (Pin 3A)            | Cabo vermelho     |
| GND (Pin 29A)            | GND (Pin 6A)           | Cabo preto   |
| SCK (Pin 30A)            | SCK (Pin 12A)          | Cabo amarelo  |
| SDO (Pin 31A)            | MI (Pin 14A)           | Cabo branco   |
| SDI (Pin 32A)            | M0 (Pin 13A)           | Cabo azul    |
| CS (Pin 33A)             | GPIO 5 (Pin 15J)       | Cabo laranja  |

Para obter mais informações, consulte [Adafruit BME280 humidade + pressão Barometric + Breakout do Sensor de temperatura](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) e [Adafruit Feather M0 Wi-Fi pinouts](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Agora, deve estar ligada a Wi-Fi do Feather M0 com um sensor de trabalho.

![Ligar DHT22 com Feather Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Ligar M0 de Feather Wi-Fi para o seu computador

Utilize o USB Micro a cabo USB de um tipo para estabelecer a ligação Wi-Fi do Feather M0 para o seu computador, conforme mostrado:

![Ligar Feather Huzzah para o seu computador](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adicione permissões de porta série (apenas Ubuntu)

Se utilizar Ubuntu, certifique-se de que tem as permissões para funcionar no USB porta de Feather M0 Wi-Fi. Para adicionar permissões de porta série, siga estes passos:


1. Um terminal, execute os seguintes comandos:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Obter uma das saídas seguintes:

   * crw-rw---1 raiz uucp xxxxxxxx
   * crw-rw---1 raiz dialout xxxxxxxx

   Na saída, repare que `uucp` ou `dialout` é o nome de proprietário do grupo da porta USB.

2. Para adicionar o utilizador ao grupo, execute o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   No passo anterior, que obteve o nome de proprietário do grupo `<group-owner-name>`. O nome de utilizador Ubuntu for `<username>`.

3. Para que a alteração aparecer, termine sessão Ubuntu e, em seguida, inicie sessão novamente.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Recolher dados de sensores e enviá-lo ao seu IoT hub

Nesta secção, implementar e executar uma aplicação de exemplo no M0 de Feather Wi-Fi. A aplicação de exemplo torna o blink LED no M0 de Feather Wi-Fi. Em seguida, envia os dados relativos à temperatura e humidade recolhidos a partir do sensor BME280 ao seu IoT hub.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>Obter a aplicação de exemplo a partir do GitHub e preparar o IDE Arduino

A aplicação de exemplo está alojada no GitHub. Clone o repositório de exemplo que contém a aplicação de exemplo a partir do GitHub. Para clonar o repositório de exemplo, siga estes passos:

1. Abra uma linha de comandos ou de uma janela de terminal.

2. Ir para uma pasta onde pretende que a aplicação de exemplo para ser armazenado.
3. Execute o seguinte comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Instalar o pacote para Feather M0 Wi-Fi no Arduino IDE

1. Abra a pasta onde está armazenada a aplicação de exemplo.

2. Abra o ficheiro de app.ino na pasta da aplicação na Arduino IDE.

   ![Abra a aplicação de exemplo no Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Clique em **ficheiro** > **preferências** (Windows/Linux) ou **Arduino** > **preferências** (Mac) e copie e cole a hiperligação abaixo para o **adicionais URLs de Gestor de quadros** opção nas preferências de Arduino IDE.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Clique em **ferramentas** > **quadro** > **quadros Manager**e, em seguida, instale o `Arduino SAMD Boards` versão `1.6.2` ou posterior. 

1. Em seguida, na janela do mesma, instale `Adafruit SAMD Boards` pacote para adicionar as definições de ficheiro do quadro.

   ![O pacote de esp8266 está instalado](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Clique em **ferramentas** > **quadro** > **Wi-Fi do Adafruit M0**.

5. Instale controladores (para Windows apenas). Quando tiver de plug-in M0 de Feather Wi-Fi, poderá ter de instalar um controlador. Clique em [na ligação de transferência na página Web](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) para transferir o instalador de controlador. Siga os passos para instalar os controladores que pretende.

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. No Arduino IDE, clique em **Sketch** > **incluem bibliotecas** > **gerir bibliotecas**.

2. Pesquisa para a biblioteca seguinte nomes um por um. Para cada biblioteca que encontrar, clique em **instalar**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Instale manualmente o `Adafruit_WINC1500`. Aceda a [este Web site](https://github.com/adafruit/Adafruit_WINC1500) e clique em **clonar ou transferir** > **transferir ZIP**. Em seguida, na sua IDE Arduino, aceda a **Sketch** > **incluem bibliotecas** > **adicionar. zip biblioteca** e adicione o ficheiro zip.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>Utilizar a aplicação de exemplo, se não tiver um sensor BME280 real

Se não tiver um sensor BME280 real, a aplicação de exemplo pode simular dados relativos à temperatura e humidade. Para configurar a aplicação de exemplo para utilizar dados simulados, siga estes passos:

1. Abra o `config.h` ficheiros o `app` pasta.

2. Localize a seguinte linha de código e altere o valor de `false` para `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![Configurar a aplicação de exemplo para utilizar dados simulados](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Guarde o ficheiro com `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Implementar a aplicação de exemplo para M0 de Feather Wi-Fi

1. No Arduino IDE, clique em **ferramenta** > **porta**e, em seguida, clique na porta série para M0 de Feather Wi-Fi.

2. Clique em **Sketch** > **carregar** para criar e implementar a aplicação de exemplo para M0 de Feather Wi-Fi.

### <a name="enter-your-credentials"></a>Introduza as suas credenciais

Após a conclusão do carregamento com êxito, siga estes passos para introduzir as suas credenciais:

1. No Arduino IDE, clique em **ferramentas** > **Monitor série**.

2. No canto inferior direito da janela monitor de série, selecione **terminar nenhuma linha** na lista pendente à esquerda.
3. Selecione **transmissão 115200** na lista pendente à direita.
4. Na caixa de entrada na parte superior, introduza as seguintes informações se estiver a ser pedido para proporcionar e clique em **enviar**:

   * SSID Wi-Fi
   * Palavra-passe de Wi-Fi
   * Cadeia de ligação do dispositivo

> [!Note]
> As informações de credenciais são armazenadas do Wi-Fi do EEPROM Feather M0. Se clicar no botão de reposição no quadro de Wi-Fi do Feather M0, a aplicação de exemplo pergunta se pretende apagar as informações. Introduza `Y` para apagar as informações. Está a pedido para fornecer as informações de uma segunda vez.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Certifique-se de que a aplicação de exemplo está em execução com êxito

Se vir o resultado seguinte da janela monitor de série e o LED intermitente no M0 de Feather Wi-Fi, a aplicação de exemplo é executada com êxito:

![Resultado final no Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Passos seguintes

Ter ligado Wi-Fi do Feather M0 ao seu IoT hub e enviados os dados de sensor capturada ao seu IoT hub com êxito. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

