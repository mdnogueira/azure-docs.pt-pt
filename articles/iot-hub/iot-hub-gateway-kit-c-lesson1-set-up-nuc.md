---
title: 'Dispositivos SensorTag & Gateway do IoT do Azure - Lesson 1: Configurar Intel NUC | Microsoft Docs'
description: "Configure Intel NUC para funcionar como um gateway de IoT entre um sensor e o Azure IoT Hub para recolher informações de sensores e enviá-lo ao IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: gateway de IOT intel nuc, nuc computador, DE3815TYKE
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 1a3a92ab8d08c6ed6f047208217c46022027157e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Configurar Intel NUC como um gateway de IoT
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

## <a name="what-you-will-do"></a>O que irá fazer

- Configure Intel NUC como um gateway de IoT.
- Instale o pacote de limite de IoT do Azure no NUC Intel.
- Execute uma aplicação de exemplo "hello_world" em NUC o Intel para verificar a funcionalidade de gateway.

  > Se tiver quaisquer problemas, procurar soluções no [resolução de problemas de página](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que aprenderá

Este lesson, irá aprender:

- Como ligar Intel NUC com periféricos.
- Como instalar e atualizar os pacotes necessários no NUC Intel utilizando o Gestor de pacotes inteligente.
- Como executar a aplicação de exemplo "hello_world" para verificar a funcionalidade de gateway.

## <a name="what-you-need"></a>Do que precisa

- Um DE3815TYKE do Kit de NUC Intel com o conjunto de Software do Gateway de IoT Intel (Linux vento River * 7.0.0.13) pré-instalado. [Clique aqui para comprar Grove IoT comerciais Gateway Kit](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html).
- Um cabo de Ethernet.
- Um teclado.
- Um cabo HDMI ou VGA.
- Monitor com uma porta HDMI ou VGA.
- Opcional: [Texas Instruments Sensor Tag (CC2650STK)](http://www.ti.com/tool/cc2650stk)

![Kit de gateway](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Ligar Intel NUC com os periféricos

A imagem abaixo está um exemplo de NUC Intel que está ligada à periféricos vários:

1. Ligado a um teclado.
2. Ligado a um monitor com um cabo VGA ou um cabo HDMI.
3. Ligado a uma rede com fios com um cabo de Ethernet.
4. Ligado a uma fonte de alimentação com um cabo de energia.

![Intel NUC ligado a periféricos](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Ligar ao sistema Intel NUC do computador anfitrião através de Secure Shell (SSH)

Precisa de um teclado e um monitor para obter o endereço IP do seu dispositivo Intel NUC. Se já conhece o endereço IP, pode avançar diretamente para o passo 3 nesta secção.

1. Ativar o Intel NUC, premindo o botão de energia e, em seguida, inicie sessão.

   O nome de utilizador predefinido e a palavra-passe são ambos `root`.

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. Obtenha o endereço IP do NUC Intel executando o `ifconfig` comando no dispositivo Intel NUC.

   Eis um exemplo de resultado do comando.

   ![saída de ifconfig Mostrar Intel NUC IP](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   Neste exemplo, o valor que se segue `inet addr:` é o endereço IP que é necessário quando ligar para o Intel NUC de um computador anfitrião.

3. Utilize um dos seguintes clientes SSH do seu computador anfitrião para ligar ao Intel NUC.

    - [PuTTY](http://www.putty.org/) para Windows.
    - O cliente SSH incorporado no Ubuntu ou macOS.

   É mais eficiente e produtiva para operar uma NUC Intel de um computador anfitrião. Terá de endereço IP do NUC da Intel, nome de utilizador e palavra-passe para ligar ao mesmo através de um cliente SSH. Eis um exemplo que utiliza um cliente SSH no macOS.
   ![Cliente SSH em execução no macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-edge-package"></a>Instalar o pacote de limite de IoT do Azure

O pacote do Azure IoT Edge contém os binários de limite de IoT e as respetivas dependências previamente compilados. Estes binários são contorno de IoT do Azure, o Azure IoT SDK e as ferramentas de correspondentes. O pacote contém também um "hello_world" aplicação de exemplo é utilizada para validar a funcionalidade de gateway. Limite de IoT é a parte de núcleo do gateway. 

Siga estes passos para instalar o pacote.

1. Adicione o repositório de nuvem do IoT, executando os comandos seguintes numa janela de terminal:

   ```bash
   rpm --import https://iotdk.intel.com/misc/iot_pub2.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
   ```

   > Introduza 'y', quando o pede-lhe 'Include este canal?'
   
   Se receber um `import read failed(-1)` erro, utilize os seguintes comandos para resolver o problema:
   ```bash
   wget http://iotdk.intel.com/misc/iot_pub2.key 
   rpm --import iot_pub2.key  
   ```

   O `rpm` comando importa a chave de rpm. O `smart channel` comando adiciona o canal rpm para o Gestor de pacotes inteligente. Antes de executar o `smart update` comando, verá uma saída, conforme mostrado abaixo.

   ![rpm e de saída de comandos do canal inteligente](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. Execute o comando de atualização inteligente:

   ```bash
   smart update
   ```

3. Instale o pacote do Gateway de IoT do Azure executando o seguinte comando:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure`é o nome do pacote. O `smart install` comando é utilizado para instalar o pacote.

    > Execute o seguinte comando, se vir este erro: 'não está disponível de chave pública'

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
    > Reinicie o NUC Intel se vir este erro: 'nenhum pacote fornece util-linux-dev'

   Depois do pacote está instalado, Intel NUC está pronto para funcionar como um gateway.

## <a name="run-the-azure-iot-edge-helloworld-sample-application"></a>Executar a aplicação de exemplo do Azure IoT Edge "hello_world"

A aplicação de exemplo seguinte cria um gateway de um `hello_world.json` do ficheiro e utiliza os componentes fundamentais da arquitetura de limite de IoT do Azure para registar uma mensagem de mundo Olá um ficheiro (log.txt) a cada cinco segundos.

Pode executar o exemplo Olá mundo executando os seguintes comandos:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Permitir que a aplicação de Olá, mundo executar alguns minutos e, em seguida, prima a tecla Enter para a interromper.
![saída da aplicação](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> Pode ignorar quaisquer erros de 'handle(NULL) argumento inválido', que são apresentados depois prima Enter.

Pode verificar que o gateway foi executada com êxito ao abrir o ficheiro de log.txt está agora na sua pasta hello_world ![log.txt vista de diretório](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png)

Abra log.txt utilizando o seguinte comando:

```bash
vim log.txt
```

Em seguida, irá ver o conteúdo do log.txt, o que poderá ser uma saída de formatação JSON das mensagens de registo que foram escritas o módulo Olá mundo de gateway a cada cinco segundos.
![Vista de diretório log.txt](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

Se tiver quaisquer problemas, procurar soluções no [resolução de problemas de página](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Resumo

Parabéns! Concluiu a cópia de segurança Intel NUC como um gateway. Agora está pronto para avançar para o lesson seguinte para configurar o computador anfitrião, criar um IoT Hub do Azure e registar o seu dispositivo lógico do IoT Hub do Azure.

## <a name="next-steps"></a>Passos seguintes
[Utilizar um gateway de IoT para ligar um dispositivo ao IoT Hub do Azure](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

