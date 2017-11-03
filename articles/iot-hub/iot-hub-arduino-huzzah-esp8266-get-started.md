---
title: ESP8266 na nuvem - ligar Feather HUZZAH ESP8266 ao IoT Hub do Azure | Microsoft Docs
description: Saiba como configurar e ligar Adafruit Feather HUZZAH ESP8266 ao IoT Hub do Azure para o mesmo para enviar dados para a plataforma de nuvem do Azure, neste tutorial.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: xshi
ms.openlocfilehash: 6a450579c848fe6030a328ddf410f139baae2324
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Ligar Adafruit Feather HUZZAH ESP8266 ao IoT Hub do Azure na nuvem

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Ligação entre DHT22, Feather HUZZAH ESP8266 e IoT Hub](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>O que fazer


Ligar Adafruit Feather HUZZAH ESP8266 a um IoT hub que criou. Em seguida, execute um exemplo de aplicação no ESP8266 para recolher os dados relativos à temperatura e humidade de um sensor DHT22. Por fim, enviar os dados de sensor ao seu IoT hub.

> [!NOTE]
> Se estiver a utilizar outros quadros ESP8266, ainda pode seguir estes passos para estabelecer a ligação ao seu IoT hub. Consoante o Conselho ESP8266 estiver a utilizar, poderá ter de reconfigurar o `LED_PIN`. Por exemplo, se estiver a utilizar ESP8266 de AI Thinker, poderá alterar a `0` para `2`. Não tem um kit ainda? Obtido a partir de [Web site Azure](http://azure.com/iotstarterkits).




## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT e registar um dispositivo para Feather HUZZAH ESP8266
* Como ligar Feather HUZZAH ESP8266 com o sensor e o seu computador
* Como recolher dados de sensores ao executar uma aplicação de exemplo no Feather HUZZAH ESP8266
* Como enviar os dados de sensor ao seu IoT hub

## <a name="what-you-need"></a>Do que precisa

![Componentes necessários para o tutorial](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, tem as seguintes partes do seu Feather HUZZAH ESP8266 Starter Kit:

* O quadro Feather HUZZAH ESP8266
* Uma USB Micro a cabo USB de um tipo

Também tem os seguintes procedimentos para o seu ambiente de desenvolvimento:

* Uma subscrição ativa do Azure. Se não tiver uma conta do Azure, [criar uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Mac ou PC com Windows ou Ubuntu.
* Rede sem fios para Feather HUZZAH ESP8266 ligar a.
* Ligação à Internet para transferir a ferramenta de configuração.
* [Arduino IDE](https://www.arduino.cc/en/main/software) versão 1.6.8 ou posterior. Versões anteriores não funcionam com a biblioteca de AzureIoT.

Os seguintes itens são opcionais no caso de não ter um sensor. Também tem a opção de utilização de dados de sensores simulados.

* Um sensor de temperatura e humidade Adafruit DHT22
* Um breadboard
* Cablagem jumper M/M


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Ligar Feather HUZZAH ESP8266 com o sensor e o seu computador
Nesta secção, ligar os sensores a sua placa. Em seguida, tem de plug-in do dispositivo para o seu computador para utilização mais.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Ligar um sensor de temperatura e humidade DHT22 Feather HUZZAH ESP8266

Utilize a cablagem breadboard e jumper para efetuar a ligação da seguinte forma. Se não tiver um sensor, ignore esta secção dado que pode utilizar os dados de sensores simulados em vez disso.

![Referência de ligações](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)


Para sensor pins, utilize o wiring seguinte:


| Iniciar (Sensor)           | Fim (quadro)           | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 31F)            | 3 v (afixar 58H)           | Cabo vermelho     |
| DADOS de (Pin 32F)           | GPIO 2 (Pin 46A)       | Cabo azul    |
| GND (Pin 34F)            | GND (PIn 56I)          | Cabo preto   |

Para obter mais informações, consulte [a configuração do sensor Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) e [Adafruit Feather HUZZAH Esp8266 Pinouts](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).



Agora a sua ESP8266 de Huzzah Feather devem estar ligadas com um sensor de trabalho.

![Ligar DHT22 com Feather Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Ligar Feather HUZZAH ESP8266 para o seu computador

Como é mostrado seguinte, utilize o USB Micro a cabo USB de um tipo para ligar Feather HUZZAH ESP8266 para o seu computador.

![Ligar Feather Huzzah para o seu computador](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adicione permissões de porta série (apenas Ubuntu)


Se utilizar Ubuntu, certifique-se de que tem as permissões para funcionar no USB porta de Feather HUZZAH ESP8266. Para adicionar permissões de porta série, siga estes passos:


1. Execute os seguintes comandos um terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Obter uma das saídas seguintes:

   * crw-rw---1 raiz uucp xxxxxxxx
   * crw-rw---1 raiz dialout xxxxxxxx

   Na saída, repare que `uucp` ou `dialout` é o nome de proprietário do grupo da porta USB.

1. Adicione o utilizador ao grupo executando o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>`é o nome de proprietário do grupo que obteve no passo anterior. `<username>`é o nome de utilizador Ubuntu.

1. Terminar a sessão do Ubuntu e, em seguida, inicie sessão novamente para que a alteração apareçam.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Recolher dados de sensores e enviá-lo ao seu IoT hub

Nesta secção, implementar e executar uma aplicação de exemplo no Feather HUZZAH ESP8266. A aplicação de exemplo fica intermitente LED no Feather HUZZAH ESP8266 e envia os dados relativos à temperatura e humidade recolhidos a partir do sensor DHT22 ao seu IoT hub.

### <a name="get-the-sample-application-from-github"></a>Obter a aplicação de exemplo a partir do GitHub

A aplicação de exemplo está alojada no GitHub. Clone o repositório de exemplo que contém a aplicação de exemplo a partir do GitHub. Para clonar o repositório de exemplo, siga estes passos:

1. Abra uma linha de comandos ou de uma janela de terminal.
1. Ir para uma pasta onde pretende que a aplicação de exemplo para ser armazenado.
1. Execute o seguinte comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Instale o pacote para Feather HUZZAH ESP8266 no Arduino IDE:

1. Abra a pasta onde está armazenada a aplicação de exemplo.
1. Abra o ficheiro de app.ino na pasta da aplicação na Arduino IDE.

   ![Abra a aplicação de exemplo no Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. No Arduino IDE, clique em **ficheiro** > **preferências**.
1. No **preferências** caixa de diálogo caixa, clique no ícone junto a **URLs de Gestor de quadros adicionais** caixa.
1. Na janela de pop-up, introduza o seguinte URL e, em seguida, clique em **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Apontar para um url de pacote no Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. No **preferência** caixa de diálogo, clique em **OK**.
1. Clique em **ferramentas** > **quadro** > **quadros Manager**e, em seguida, procure esp8266.

   Gestor de quadros indica que ESP8266 com uma versão do 2.2.0 ou posterior está instalado.

   ![O pacote de esp8266 está instalado](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

1. Clique em **ferramentas** > **quadro** > **Adafruit HUZZAH ESP8266**.

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. No Arduino IDE, clique em **Sketch** > **incluem bibliotecas** > **gerir bibliotecas**.
1. Pesquisa para a biblioteca seguinte nomes um por um. Para cada biblioteca que encontrar, clique em **instalar**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Não tem um sensor DHT22 real?

A aplicação de exemplo pode simular dados relativos à temperatura e humidade no caso de não ter um sensor DHT22 real. Para configurar a aplicação de exemplo para utilizar dados simulados, siga estes passos:

1. Abra o `config.h` ficheiros o `app` pasta.
1. Localize a seguinte linha de código e altere o valor de `false` para `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurar a aplicação de exemplo para utilizar dados simulados](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)

1. Guarde o ficheiro com `Control-s`.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Implementar a aplicação de exemplo para Feather HUZZAH ESP8266

1. No Arduino IDE, clique em **ferramenta** > **porta**e, em seguida, clique na porta série para Feather HUZZAH ESP8266.
1. Clique em **Sketch** > **carregar** para criar e implementar a aplicação de exemplo para Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Introduza as suas credenciais

Após a conclusão do carregamento com êxito, siga estes passos para introduzir as suas credenciais:

1. No Arduino IDE, clique em **ferramentas** > **Monitor série**.
1. Na janela monitor de série, tenha em atenção as duas listas de lista pendente no canto inferior direito.
1. Selecione **terminar nenhuma linha** para a esquerda na lista pendente.
1. Selecione **transmissão 115200** para a direita na lista pendente.
1. Na caixa de entrada localizada na parte superior da janela monitor de série, introduza as seguintes informações se lhe for pedido que forneça-los e, em seguida, clique em **enviar**.
   * SSID Wi-Fi
   * Palavra-passe de Wi-Fi
   * Cadeia de ligação do dispositivo

> [!Note]
> As informações de credenciais são armazenadas no EEPROM de Feather HUZZAH ESP8266. Se clicar no botão de reposição no quadro a Feather HUZZAH ESP8266, a aplicação de exemplo pergunta se pretende apagar as informações. Introduza `Y` para ter a informação de apagar. É-lhe pedido para fornecer as informações de uma segunda vez.

### <a name="verify-the-sample-application-is-running-successfully"></a>Certifique-se de que a aplicação de exemplo está a ser executado com êxito

Se vir o resultado seguinte da janela monitor de série e o LED intermitente no Feather HUZZAH ESP8266, a aplicação de exemplo está em execução com êxito.

![Resultado final no Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Passos seguintes

Com êxito ter ligado um ESP8266 de HUZZAH Feather ao seu IoT hub e enviados os dados de sensor capturada ao seu IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

