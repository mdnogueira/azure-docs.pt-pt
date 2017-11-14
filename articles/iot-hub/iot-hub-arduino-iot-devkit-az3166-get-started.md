---
title: 'IoT DevKit na nuvem: ligar o IoT DevKit AZ3166 ao IoT Hub do Azure | Microsoft Docs'
description: Neste tutorial, saiba como configurar e ligar IoT DevKit AZ3166 ao IoT Hub do Azure, pelo que pode enviar dados para a plataforma de nuvem do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.openlocfilehash: da757085e55fa0d8ca883f8ed64f30ea81e973f7
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Ligar IoT DevKit AZ3166 ao IoT Hub do Azure na nuvem

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Pode utilizar o [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e soluções de Internet das coisas (IoT) de protótipo tirar partido dos serviços do Microsoft Azure. Inclui uma placa de Arduino compatível com periféricos avançados e sensores, um pacote de placa de open source e um crescer [catálogo projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>O que fazer
Ligar o [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) para um hub IoT do Azure que criar, recolher os dados relativos à temperatura e humidade de sensores e enviar os dados para o IoT hub.

Não tem um DevKit ainda? [Repita o simulador](https://aka.ms/iot-devkit-simulator) ou [obter uma](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>O que irá aprender

* Como ligar o DevKit de IoT a um ponto de acesso sem fios e preparar o ambiente de desenvolvimento.
* Como criar um hub IoT e registar um dispositivo para DevKit de IoT MXChip.
* Como recolher dados de sensores ao executar uma aplicação de exemplo no DevKit de IoT MXChip.
* Como enviar os dados de sensor ao seu IoT hub.

## <a name="what-you-need"></a>Do que precisa

* Um quadro MXChip IoT DevKit com um cabo Micro USB. [Obtê-lo agora](https://aka.ms/iot-devkit-purchase).
* Um computador com Windows 10 ou macOS 10.10 +.
* Uma subscrição ativa do Azure. [Ativar uma conta gratuita de 30 dias avaliação Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Prepare o hardware

Ligue o hardware para o seu computador.

É necessário este hardware:

* Placa DevKit
* Cabo micro USB

![Hardware necessário](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Para ligar o DevKit para o seu computador:

1. Ligar o fim USB para o seu computador.
2. Ligar o fim de Micro USB para o DevKit.
3. O LED verde para power confirma que a ligação.

![Ligações de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Configurar Wi-Fi

Projetos IoT dependem de conectividade à internet. Utilize as seguintes instruções para configurar o DevKit para ligar ao Wi-Fi.

### <a name="enter-ap-mode"></a>Introduza o modo da Ásia-Pacífico

Mantenha premida botão B, push e o botão de reposição de versão e, em seguida, da versão botão B. O DevKit introduz o modo da Ásia-Pacífico para a configuração de Wi-Fi. O ecrã apresenta o identificador de conjunto de serviço (SSID) da DevKit e o endereço IP de portal de configuração.

![Repor botão, o botão B e o SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Ligar ao DevKit da Ásia-Pacífico

Agora, utilize outro dispositivo de Wi-Fi ativada (computador ou telemóvel) para ligar ao SSID DevKit (realçado na imagem anterior). Deixe a palavra-passe em branco.

![Informações de rede e o botão de ligar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurar Wi-Fi para o DevKit

Abra o endereço IP que aparecem no ecrã DevKit no seu computador ou telemóvel browser, selecione a rede Wi-Fi que pretende que o DevKit para ligar a e, em seguida, escreva a palavra-passe. Selecione **Ligar**.

![Caixa de palavra-passe e o botão Ligar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Quando a ligação é bem-sucedida, o DevKit é reiniciado dentro de alguns segundos. Em seguida, ver o endereço IP e nome de Wi-Fi no ecrã:

![Nome de Wi-Fi e endereço IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> O endereço IP apresentado na fotografia não pode corresponder ao endereço IP real atribuído e apresentado no ecrã DevKit. Isto é normal, uma vez Wi-Fi utiliza DHCP para atribuir dinamicamente IPs.

Após a configuração de Wi-Fi, as suas credenciais serão mantidas no dispositivo para essa ligação, mesmo que o dispositivo está desligado. Por exemplo, se configurar o DevKit para Wi-Fi na sua home e, em seguida, colocar o DevKit para o office, terá de reconfigurar o modo da Ásia-Pacífico (a partir do passo na secção "Introduza o modo da Ásia-Pacífico") para ligar o DevKit ao seu escritório Wi-Fi. 

## <a name="start-using-the-devkit"></a>Começar a utilizar o DevKit

A aplicação de predefinido em execução no DevKit verifica a versão mais recente do firmware e apresenta alguns dados de diagnóstico do sensor para si.

### <a name="upgrade-to-the-latest-firmware"></a>Atualizar o firmware mais recente

> [!NOTE] 
> Desde v 1.1, DevKit permite ST SEGURA no bootloader. É necessário atualizar o firmware se estiver a executar em v 1.1 para torná-lo, provavelmente, a funcionar.

Se precisar de uma atualização de firmware, o ecrã apresentará as versões de firmware mais recente e atual. Para atualizar, siga o [atualizar firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) guia.

![Apresentação das versões de firmware atual e mais recente](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Este é um esforço única. Depois de iniciar a desenvolver o DevKit e carregar a aplicação, irá ser proveniente do firmware mais recente com a sua aplicação.

### <a name="test-various-sensors"></a>Testar vários sensores

Clique no botão B sensores de teste. Continue premir e libertar o botão B para percorra cada sensor.

![Apresentar no botão B e sensor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Agora está na altura de configurar o ambiente de desenvolvimento: ferramentas e pacotes a compilação fantásticas aplicações IoT. Pode escolher o Windows ou a versão de macOS, de acordo com o sistema operativo.

### <a name="windows"></a>Windows

Aconselhamo-lo para utilizar o pacote de instalação para preparar o ambiente de desenvolvimento. Se tiver quaisquer problemas, pode seguir o [passos manuais](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) obtê-lo a terminar.

#### <a name="download-the-latest-package"></a>Transferir o pacote mais recente

O ficheiro. zip que transferiu contém todas as ferramentas necessárias e os pacotes para o desenvolvimento de DevKit.

> [!div class="button"]
[Transferência](https://aka.ms/devkit/prod/installpackage/latest)

O ficheiro. zip contém as seguintes ferramentas e pacotes. Se já tiver alguns componentes instalados, o script irá detetar e ignorá-los.

* NODE.js e o Yarn: tempo de execução para o script de configuração e tarefas automatizadas.
* [MSI de 2.0 CLI do Azure](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): experiência da linha de comandos de plataforma para gerir recursos do Azure. O MSI contém dependentes Python e pip.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): o editor de código simples para o desenvolvimento de DevKit.
* [Visual Studio Code extensão para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): extensão que permite um desenvolvimento Arduino no Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): ferramenta que a extensão para Arduino depende.
* Pacote de placa de DevKit: Ferramenta cadeias, bibliotecas e projetos para o DevKit.
* Utilitário de ligação ST: Essenciais ferramentas e controladores.

#### <a name="run-the-installation-script"></a>Execute o script de instalação

No Explorador de ficheiros do Windows, localize o ficheiro. zip e extraia. Localizar `install.cmd`, faça duplo clique nele e selecione **executar como administrador**.

![Explorador de ficheiros](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Durante a instalação, pode ver o progresso de cada ferramenta ou pacote.

![Progresso da instalação](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> Dependendo do seu ambiente, por vezes, obterá Falha ao instalar Arduino IDE. Neste caso, tente [instalar individualmente Arduino IDE](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) e execute novamente a install.cmd. Caso contrário, siga o [passos manuais](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) para instalar todas as ferramentas necessárias e pacotes.

#### <a name="install-drivers"></a>Instalar controladores

O VS Code para extensão Arduino baseia-se a Arduino IDE. Se esta for a primeira vez que está a instalar o IDE Arduino, é-lhe pedido que instale controladores relevantes:

![obter-iniciado-controlador](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

A instalação deve demorar cerca de 10 minutos, consoante a velocidade de internet. Após a instalação estiver concluída, deverá ver o Visual Studio Code e Arduino IDE atalhos no ambiente de trabalho.

> [!NOTE] 
> Ocasionalmente, quando iniciar o VS Code, lhe for pedida com um erro que não é possível localizar Arduino IDE ou pacote quadro relacionados. Para resolver este, feche o VS Code e reinicie o IDE Arduino. VS código deve, em seguida, localize o caminho de Arduino IDE corretamente.

### <a name="macos"></a>macOS

Aconselhamo-lo para utilizar a experiência de instalação de um clique para preparar o ambiente de desenvolvimento. Se tiver quaisquer problemas, pode seguir o [passos manuais](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) obtê-lo a terminar.

#### <a name="install-homebrew"></a>Instalar Homebrew

> [!NOTE] 
> Se tiver instalado Homebrew, pode ignorar este passo.

Siga o [instruções de instalação Homebrew](https://docs.brew.sh/Installation.html) instalá-la.

#### <a name="download-the-latest-package"></a>Transferir o pacote mais recente
O ficheiro. zip que transferiu contém todas as ferramentas necessárias e os pacotes para o desenvolvimento de DevKit.

> [!div class="button"]
[Transferência](https://aka.ms/devkit/prod/installpackage/mac/latest)

O ficheiro. zip contém as seguintes ferramentas e pacotes. Se já tiver alguns componentes instalados, o script irá detetar e ignorá-los.

* NODE.js e o Yarn: tempo de execução para o script de configuração e tarefas automatizadas.
* [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): experiência da linha de comandos de plataforma para gerir recursos do Azure.
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): o editor de código simples para o desenvolvimento de DevKit.
* [Visual Studio Code extensão para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): extensão que permite um desenvolvimento Arduino no Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): ferramenta que a extensão para Arduino depende.
* Pacote de placa de DevKit: Ferramenta cadeias, bibliotecas e projetos para o DevKit.
* Utilitário de ligação ST: Essenciais ferramentas e controladores.

#### <a name="run-the-installation-script"></a>Execute o script de instalação

No LCA, localize o. zip e extraia:

Iniciar a aplicação de Terminal, localize a pasta extrair o ficheiro. zip e execute:

```bash
./install.sh
```

> [!NOTE] 
> Caso cumpra Homebrew erro de permissão, execute `brew doctor` obtê-lo a corrigir. Verifique [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) para obter mais detalhes.

Tem agora todas as ferramentas necessárias e os pacotes instalados para macOS.

## <a name="open-the-project-folder"></a>Abra a pasta do projeto

Começar a criar um IoT Hub do Azure, ligar DevKit e recolher os dados relativos à temperatura e humidade de sensores e enviar os dados para o IoT hub.

### <a name="start-vs-code"></a>Iniciar o VS Code

Certifique-se a que sua DevKit não está ligado. Iniciar o VS Code pela primeira vez e ligar o DevKit para o seu computador. O VS Code automaticamente localiza o DevKit e abre uma página de introdução:

![Página de introdução](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
> Ocasionalmente, quando iniciar o VS Code, serão apresentadas com o erro que não é possível localizar Arduino IDE ou pacote quadro relacionados. Fechar o VS Code, inicie novamente a Arduino IDE e VS Code devem localizar Arduino IDE caminho corretamente.


### <a name="open-the-arduino-examples-folder"></a>Abra a pasta de Arduino exemplos

Expanda o lado esquerdo **ARDUINO exemplos** secção, navegue para **exemplos de MXCHIP AZ3166 > AzureIoT**e selecione **GetStarted**. Esta ação irá abrir uma nova janela do código de VS com a pasta do projeto no mesmo.

![Separador Arduino exemplos](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Se ocorrer fechar o painel, pode abri-lo novamente. Utilize `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, escreva **Arduino**e, em seguida, localize e selecione **Arduino: exemplos**.

## <a name="provision-azure-services"></a>Aprovisionar os serviços do Azure

Na janela da solução, executar a tarefa `Ctrl+P` (macOS: `Cmd+P`), introduzindo `task cloud-provision`:

No terminal VS Code, uma linha de comandos interativa orienta-o aprovisionamento de serviços do Azure necessários:

![Linha de comandos interativa](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Criar e carregar o sketch Arduino

### <a name="install-the-required-library"></a>Instalar a biblioteca necessária

1. Prima `F1` ou `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, escreva **Arduino**e, em seguida, localize e selecione **Arduino: Gestor biblioteca**.

2. Procure o **ArduinoJson** biblioteca e selecione **instalar**: ![instalar Arduino biblioteca](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/arduino-json.png)

### <a name="build-and-upload-the-device-code-windows"></a>Criar e carregar o código de dispositivo (Windows)
1. Utilize `Ctrl+P` executar `task device-upload`.
2. O terminal pede-lhe para introduzir o modo de configuração. Para tal, mantenha premida botão A, em seguida, push e no botão de reposição de versão. O ecrã apresenta a DevKit id e a 'Configuração'.

Isto serve para definir a cadeia de ligação que obtém a partir do `task cloud-provision` passo.

Em seguida, o terminal inicia a verificar e carregar o sketch Arduino:

![Verificação e de carregamento do sketch Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

O DevKit reinicia e começa a executar o código.

### <a name="build-and-upload-the-device-code-mac"></a>Criar e carregar o código de dispositivo (Mac)

1. Colocar DevKit no modo de configuração: mantenha premida A botão, em seguida, push e versão no botão de reposição. O ecrã apresenta a 'Configuração'.
2. Utilize `Cmd+P` executar `task device-upload`.

Isto serve para definir a cadeia de ligação que obtém a partir do `task cloud-provision` passo.

Em seguida, o VS Code inicia a verificar e carregar o sketch Arduino:

![carregamento de dispositivo](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

O DevKit reinicia e começa a executar o código.

## <a name="test-the-project"></a>Testar o projeto

No VS Code, seguindo estes passos para abrir e configurar o Monitor de série:

1. Clique em de `COM[X]` word na barra de estado para definir a porta COM à direita com `STMicroelectronics`: ![porta com](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Clique energia plug ícone na barra de estado para abrir o Monitor de série: ![monitor de série](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/serial-monitor.png)

3. Na barra de estado, clique no número que representa a velocidade de transmissão e definido para `115200`: ![velocidade de transmissão.](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

O exemplo de aplicação é executada com êxito quando vir os seguintes resultados:

* O Monitor de série apresenta as mesmas informações como o conteúdo na captura de ecrã abaixo.
* O LED no MXChip IoT DevKit é blinking.

![Resultado final no VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problemas e comentários

Se tiver problemas, pode encontrar [perguntas mais frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Pode também enviar comentários para por deixar um comentário nesta página.

## <a name="next-steps"></a>Passos seguintes

Se ligou com êxito um DevKit de IoT MXChip ao seu IoT hub e ter enviado os dados de sensor capturada ao seu IoT hub.

Para continuar a introdução ao IoT Hub do Azure e explorar outros cenários de IoT, consulte:

- [Gerir mensagens de dispositivo na cloud com o iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Guardar mensagens do Hub IoT no armazenamento de dados do Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Utilizar o Power BI para visualizar dados de sensores em tempo real do IoT Hub do Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Utilizar as aplicações Web para visualizar dados de sensores em tempo real do IoT Hub do Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Meteorologia previsão a utilizar os dados de sensor do seu hub IoT no Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Gestão de dispositivos com o iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Monitorização remota e notificações com Aplicações Lógicas](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
