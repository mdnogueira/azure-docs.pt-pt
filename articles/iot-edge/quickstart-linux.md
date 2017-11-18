---
title: "Limite de IoT do Azure de início rápido + Linux | Microsoft Docs"
description: "Experimentar o Azure IoT Edge executando análise num dispositivo simulado contorno"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: bfa6652eac34f88baf09f55353cf58227a20e4cf
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-linux-device---preview"></a>Início rápido: Implementar o módulo de limite de IoT primeiro do portal do Azure para um dispositivo de Linux – pré-visualização

Limite de IoT do Azure move a capacidade da nuvem para os seus dispositivos Internet das coisas. Neste tópico, saiba como utilizar a interface de nuvem para implementar o código prebuilt remotamente um dispositivo de limite de IoT.

Se não tiver uma subscrição do Azure Active Directory, crie um [conta gratuita] [ lnk-account] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar esta tarefa, utilize o seu computador ou uma máquina virtual para simular um dispositivo de Internet das coisas. Os seguintes serviços são necessários para implementar com êxito um dispositivo de limite de IoT:

- [Instalar Docker no Linux] [ lnk-docker-ubuntu] e certifique-se de que está a ser executado. 
- A maioria das distribuições de Linux, incluindo Ubuntu, já tem o Python 2.7 instalado. Utilize o seguinte comando para se certificar de que o pip está instalado: `sudo apt-get install python-pip`.

## <a name="create-an-iot-hub-with-azure-cli"></a>Criar um IoT hub com a CLI do Azure

Crie um IoT hub na sua subscrição do Azure. O nível do IoT Hub gratuito funciona para este início rápido. Se tiver utilizado o IoT Hub no passado e já tiver um hub gratuito criado, pode ignorar esta secção e avance para [registar um dispositivo de limite de IoT][anchor-register]. Cada subscrição só pode ter um IoT hub gratuito. 

1. Inicie sessão no [Portal do Azure][lnk-portal]. 
1. Selecione o **nuvem Shell** botão. 

   ![Botão de Shell de nuvem][1]

1. Crie um grupo de recursos. O código seguinte cria um grupo de recursos denominado **IoTEdge** no **EUA oeste** região:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Crie um hub IoT no seu novo grupo de recursos. O código seguinte cria um livre **F1** hub chamado **MyIotHub** no grupo de recursos **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registar um dispositivo de limite de IoT

Crie uma identidade de dispositivo para o seu dispositivo simulado para que este consegue comunicar com o seu IoT hub. Uma vez que os dispositivos de limite de IoT comportar-se e podem ser geridos de forma diferente de dispositivos de IoT típicos, declarar esta opção para ser um dispositivo de limite de IoT a partir do início. 

1. No portal do Azure, navegue até ao seu IoT hub.
1. Selecione **IoT Edge (pré-visualização)**.
1. Selecione **dispositivo de limite de IoT adicionar**.
1. Dê o seu dispositivo simulado um ID de dispositivo exclusivo.
1. Selecione **guardar** para adicionar o seu dispositivo.
1. Selecione o novo dispositivo na lista de dispositivos. 
1. Copie o valor para **cadeia de ligação - chave primária** e guardá-lo. Irá utilizar este valor para configurar o tempo de execução do limite de IoT na secção seguinte. 

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o tempo de execução do limite de IoT

O tempo de execução do limite de IoT é implementado em todos os dispositivos de limite de IoT. É composto por dois módulos. Em primeiro lugar, o agente de limite de IoT facilita a implementação e monitorização de módulos no dispositivo de limite de IoT. Segundo, o hub IoT Edge gere as comunicações entre os módulos no dispositivo de limite de IoT e entre o dispositivo e o IoT Hub. 

No computador onde irá executar o dispositivo de limite de IoT, transferi o script de controlo contorno de IoT:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Configure o tempo de execução com a cadeia de ligação do dispositivo de limite de IoT da secção anterior:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Inicie o tempo de execução:
```cmd
sudo iotedgectl start
```

Docker para ver que o agente de limite de IoT está em execução como um módulo de verificação:
```cmd
sudo docker ps
```

![Consulte edgeAgent no Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Implementar um módulo

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Dados de vista gerada

Este guia de introdução, criou um novo dispositivo de limite de IoT e instalado o tempo de execução do limite de IoT. Em seguida, utilizou o portal do Azure para emitir um módulo de limite de IoT para ser executada no dispositivo sem ter de efetuar alterações para o dispositivo propriamente dito. Neste caso, o módulo que tiver feito o Push de cria ambientais dados que pode utilizar para os tutoriais. 

Abra a linha de comandos no computador com o seu dispositivo simulado novamente. Confirme que o módulo implementado a partir da nuvem está em execução no seu dispositivo de limite de IoT:

```cmd
sudo docker ps
```

![Ver três módulos no seu dispositivo](./media/tutorial-simulate-device-linux/docker-ps2.png)

Ver as mensagens do módulo tempSensor, que está a ser enviadas para a nuvem:

```cmd
sudo docker logs -f tempSensor
```

![Ver os dados a partir do módulo](./media/tutorial-simulate-device-linux/docker-logs.png)

Também pode ver a telemetria que o dispositivo está a enviar ao utilizar o [ferramenta do Explorador do IoT Hub][lnk-iothub-explorer]. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não necessita do IoT Hub que criou, pode utilizar o [delete de hub iot de az] [ lnk-delete] comando para remover o recurso e todos os dispositivos associados à mesma:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Passos seguintes

Aprendeu a implementar um módulo de IoT Edge para um dispositivo de limite de IoT. Agora tente implementar tipos diferentes de serviços do Azure como módulos, para que possa analisar dados no limite. 

* [Implementar o seu próprio código como um módulo](tutorial-csharp-module.md)
* [Implementar a função do Azure como um módulo](tutorial-deploy-function.md)
* [Implementar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
