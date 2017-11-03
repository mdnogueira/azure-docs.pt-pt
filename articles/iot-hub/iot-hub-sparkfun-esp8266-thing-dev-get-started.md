---
title: ESP8266 na nuvem - ligar Sparkfun ESP8266 coisa Dev ao IoT Hub do Azure | Microsoft Docs
description: Saiba como configurar e ligar Sparkfun ESP8266 coisa Dev ao IoT Hub do Azure para o mesmo para enviar dados para a plataforma de nuvem do Azure, neste tutorial.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.openlocfilehash: 557f0cdf375b345e0dbe0526f5a5bd3c050dec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Ligar Sparkfun ESP8266 coisa Dev ao IoT Hub do Azure na nuvem

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![ligação entre DHT22, coisa Dev e IoT Hub](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>O que irá fazer

Ligar Sparkfun ESP8266 coisa Dev a um IoT hub, irá criar. Em seguida, execute um exemplo de aplicação no ESP8266 para recolher dados relativos à temperatura e humidade de um sensor DHT22. Por fim, envie os dados de sensor ao seu IoT hub.

> [!NOTE]
> Se estiver a utilizar outros quadros ESP8266, ainda pode seguir estes passos para estabelecer a ligação ao seu IoT hub. Consoante o Conselho ESP8266 estiver a utilizar, poderá ter de reconfigurar o `LED_PIN`. Por exemplo, se estiver a utilizar ESP8266 de AI Thinker, pode alterá-la de `0` para `2`. Ainda não tem um kit?: clique em [aqui](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>O que aprenderá

* Como criar um hub IoT e registar um dispositivo para efeitos de coisa
* Como ligar Dev coisa com o sensor e o seu computador.
* Como recolher dados de sensores ao executar uma aplicação de exemplo no coisa efeitos
* Como enviar os dados de sensor ao seu IoT hub.

## <a name="what-you-will-need"></a>O que precisa

![Componentes necessários para o tutorial](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, tem as seguintes partes do seu coisa Dev Starter Kit:

* Placa Sparkfun ESP8266 coisa Dev.
* Uma USB Micro a cabo USB de um tipo.

É também necessário o seguinte para o seu ambiente de desenvolvimento:

* Uma subscrição ativa do Azure. Se não tiver uma conta do Azure, [criar uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Mac ou PC com Windows ou Ubuntu.
* Rede sem fios para Sparkfun ESP8266 coisa Dev ligar a.
* Ligação à Internet para transferir a ferramenta de configuração.
* [Arduino IDE](https://www.arduino.cc/en/main/software) versão 1.6.8 (ou mais recentes), versões anteriores não irão funcionar com a biblioteca de AzureIoT.

Os seguintes itens são opcionais no caso de não ter um sensor. Também tem a opção de utilização de dados de sensores simulados.

* Sensor de temperatura e humidade um Adafruit DHT22.
* Um breadboard.
* Cablagem jumper M/M.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Ligar ESP8266 coisa Dev com o sensor e o seu computador

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Ligar um sensor de temperatura e humidade DHT22 ESP8266 coisa Dev

Utilize a cablagem breadboard e jumper para efetuar a ligação da seguinte forma. Se não tiver um sensor, ignore esta secção dado que pode utilizar os dados de sensores simulados em vez disso.

![Referência de ligações](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Para sensor pins, utilizamos o wiring seguinte:

| Iniciar (Sensor)           | Fim (quadro)           | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27F)            | 3 v (Pin 8A)           | Cabo vermelho     |
| DADOS de (Pin 28F)           | GPIO 2 (Pin 9A)       | Cabo branco    |
| GND (Pin 30F)            | GND (Pin 7J)          | Cabo preto   |


- Para obter mais informações, consulte: [a configuração do sensor DHT22](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) e [Sparkfun ESP8266 coisa Dev especificação](https://www.sparkfun.com/products/13711)

Agora, o Programador de coisa ESP8266 Sparkfun devem estar ligada com um sensor de trabalho.

![ligar dht22 com ESP8266 coisa Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Ligar Sparkfun ESP8266 coisa Dev para o seu computador

Utilize o USB Micro a cabo USB de um tipo para estabelecer a ligação Sparkfun ESP8266 coisa Dev para o seu computador da seguinte forma.

![ligar feather huzzah para o seu computador](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Adicione permissões de porta série – Ubuntu apenas

Se utilizar Ubuntu, certifique-se que um utilizador normal tem as permissões para funcionar no USB porta de Sparkfun ESP8266 coisa efeitos Para adicionar permissões de porta série de um utilizador normal, siga estes passos:

1. Execute os seguintes comandos um terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Obter uma das saídas seguintes:

   * crw-rw---1 raiz uucp xxxxxxxx
   * crw-rw---1 raiz dialout xxxxxxxx

   Na saída, repare `uucp` ou `dialout` que é o nome do proprietário grupo da porta USB.

1. Adicione o utilizador ao grupo executando o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>`é o nome de proprietário do grupo que obteve no passo anterior. `<username>`é o nome de utilizador Ubuntu.

1. Terminar Ubuntu e inicie sessão-lo novamente para a alteração produza efeito.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Recolher dados de sensores e enviá-lo ao seu IoT hub

Nesta secção, implementar e executar uma aplicação de exemplo no Sparkfun ESP8266 coisa efeitos A aplicação de exemplo fica intermitente LED no Sparkfun ESP8266 coisa Dev e envia os dados relativos à temperatura e humidade recolhidos a partir do sensor DHT22 ao seu IoT hub.

### <a name="get-the-sample-application-from-github"></a>Obter a aplicação de exemplo a partir do GitHub

A aplicação de exemplo está alojada no GitHub. Clone o repositório de exemplo que contém a aplicação de exemplo a partir do GitHub. Para clonar o repositório de exemplo, siga estes passos:

1. Abra uma linha de comandos ou de uma janela de terminal.
1. Ir para uma pasta onde pretende que a aplicação de exemplo para ser armazenado.
1. Execute o seguinte comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Instale o pacote para Sparkfun ESP8266 coisa Dev no Arduino IDE:

1. Abra a pasta onde está armazenada a aplicação de exemplo.
1. Abra o ficheiro de app.ino na pasta da aplicação na Arduino IDE.

   ![Abra a aplicação de exemplo no arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. No Arduino IDE, clique em **ficheiro** > **preferências**.
1. No **preferências** caixa de diálogo caixa, clique no ícone junto a **URLs de Gestor de quadros adicionais** caixa de texto.
1. Na janela de pop-up, introduza o seguinte URL e, em seguida, clique em **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![apontar para um url de pacote no arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. No **preferência** caixa de diálogo, clique em **OK**.
1. Clique em **ferramentas** > **quadro** > **quadros Manager**e, em seguida, procure esp8266.
   ESP8266 com uma versão do 2.2.0 ou posterior deve ser instalado.

   ![O pacote de esp8266 está instalado](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Clique em **ferramentas** > **quadro** > **Sparkfun ESP8266 coisa Dev**.

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. No Arduino IDE, clique em **Sketch** > **incluem bibliotecas** > **gerir bibliotecas**.
1. Pesquisa para a biblioteca seguinte nomes um por um. Para cada uma das bibliotecas encontrará, clique em **instalar**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Não tem um sensor DHT22 real?

A aplicação de exemplo pode simular dados relativos à temperatura e humidade no caso de não ter um sensor DHT22 real. Para ativar a aplicação de exemplo para utilizar dados simulados, siga estes passos:

1. Abra o `config.h` ficheiros o `app` pasta.
1. Localize a seguinte linha de código e altere o valor de `false` para `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurar a aplicação de exemplo para utilizar dados simulados](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Guarde com `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Implementar a aplicação de exemplo para Sparkfun ESP8266 coisa Dev

1. No Arduino IDE, clique em **ferramenta** > **porta**e, em seguida, clique na porta série para efeitos de coisa Sparkfun ESP8266
1. Clique em **Sketch** > **carregar** para criar e implementar a aplicação de exemplo para efeitos de coisa Sparkfun ESP8266

> [!Note]
> Se estiver a utilizar macOS foi provavelmente verá as seguintes mensagens durante o carregamento. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Volte a abrir a janela de ternimal e concluir abaixo ações para resolver este problema.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Introduza as suas credenciais

Após a conclusão do carregamento com êxito, siga os passos para introduzir as suas credenciais:

1. No Arduino IDE, clique em **ferramentas** > **Monitor série**.
1. Na janela monitor de série, tenha em atenção as duas listas de lista pendente no canto inferior direito.
1. Selecione **terminar nenhuma linha** para a esquerda na lista pendente.
1. Selecione **transmissão 115200** para a direita na lista pendente.
1. Na caixa de entrada localizada na parte superior da janela monitor de série, introduza as seguintes informações se lhe for pedido que forneça-los e, em seguida, clique em **enviar**.
   * SSID Wi-Fi
   * Palavra-passe de Wi-Fi
   * Cadeia de ligação do dispositivo

> [!Note]
> As informações de credenciais são armazenadas no EEPROM de Sparkfun ESP8266 coisa efeitos Se clicar no botão de reposição no quadro a Sparkfun ESP8266 coisa Dev, é a aplicação de exemplo pede-lhe se pretende apagar as informações. Introduza `Y` ter as informações de apagar e é-lhe pedido para fornecer novamente as informações.

### <a name="verify-the-sample-application-is-running-successfully"></a>Certifique-se de que a aplicação de exemplo está a ser executado com êxito

Se vir o resultado seguinte da janela monitor de série e o LED intermitente no Sparkfun ESP8266 coisa programador, a aplicação de exemplo está em execução com êxito.

![resultado final no arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Passos seguintes

Ter ligado um programador de coisa ESP8266 Sparkfun ao seu IoT hub e enviados os dados de sensor capturada ao seu IoT hub com êxito. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
