---
title: "SensorTag dispositivo & Gateway de IoT do Azure - resolução de problemas | Microsoft Docs"
description: "Página de resolução de problemas de gateway Intel NUC"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: problemas de IOT, internet dos problemas de coisas
ROBOTS: NOINDEX
ms.assetid: 5f742c38-0e33-465a-9a0d-1e41e8d17187
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 7e80951de55ade6b5140608dcff8ebb064f942ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting"></a>Resolução de problemas

## <a name="hardware-issues"></a>Problemas de hardware

### <a name="ti-sensortag-cannot-be-connected"></a>Não é possível ligar SensorTag de TI

Para resolver problemas de conectividade SensorTag, utilize o [SensorTag aplicação](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide).

### <a name="have-an-issue-with-intel-nuc"></a>Tem um problema com Intel NUC

Para resolver problemas de arranque, consulte [resolução de problemas de arranque não no Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html).

Para resolver problemas do sistema operativo, consulte [resolução de problemas do sistema operativo no Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html).

Para resolver outros problemas, consulte [Blink códigos e um aviso sonoro códigos para Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html).

## <a name="nodejs-package-issues"></a>Problemas de pacote do node.js

### <a name="no-response-during-gulp-tasks"></a>Nenhuma resposta durante gulp tarefas

Se tiver problemas em gulp tarefas em execução, pode adicionar o `--verbose` opção de depuração. Tente terminar tarefas atuais gulp utilizando `Ctrl + C`e, em seguida, execute o seguinte comando na sua janela de consola para ver mensagens de depuração. Poderá ver mensagens de erro detalhadas no resultado da consola.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemas de deteção de dispositivo

Para obter ajuda na resolução de problemas comuns com o `discover-sensortag` comando, verifique o [wiki página](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl).

### <a name="npm-issues"></a>problemas de npm

Experimente atualizar o seu pacote npm, executando o seguinte comando:

```bash
npm install -g npm
```

Se o problema continuar, deixe os seus comentários no final deste artigo ou criar um problema no GitHub na nossa [repositório de exemplo](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started).

## <a name="remote-debugging"></a>Depuração remota
> Abaixo instruções destinam-se para depuração de scripts de node.js que utilizou neste tutorial.
### <a name="run-the-sample-application-in-debug-mode"></a>Executar a aplicação de exemplo no modo de depuração

Execute a aplicação de exemplo no modo de depuração, executando o seguinte comando:

```bash
gulp run --debug
```

Quando o motor de depuração está pronto, deverá ver `Debugger listening on port 5858` no resultado da consola.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Configurar o Visual Studio Code para ligar ao dispositivo remoto

1. Abra o **depurar** painel no lado esquerdo.
2. Clique na verde **iniciar depuração** botão (F5). Visual Studio Code abre um `launch.json` ficheiro.
3. Atualização do `launch.json` ficheiro com o seguinte conteúdo. Substitua `[device hostname or IP address]` com o nome de anfitrião ou endereço IP do dispositivo real.

   ``` json
   {
     "version": "0.2.0",
     "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![Configuração de depuração remota](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Ligar à aplicação remota

Clique na verde **iniciar depuração** botão (F5) para iniciar a depuração.

Leitura [JavaScript no VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) para saber mais sobre o depurador.

![Exemplo de var depuração](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Problemas CLI do Azure

A interface de linha de comandos do Azure (CLI do Azure) é uma versão de pré-visualização. Procurar o ressarcimento de soluções, pode utilizar o [guia de instalação de pré-visualização](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

O ficheiro se encontrar quaisquer erros com a ferramenta, um [problema](https://github.com/Azure/azure-cli/issues) no **problemas** secção do repositório GitHub.

Para obter ajuda na resolução de problemas comuns, verifique o [Leia-me](https://github.com/Azure/azure-cli/blob/master/README.rst).

Caso cumpra "Não foi possível localizar uma versão que satisfaça os requisitos", execute o seguinte comando para atualizar o pip para qualquer versão.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Problemas de instalação do Python

### <a name="legacy-installation-issues-macos"></a>Problemas de instalação legado (macOS)

Quando estiver a instalar o pip, um erro de permissão é acionado quando são instalados pacotes antigos com **su** permissões. Esta situação ocorre porque uma instalação anterior do Python com brew (macOS) não está completamente desinstalada. Alguns pacotes de pip de uma instalação anterior foram criados pelo raiz, o que faz com que o erro de permissão. A solução é remover estes pacotes instalados pelo raiz. Utilize os seguintes passos para concluir esta tarefa:

1. Ir para `/usr/local/lib/python2.7/site-packages`
2. Pacotes da lista criados pelo raiz:`ls -l | grep root`
3. Desinstale pacotes a partir do passo 2:`sudo rm -rf {package name}`
4. Reinstale o Python.

## <a name="azure-iot-hub-issues"></a>Problemas de IoT Hub do Azure

Se tiver aprovisionado com êxito o hub IoT do Azure com a CLI do Azure e precisar de uma ferramenta para gerir os dispositivos que se ligam ao seu IoT hub, experimente as seguintes ferramentas.

### <a name="device-explorer"></a>Explorador de dispositivo

[Explorador de dispositivo](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) é executado no seu computador local do Windows e liga ao seu hub IoT no Azure. Comunica com o seguinte [pontos finais de IoT Hub](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/):

- Gestão de identidades de dispositivo para aprovisionar e gerir dispositivos registados com o seu IoT hub.
- Receba o dispositivo para nuvem pelo que pode monitorizar as mensagens enviadas a partir do seu dispositivo ao seu IoT hub.
- Envie nuvem para o dispositivo, para que possam enviar mensagens para os seus dispositivos a partir do seu IoT hub.

Configure a cadeia de ligação do hub IoT desta ferramenta para utilizar as respetivas capacidades.

### <a name="iothub-explorer"></a>Explorador de iothub

[Explorador de iothub](https://github.com/Azure/iothub-explorer) é uma ferramenta CLI multiplataformas de exemplo para gerir clientes de dispositivo. Pode utilizar a ferramenta para gerir os dispositivos no registo de identidade, monitorizar as mensagens do dispositivo para nuvem e enviar comandos da nuvem para o dispositivo.

Para instalar a versão mais recente (pré-lançamento) da ferramenta iothub Explorador, execute o seguinte comando:

```bash
npm install -g iothub-explorer@latest
```

Para obter ajuda adicional sobre todos os comandos iothub explorer e os respetivos parâmetros, execute o seguinte comando:

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>O portal do Azure

Uma experiência completa do CLI ajuda-o a criar e gerir todos os seus recursos do Azure. Pode também querer utilizar os [portal do Azure](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/) para ajudar a aprovisionar, gerir e depurar os recursos do Azure.

## <a name="azure-storage-issues"></a>Problemas de armazenamento do Azure

[Explorador de armazenamento do Microsoft Azure (pré-visualização)](http://storageexplorer.com/) é uma aplicação autónoma da Microsoft que pode utilizar para trabalhar com dados de armazenamento do Azure no Windows, macOS e Linux. Ao utilizar esta ferramenta, pode ligar a sua tabela e ver os dados no mesmo. Pode utilizar esta ferramenta para resolver os problemas de armazenamento do Azure.
