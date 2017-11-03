---
title: Intel Edison nuvem (Node.js) - ligar o Intel Edison ao IoT Hub do Azure | Microsoft Docs
description: Saiba como configurar e ligar Intel Edison ao Azure IoT Hub para Intel Edison enviar dados para a plataforma de nuvem do Azure, neste tutorial.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: iot do Azure intel edison, intel iothub edison, intel edison enviar dados para a nuvem, intel edison na nuvem
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/15/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a31efba704045196b5563f7bc467c773bea7805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-intel-edison-to-azure-iot-hub-nodejs"></a>Ligar Intel Edison ao IoT Hub do Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, comece por aprender as noções básicas de trabalhar com Intel Edison. Em seguida, aprenderá a forma totalmente integrada ligar os seus dispositivos para a nuvem, utilizando o [IoT Hub do Azure](iot-hub-what-is-iot-hub.md).

Não tem um kit ainda? Iniciar [aqui](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>O que fazer

* Intel Edison o programa de configuração e e Grove módulos.
* Crie um hub IoT.
* Registe um dispositivo para Edison no seu IoT hub.
* Execute um exemplo de aplicação no Edison para enviar dados de sensores ao seu IoT hub.

Ligar Intel Edison a um IoT hub que criou. Em seguida, execute um exemplo de aplicação no Edison para recolher dados relativos à temperatura e humidade de um sensor de temperatura Grove. Por fim, enviar os dados de sensor ao seu IoT hub.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT do Azure e obter a cadeia de ligação do novo dispositivo.
* Como ligar Edison com um sensor de temperatura Grove.
* Como recolher dados de sensores ao executar uma aplicação de exemplo no Edison.
* Como enviar dados de sensores ao seu IoT hub.

## <a name="what-you-need"></a>Do que precisa

![Do que precisa](media/iot-hub-intel-edison-kit-node-get-started/0_kit.png)

* O quadro Intel Edison
* Placa de expansão Arduino
* Uma subscrição ativa do Azure. Se não tiver uma conta do Azure, [criar uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um Mac ou num PC com Windows ou Linux.
* Uma ligação à Internet.
* A B Micro a cabo USB de um tipo
* Alimentação direta atual (DC). Fonte de alimentação deve ser classificado da seguinte forma:
  - 7 15V DC
  - Pelo menos 1500mA
  - O pin do System center/interna deve ser o pólo positivo de fonte de alimentação

Os seguintes itens são opcionais:

* Grove proteção Base V2
* Grove - Sensor de temperatura
* Grove cabo
* Qualquer barras spacer ou screws incluídos em empacotamento, incluindo dois screws para fasten o módulo para a placa de expansão e quatro conjuntos de screws e plastic spacers.

> [!NOTE] 
Estes itens são opcionais, porque o suporte de exemplo de código simulated dados de sensores.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>A configuração Intel Edison

### <a name="assemble-your-board"></a>Monte a sua placa

Esta secção contém passos para ligar o seu módulo Intel Edison de® para a placa de expansão.

1. Coloque o módulo de Intel Edison de® dentro do contorno branco no seu painel expansão, lining holes no módulo com os screws no quadro a expansão de cópia de segurança.

2. Prima para baixo no módulo imediatamente por baixo as palavras `What will you make?` até sentir um snap.

   ![Monte quadro 2](media/iot-hub-intel-edison-kit-node-get-started/1_assemble_board2.jpg)

3. Utilize os dois nuts hexadecimais (incluídos no pacote) para proteger o módulo para a placa de expansão.

   ![Monte quadro 3](media/iot-hub-intel-edison-kit-node-get-started/2_assemble_board3.jpg)

4. Inserir uma screw dos holes quatro canto no quadro a expansão. Twist e reforçar a um dos spacers plastic brancos para a screw.

   ![Monte quadro 4](media/iot-hub-intel-edison-kit-node-get-started/3_assemble_board4.jpg)

5. Repita para os outros spacers três canto.

   ![Monte quadro 5](media/iot-hub-intel-edison-kit-node-get-started/4_assemble_board5.jpg)

Agora é assembled seu painel.

   ![placa assembled](media/iot-hub-intel-edison-kit-node-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Ligar a proteção de Base Grove e o sensor de temperatura

1. Coloque a proteção de Base Grove sessão no seu painel. Certifique-se de que todos os pins totalmente são ligados à sua placa.
   
   ![Proteção de Base Grove](media/iot-hub-intel-edison-kit-node-get-started/6_grove_base_sheild.jpg)

2. Utilize o cabo de Grove para ligar o sensor de temperatura Grove para proteção de Base Grove **A0** porta.

   ![Ligar para o sensor de temperatura](media/iot-hub-intel-edison-kit-node-get-started/7_temperature_sensor.jpg)

   ![Ligação EDISON e sensor](media/iot-hub-intel-edison-kit-node-get-started/16_edion_sensor.png)

O sensor está agora pronta.

### <a name="power-up-edison"></a>Energia segurança Edison

1. Plug-in a fonte de alimentação.

   ![Plug-in fonte de alimentação](media/iot-hub-intel-edison-kit-node-get-started/8_plug_power.jpg)

2. Um LED verde (com a etiqueta DS1 no quadro de expansão Arduino *) deve ser apresentado e permaneça lit.

3. Aguarde um minuto para a placa concluir o arranque de cópia de segurança.

   > [!NOTE]
   > Se não tiver uma fonte de alimentação do DC, ainda será possível ligar a placa através de uma porta USB. Consulte `Connect Edison to your computer` secção para obter detalhes. A ligar a sua placa desta forma poderão resultar num comportamento imprevisível do seu painel, especialmente quando através de Wi-Fi ou ocasionar motors.

### <a name="connect-edison-to-your-computer"></a>Ligar Edison para o seu computador

1. Alternar para baixo microswitch para as duas portas USB micro, para que Edison está no modo de dispositivo. Para as diferenças entre o modo de anfitrião e de dispositivo, referência [aqui](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Alternar para baixo a microswitch](media/iot-hub-intel-edison-kit-node-get-started/9_toggle_down_microswitch.jpg)

2. Ligue o cabo USB micro a porta USB micro superior.

   ![Porta USB de micro superior](media/iot-hub-intel-edison-kit-node-get-started/10_top_usbport.jpg)

3. Ligue o computador outra extremidade do cabo USB.

   ![Computador USB](media/iot-hub-intel-edison-kit-node-get-started/11_computer_usb.jpg)

4. Ficará a saber o que a placa é completamente inicializada quando o computador monta uma nova unidade (muito semelhante a inserir um cartão SD, no seu computador).

## <a name="download-and-run-the-configuration-tool"></a>Transfira e execute a ferramenta de configuração
Obter a ferramenta de configuração mais recente do [esta ligação](https://software.intel.com/en-us/iot/hardware/edison/downloads) listados no `Installers` cabeçalho. Executar a ferramenta e siga o ecrã instruções, clicar em seguinte, quando necessário

### <a name="flash-firmware"></a>Flash firmware
1. No `Set up options` página, clique em `Flash Firmware`.
2. Selecione a imagem a flash no seu painel efetuando um dos seguintes:
   - Para transferir e flash seu painel com a imagem do firmware mais recente disponível a partir da Intel, selecione `Download the latest image version xxxx`.
   - Para flash seu painel com uma imagem já tiver guardado no seu computador, selecione `Select the local image`. Procure e selecione a imagem que pretende flash ao seu painel.
3. A ferramenta de configuração irá tentar flash seu painel. Todo o processo intermitente pode demorar até 10 minutos.

### <a name="set-password"></a>Definir palavra-passe
1. No `Set up options` página, clique em `Enable Security`.
2. Pode definir um nome personalizado para o seu painel Intel Edison de®. Isto é opcional.
3. Escreva uma palavra-passe para a placa, em seguida, clique em `Set password`.
4. Marcar para baixo a palavra-passe, que é utilizada mais tarde.

### <a name="connect-wi-fi"></a>Estabelecer a ligação Wi-Fi
1. No `Set up options` página, clique em `Connect Wi-Fi`. Aguarde até um minuto, como o seu computador verifica a existência de redes Wi-Fi disponíveis.
2. Do `Detected Networks` pendente lista, selecione a sua rede.
3. Do `Security` pendente lista, selecione o tipo de segurança da rede.
4. Indique as informações de início de sessão e palavra-passe e clique em `Configure Wi-Fi`.
5. Marcar para baixo o endereço IP, o que é utilizado mais tarde.

> [!NOTE]
> Certifique-se de que Edison está ligado à mesma rede que o computador. O computador liga ao seu Edison utilizando o endereço IP.

   ![Ligar para o sensor de temperatura](media/iot-hub-intel-edison-kit-node-get-started/12_configuration_tool.png)

Parabéns! Configurou com êxito Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Executar uma aplicação de exemplo no Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Preparar o dispositivo IoT do Azure SDK

1. Utilize um dos seguintes clientes SSH do seu computador anfitrião para ligar ao seu Intel Edison. O endereço IP é da ferramenta de configuração e a palavra-passe é o que definiu nessa ferramenta.
    - [PuTTY](http://www.putty.org/) para Windows.
    - O cliente SSH incorporado no Ubuntu ou macOS.

2. Clone a aplicação de cliente de exemplo para o seu dispositivo. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-client-app
   ```

3. Em seguida, navegue para a pasta de repositório para executar o seguinte comando para instalar todos os pacotes, poderá demorar serval minutos a concluir.
   
   ```bash
   cd iot-hub-node-intel-edison-client-app
   npm install
   ```


### <a name="configure-and-run-the-sample-application"></a>Configurar e executar a aplicação de exemplo

1. Abra o ficheiro de configuração executando os seguintes comandos:

   ```bash
   nano config.json
   ```

   ![Ficheiro de configuração](media/iot-hub-intel-edison-kit-node-get-started/13_configure_file.png)

   Existem dois macros neste ficheiro, pode configurate. A primeira é `INTERVAL`, que define o intervalo de tempo entre duas mensagens que enviar para a nuvem. Segunda `simulatedData`, que é um valor booleano para se deve utilizar dados de sensores simulados ou não.

   Se a **não tem o sensor**, defina o `simulatedData` valor `true` para tornar a aplicação de exemplo, criar e utilizar dados de sensores simulados.

1. Guarde e saia, premindo O controlo > introduza > X de controlo.


1. Execute a aplicação de exemplo, executando o seguinte comando:

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Certifique-se de que copiar-colar a cadeia de ligação do dispositivo para as plicas.

Deverá ver o seguinte resultado que mostra os dados de sensor e as mensagens que são enviadas para o seu IoT hub.

![Saída - dados de sensor enviados do Intel Edison ao seu IoT hub](media/iot-hub-intel-edison-kit-node-get-started/15_message_sent.png)

## <a name="next-steps"></a>Passos seguintes

Tiver de executar um exemplo de aplicação para recolher dados de sensores e enviá-lo ao seu IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
