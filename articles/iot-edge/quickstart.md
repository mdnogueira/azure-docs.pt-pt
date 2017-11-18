---
title: "Limite de IoT do Azure de início rápido + Windows | Microsoft Docs"
description: "Experimentar o Azure IoT Edge executando análise num dispositivo simulado contorno"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 17675f870a015e86f98bf286a9b1c2bbc05c16cd
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Início rápido: Implementar o módulo de limite de IoT primeiro do portal do Azure para um dispositivo Windows – pré-visualização

Este guia de introdução, utilize a interface de nuvem do Azure IoT Edge para implementar código prebuilt remotamente um dispositivo de limite de IoT. Para realizar esta tarefa, primeiro utilizar o seu dispositivo do Windows para simular um dispositivo de limite de IoT, em seguida, pode implementar um módulo ao mesmo.

Se não tiver uma subscrição do Azure Active Directory, crie um [conta gratuita] [ lnk-account] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial parte do princípio de que está a utilizar um computador ou máquina virtual com o Windows para simular um dispositivo de Internet das coisas. Se estiver a executar o Windows numa máquina virtual, ative [virtualização aninhada] [ lnk-nested] e atribua, pelo menos, 2 GB de memória. 

1. Certifique-se de que está a utilizar uma versão suportada do Windows:
   * Windows 10 
   * Windows Server
2. Instalar [Docker para Windows] [ lnk-docker] e certifique-se de que está a ser executado.
3. Instalar [Python 2.7 no Windows] [ lnk-python] e certifique-se de que pode utilizar o comando do pip.
4. Execute o seguinte comando para transferir o script de controlo contorno de IoT.

   ```
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Limite de IoT do Azure pode executar contentores do Windows ou Linux contentores. Utilizar contentores do Windows, tem de executar:
>    * Atualizar do Windows 10 criadores de reversão, ou
>    * Windows Server 1709 (criar 16299), ou
>    * Windows IoT Core (criar 16299) num dispositivo baseado em x64
>
> Para Windows IoT Core, siga as instruções em [instalar o runtime de limite de IoT no Windows IoT Core][lnk-install-iotcore]. Caso contrário, basta [configurar Docker utilizar contentores Windows][lnk-docker-containers]e, opcionalmente, confirme os pré-requisitos com o seguinte comando do powershell:
>    ```
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

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

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Configurar o tempo de execução do limite de IoT

O tempo de execução do limite de IoT é implementado em todos os dispositivos de limite de IoT. É composto por dois módulos. Em primeiro lugar, o agente de limite de IoT facilita a implementação e monitorização de módulos no dispositivo de limite de IoT. Segundo, o hub IoT Edge gere as comunicações entre os módulos no dispositivo de limite de IoT e entre o dispositivo e o IoT Hub. 

Configure o tempo de execução com a cadeia de ligação do dispositivo de limite de IoT da secção anterior.

```
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Inicie o tempo de execução.

```
iotedgectl start
```

Verifique o Docker para ver que o agente de limite de IoT está em execução como um módulo.

```
docker ps
```

![Consulte edgeAgent no Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Implementar um módulo

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Dados de vista gerada

Este guia de introdução, criou um novo dispositivo de limite de IoT e instalado o tempo de execução do limite de IoT. Em seguida, utilizou o portal do Azure para emitir um módulo de limite de IoT para ser executada no dispositivo sem ter de efetuar alterações para o dispositivo propriamente dito. Neste caso, o módulo que tiver feito o Push de cria ambientais dados que pode utilizar para os tutoriais. 

Abra a linha de comandos no computador com o seu dispositivo simulado novamente. Certifique-se de que o módulo implementado a partir da nuvem está em execução no seu dispositivo de limite de IoT. 

```cmd
docker ps
```

![Ver três módulos no seu dispositivo](./media/tutorial-simulate-device-windows/docker-ps2.png)

Ver as mensagens do módulo tempSensor, que está a ser enviadas para a nuvem. 

```cmd
docker logs -f tempSensor
```

![Ver os dados a partir do módulo](./media/tutorial-simulate-device-windows/docker-logs.png)

Também pode ver a telemetria que o dispositivo está a enviar ao utilizar o [ferramenta do Explorador do IoT Hub][lnk-iothub-explorer]. 
## <a name="clean-up-resources"></a>Limpar recursos

Quando já não necessita do IoT Hub que criou, pode utilizar o [delete de hub iot de az] [ lnk-delete] comando para remover o recurso e todos os dispositivos associados à mesma:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Passos seguintes

Aprendeu a implementar um módulo de IoT Edge para um dispositivo de limite de IoT. Agora tente implementar tipos diferentes de serviços do Azure como módulos, para que possa analisar dados no limite. 

* [Implementar a função do Azure como um módulo](tutorial-deploy-function.md)
* [Implementar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)
* [Implementar o seu próprio código como um módulo](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
