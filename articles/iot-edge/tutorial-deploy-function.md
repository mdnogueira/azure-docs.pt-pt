---
title: "Implementar a função do Azure com o limite de IoT do Azure | Microsoft Docs"
description: "Implementar a função do Azure como um módulo para um dispositivo de limite"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cba901e8929d3626dc06e4600437b6d364e9bc44
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Implementar a função do Azure como um módulo de limite de IoT – pré-visualização
Pode utilizar as funções do Azure para implementar o código que implementa a lógica de negócio diretamente nos seus dispositivos de limite de IoT. Este tutorial explica-lhe como criar e implementar uma função do Azure, que filtra os dados de sensores no dispositivo simulado contorno de IoT que criou na implementação do Azure IoT limite num dispositivo simulado no [Windows] [ lnk-tutorial1-win]ou [Linux] [ lnk-tutorial1-lin] tutoriais. Neste tutorial, ficará a saber como:     

> [!div class="checklist"]
> * Utilize o Visual Studio Code para criar uma função do Azure
> * Utilizar o VS Code e Docker para criar uma imagem de Docker e publicá-lo no seu registo 
> * Implementar o módulo para o seu dispositivo de limite de IoT
> * Dados de vista gerada


A função do Azure que criar neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo e apenas mensagens upstream quando envia para o IoT Hub do Azure a temperatura for superior um limiar especificado. 

## <a name="prerequisites"></a>Pré-requisitos

* O dispositivo de limite de IoT do Azure que criou no início rápido ou tutorial anterior.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# para a extensão do Visual Studio Code (com tecnologia OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Pode instalar a extensão do painel de extensões no Visual Studio Code.)
* [Extensão de limite de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Pode instalar a extensão do painel de extensões no Visual Studio Code.)
* [Docker](https://docs.docker.com/engine/installation/). A edição de Comunidade (CE) para a sua plataforma é suficiente para este tutorial. 
* [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="set-up-a-docker-registry"></a>Configurar um registo de Docker
Neste tutorial, utilize a extensão de limite de IoT do Azure para o VS Code para criar uma função do Azure e criar um [imagem Docker](https://docs.docker.com/glossary/?term=image) com o mesmo. Em seguida, push esta imagem de Docker para um [repositório de Docker](https://docs.docker.com/glossary/?term=repository) alojada por um [registo Docker](https://docs.docker.com/glossary/?term=registry). Por fim, implementar a imagem de Docker empacotada como um [contentor de Docker](https://docs.docker.com/glossary/?term=container) a partir do seu registo para o seu dispositivo de limite de IoT.  

Pode utilizar qualquer registo compatível com o Docker para este tutorial. Dois populares Docker registo serviços disponíveis na nuvem são **registo de contentor do Azure** e **Docker Hub**:

- [Registo de contentor do Azure](https://docs.microsoft.com/en-us/azure/container-registry/) está disponível com um [subscrição paga](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Para este tutorial, o **básico** subscrição é suficiente. 

- [Hub de docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) oferece um repositório privado livre se inscreva um ID de Docker (gratuita). 
    1. Para inscrever-se de um ID de Docker, siga as instruções no [registar para um ID de Docker](https://docs.docker.com/docker-id/#register-for-a-docker-id) no site de Docker. 

    2. Para criar um repositório de Docker privado, siga as instruções em [criar um novo repositório de concentrador de Docker](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) no site de Docker.

Ao longo deste tutorial, apropriado, os comandos são fornecidos para o registo de contentor do Azure e Docker Hub.

## <a name="create-a-function-project"></a>Criar um projeto de função
Os passos seguintes mostram como criar uma função de limite de IoT utilizando o Visual Studio Code e a extensão de limite de IoT do Azure.
1. Abra o VS Code.
2. Utilize o **vista | Integrado Terminal** comandos de menu para abrir o terminal integrado VS Code.
3. No terminal integrado, introduza o seguinte comando para instalar (ou atualizar) as **AzureIoTEdgeFunction** modelo no dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. No terminal integrado, introduza o seguinte comando para criar um projeto para o novo módulo:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > Este comando cria a pasta do projeto, **FilterFunction**, na pasta de trabalho atual. Se pretende criá-la noutra localização, altere os diretórios antes de executar o comando.

3. Utilize o **ficheiro | Abra a pasta** comandos de menu, navegue para o **FilterFunction** pasta e clique em **selecionar pasta** para abrir o projeto no VS Code.
4. No VS Code explorer, clique o **EdgeHubTrigger Csharp** pasta, em seguida, clique no **run.csx** ficheiro para o abrir.
5. Adicione a seguinte instrução depois do `#r "Microsoft.Azure.Devices.Client"` instrução:

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. Adicione as seguintes instruções de utilização depois do existente `using` instruções:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Adicione as seguintes classes. Estas classes definem o schema esperado para o corpo de mensagens a receber.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

1. Substituir o corpo do **executar** método com o seguinte código. -Filtra as mensagens com base no valor de temperatura no corpo da mensagem e o valor de limiar de temperatura.

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. Guarde o ficheiro.

## <a name="publish-a-docker-image"></a>Publicar uma imagem de Docker

1. Compilar a imagem do Docker.
    1. No VS Code explorer, clique o **Docker** pasta para abri-lo. Em seguida, selecione a pasta para a sua plataforma de contentor: **linux x64** ou **windows nano**. 
    2. Clique com botão direito do **Dockerfile** do ficheiro e clique em **imagem de Docker do módulo de limite de IoT criar**. 
    3. No **selecionar pasta** caixa, navegue para a pasta do projeto, **FilterFunction**e clique em **selecionar pasta como EXE_DIR**. 
    4. Na caixa de texto de pop-up na parte superior da janela VS Code, introduza o nome da imagem. Por exemplo, `<docker registry address>/filterfunction:latest`; onde *endereço de registo do docker* é o seu ID de Docker se estiver a utilizar o Hub de Docker ou é semelhante à `<your registry name>.azurecr.io`, se estiver a utilizar o registo de contentor do Azure.
 
4. Inicie sessão no Docker. No terminal integrada, introduza o seguinte comando: 

    - Hub de docker (introduza as suas credenciais quando lhe for pedido):
        
        ```csh/sh
        docker login
        ```

    - Registo de contentor do Azure:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Para localizar o nome de utilizador, o servidor de início de sessão e palavra-passe para utilizar neste comando, vá a [portal do Azure] (https://portal.azure.com). De **todos os recursos**, clique no mosaico para o registo de contentor do Azure para abrir as respetivas propriedades, em seguida, clique em **chaves de acesso**. Copie os valores existentes no **Username**, **palavra-passe**, e **servidor de início de sessão** campos. O sould do servidor de início de sessão ter o formato: `<your registry name>.azurecr.io`.

3. A imagem de emissão para o seu repositório de Docker. Utilize o **vista | Comando paleta... | Edge: Imagem de Docker limite de IoT Push módulo** menu de comandos e introduza o nome da imagem na caixa de texto de pop-up na parte superior da janela VS Code. Utilize o mesmo nome de imagem utilizada no passo 1.c.

## <a name="add-registry-credentials-to-your-edge-device"></a>Adicione as credenciais do registo para o seu dispositivo de limite
Adicione as credenciais para o registo para o tempo de execução do Edge no computador onde está a executar o dispositivo de limite. Isto permite que o acesso de tempo de execução para o contentor de extração. 

- Para o Windows, execute o seguinte comando:
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Para Linux, execute o seguinte comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>Executar a solução

1. No **portal do Azure**, navegue até ao seu IoT hub.
2. Aceda a **IoT Edge (pré-visualização)** e selecione o seu dispositivo de limite de IoT.
1. Selecione **definir módulos**. 
2. Adicionar o **tempSensor** módulo. Este passo só é necessário se não implementou anteriormente o **tempSensor** módulo para o seu dispositivo de limite de IoT.
    1. Selecione **Adicionar módulo IoT Edge**.
    2. No **nome** campo, introduza `tempSensor`.
    3. No **URI de imagem** campo, introduza `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deixe as outras definições inalteradas e clique em **guardar**.
1. Adicionar o **filterfunction** módulo.
    1. Selecione **Adicionar módulo de limite de IoT** novamente.
    2. No **nome** campo, introduza `filterfunction`.
    3. No **imagem** campo, introduza o seu endereço de imagem; por exemplo `<docker registry address>/filterfunction:latest`.
    74. Clique em **Guardar**.
2. Clique em **Seguinte**.
3. No **especificar rotas** passo, copie o JSON abaixo na caixa de texto. Módulos publicar todas as mensagens para o tempo de execução do limite. As regras de declarativas em tempo de execução definem onde as mensagens de fluxo. Neste tutorial, precisa de dois rotas. A rota primeiro transportes mensagens do sensor de temperatura para o módulo de filtro através de ponto final "input1", que é o ponto final que configurou com o **FilterMessages** processador. A segunda rota transportes mensagens do módulo de filtro ao IoT Hub. Nesta rota `upstream` é um destino especial que diz ao Hub Edge para enviar mensagens para o IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Clique em **Seguinte**.
5. No **rever modelo** passo, clique em **submeter**. 
6. Regressar à página de detalhes do dispositivo IoT limite e clique em **atualizar**. Deverá ver o novo **filterfunction** módulo em execução juntamente com o **tempSensor** módulo e a **IoT Edge runtime**. 

## <a name="view-generated-data"></a>Dados de vista gerada

Para monitorizar as mensagens da nuvem enviados a partir do seu dispositivo de limite de IoT ao seu IoT hub do dispositivo:
1. Configure a extensão do Toolkit de IoT do Azure com a cadeia de ligação do IoT hub: 
    1. Utilize o **vista | Explorador** comandos de menu para abrir o Explorador do VS Code. 
    3. No Explorador de, clique em **dispositivos do HUB IOT** e, em seguida, clique em **...** . Clique em **definir a cadeia de ligação do IoT Hub** e introduza a cadeia de ligação para o IoT hub que o dispositivo de limite de IoT liga na janela de pop-up. 

        Para localizar a cadeia de ligação, clique no mosaico do hub IoT no portal do Azure e, em seguida, clique em **políticas de acesso partilhado**. No **políticas de acesso partilhado**, clique em de **iothubowner** política e copie a ligação do IoT Hub da cadeia no **iothubowner** janela.   

1. Para monitorizar os dados que chegam ao IoT hub, utilize o **vista | Comando paleta... | IoT: Iniciar a monitorização de mensagem D2C** comandos de menu. 
2. Para parar a monitorização de dados, utilize o **vista | Comando paleta... | IoT: Parar a monitorização D2C mensagem** comandos de menu. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, vai criar uma função do Azure que contém o código para filtrar dados não processados gerados pelo seu dispositivo de limite de IoT. Para manter explorar o Azure IoT Edge, saiba como utilizar um dispositivo de limite de IoT como um gateway. 

> [!div class="nextstepaction"]
> [Criar um dispositivo de gateway do limite de IoT](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
