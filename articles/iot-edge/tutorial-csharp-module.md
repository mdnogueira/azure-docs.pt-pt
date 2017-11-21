---
title: "Módulo do Azure IoT Edge c# | Microsoft Docs"
description: "Criar um módulo de limite de IoT com código c# e implementá-la para um dispositivo de limite"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f93cfcdffd79b4cccdbd5f7c67ec42499bf7628c
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Desenvolver e implementar um módulo de limite de IoT c# para o seu dispositivo simulado – pré-visualização

Pode utilizar módulos de limite de IoT para implementar o código que implementa a lógica de negócio diretamente nos seus dispositivos de limite de IoT. Este tutorial explica-lhe como criar e implementar um módulo de limite de IoT que filtra os dados de sensores no dispositivo simulado contorno de IoT que criou na implementação do Azure IoT limite num dispositivo simulado no [Windows] [ lnk-tutorial1-win]ou [Linux] [ lnk-tutorial1-lin] tutoriais. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Utilize o Visual Studio Code para criar um módulo de limite de IoT com base no .NET core 2.0
> * Utilize o Visual Studio Code e Docker para criar uma imagem de docker e publicá-lo no seu registo 
> * Implementar o módulo para o seu dispositivo de limite de IoT
> * Dados de vista gerada


O módulo de limite de IoT que criou neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo e só envia mensagens upstream quando a temperatura for superior um limiar especificado. 

## <a name="prerequisites"></a>Pré-requisitos

* O dispositivo de limite de IoT do Azure que criou no início rápido ou tutorial anterior.
* A cadeia de ligação do IoT Hub para o IoT hub que liga o dispositivo de limite de IoT.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão de limite de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). (Pode instalar a extensão do painel de extensões no Visual Studio Code.)
* [C# para a extensão do Visual Studio Code (com tecnologia OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Pode instalar a extensão do painel de extensões no Visual Studio Code.)
* [Docker](https://docs.docker.com/engine/installation/). A edição de Comunidade (CE) para a sua plataforma é suficiente para este tutorial. Certifique-se, instalá-lo no computador que executa o VS Code no.
* [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>Escolher ou inscrever-se um registo de Docker
Neste tutorial, utilize a extensão de limite de IoT do Azure para o VS Code para construir um módulo e criar um [imagem Docker](https://docs.docker.com/glossary/?term=image). Em seguida, push esta imagem de Docker para um [repositório de Docker](https://docs.docker.com/glossary/?term=repository) alojados num [registo Docker](https://docs.docker.com/glossary/?term=registry). Por fim, implementar a imagem de Docker empacotada como um [contentor de Docker](https://docs.docker.com/glossary/?term=container) a partir do seu registo para o seu dispositivo de limite de IoT.  

Pode utilizar qualquer registo compatível com o Docker para este tutorial. Dois populares Docker registo serviços disponíveis na nuvem são **registo de contentor do Azure** e **Docker Hub**:

- [Registo de contentor do Azure](https://docs.microsoft.com/en-us/azure/container-registry/) está disponível com um [subscrição paga](https://azure.microsoft.com/en-us/pricing/details/container-registry/). Para este tutorial, o **básico** subscrição é suficiente. 

- [Hub de docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) oferece um repositório privado livre se inscreva um ID de Docker (gratuita). 
    1. Para inscrever-se de um ID de Docker, siga as instruções no [registar para um ID de Docker](https://docs.docker.com/docker-id/#register-for-a-docker-id) no site de Docker. 

    2. Para criar um repositório de Docker privado, siga as instruções em [criar um novo repositório de concentrador de Docker](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub) no site de Docker.

Ao longo deste tutorial, apropriado, comandos serão fornecidos para o registo de contentor do Azure e Docker Hub.

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto do módulo de limite de IoT
A mostrar os passos seguintes, como criar um módulo de limite de IoT com base no .NET core 2.0 utilizando o Visual Studio Code e a extensão de limite de IoT do Azure.
1. Abra o VS Code.
2. Utilize o **vista | Integrado Terminal** comandos de menu para abrir o terminal integrado VS Code.
3. No terminal integrado, introduza o seguinte comando para instalar (ou atualizar) as **AzureIoTEdgeModule** modelo no dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. No terminal integrado, introduza o seguinte comando para criar um projeto para o novo módulo:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > Este comando cria a pasta do projeto, **FilterModule**, na pasta de trabalho atual. Se pretende criá-la noutra localização, altere os diretórios antes de executar o comando.
 
3. Utilize o **ficheiro | Abra a pasta** comandos de menu, navegue para o **FilterModule** pasta e clique em **selecionar pasta** para abrir o projeto no VS Code.
4. No Explorador de VS Code, clique em **Program.cs** para abri-lo.
5. Adicione o seguinte campo para a **programa** classe.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Adicione as seguintes classes para o **programa** classe. Estas classes definem o schema esperado para o corpo de mensagens a receber.

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

1. No **Init** método, o código que cria e configura um **DeviceClient** objeto. Este objeto permite que o módulo ligar a execução do Azure IoT Edge local para enviar e receber mensagens. O parâmetro de cadeia de ligação utilizado no **Init** método é fornecido para o módulo pelo tempo de execução de limite de IoT a variável de ambiente **EdgeHubConnectionString**. Depois de criar o **DeviceClient**, o código regista uma chamada de retorno para a receção de mensagens do hub IoT limite através de **input1** ponto final. Substitua o método utilizado como uma chamada de retorno para processamento de mensagens com um novo e anexar uma chamada de retorno para atualizações de propriedades pretendido no duplo de módulo. Para efetuar esta alteração, substitua a última linha do **Init** método com o seguinte código:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. Adicione o seguinte método para o **programa** classe para atualizar o **temperatureThreshold** campo com base nas propriedades de pretendido enviadas pelo serviço de back-end através de duplo de módulo. Todos os módulos têm os seus próprios duplo de módulo. Um duplo módulo permite que um serviço de back-end, configurar o código em execução no interior de um módulo.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

1. Substitua o **PipeMessage** método com o seguinte método. Este método é denominado sempre que o módulo é enviado uma mensagem do Edge Hub. -Filtra as mensagens com base no valor temperatura no corpo da mensagem e o limiar de temperatura definido através de duplo de módulo.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. Crie o projeto. Utilize o **vista | Explorador** comandos de menu para abrir o Explorador do VS Code. No Explorador de, clique com botão direito do **FilterModule.csproj** do ficheiro e clique em **módulo criar limite de IoT** para compilar o módulo e exportar o binário e as respetivas dependências para uma pasta que é criada a imagem de Docker no próximo passo.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Criar uma imagem de Docker e publicá-lo no seu registo

1. Compilar a imagem do Docker.
    1. No VS Code explorer, clique o **Docker** pasta para abri-lo. Em seguida, selecione a pasta para a sua plataforma de contentor: **linux x64** ou **windows nano**. 
    2. Clique com botão direito do **Dockerfile** do ficheiro e clique em **imagem de Docker do módulo de limite de IoT criar**. 
    3. No **selecionar pasta** caixa, ou navegue para ou introduza `./bin/Debug/netcoreapp2.0/publish`. Clique em **selecionar pasta como EXE_DIR**.
    4. Na caixa de texto de pop-up na parte superior da janela VS Code, introduza o nome da imagem. Por exemplo:`<docker registry address>/filtermodule:latest`; onde *endereço de registo do docker* é o seu ID de Docker se estiver a utilizar o Hub de Docker ou semelhante `<your registry name>.azurecr.io`, se estiver a utilizar o registo de contentor do Azure.
 
4. Inicie sessão no Docker. No terminal integrada, introduza o seguinte comando: 

    - Hub de docker (introduza as suas credenciais quando lhe for pedido):
        
        ```csh/sh
        docker login
        ```

    - Para o registo de contentor do Azure:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        Para localizar o nome de utilizador, o servidor de início de sessão e palavra-passe para utilizar neste comando, vá a [portal do Azure] (https://portal.azure.com). De **todos os recursos**, clique no mosaico para o registo de contentor do Azure para abrir as respetivas propriedades, em seguida, clique em **chaves de acesso**. Copie os valores existentes no **Username**, **palavra-passe**, e **servidor de início de sessão** campos. O sould do servidor de início de sessão ter o formato: `<your registry name>.azurecr.io`.

3. A imagem de emissão para o seu repositório de Docker. Utilize o **vista | Comando paleta... | Edge: Imagem de Docker limite de IoT Push módulo** menu de comandos e introduza o nome da imagem na caixa de texto de pop-up na parte superior da janela VS Code. Utilize o mesmo nome de imagem utilizada no passo 1.d.

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>Adicionar credenciais de registo ao tempo de execução do Edge no seu dispositivo de limite
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

1. No [portal do Azure](https://portal.azure.com), navegue até ao seu IoT hub.
2. Aceda a **IoT Edge (pré-visualização)** e selecione o seu dispositivo de limite de IoT.
3. Selecione **definir módulos**. 
2. Adicionar o **tempSensor** módulo. Este passo só é necessário se não implementou anteriormente o **tempSensor** módulo para o seu dispositivo de limite de IoT.
    1. Selecione **Adicionar módulo IoT Edge**.
    2. No **nome** campo, introduza `tempSensor`.
    3. No **URI de imagem** campo, introduza `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deixe as outras definições inalteradas e clique em **guardar**.
9. Adicionar o **filtermodule**
    1. Selecione **Adicionar módulo de limite de IoT** novamente.
    2. No **nome** campo, introduza `filtermodule`.
    3. No **imagem** campo, introduza o seu endereço de imagem; por exemplo `<docker registry address>/filtermodule:latest`.
    4. Verifique o **duplo de módulo de edição** caixa.
    5. Substitua o JSON na caixa de texto para o duplo módulo com o seguinte JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Clique em **Guardar**.
12. Clique em **Seguinte**.
13. No **especificar rotas** passo, copie o JSON abaixo na caixa de texto. Módulos publicar todas as mensagens para o tempo de execução do limite. As regras de declarativas em tempo de execução definem onde as mensagens de fluxo. Este tutorial precisa de dois rotas. A rota primeiro transportes mensagens do sensor de temperatura para o módulo de filtro através de ponto final "input1", que é o ponto final que configurou com o **FilterMessages** processador. A segunda rota transportes mensagens do módulo de filtro ao IoT Hub. Nesta rota `upstream` é um destino especial que diz ao Hub Edge para enviar mensagens para o IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Clique em **Seguinte**.
5. No **rever modelo** passo, clique em **submeter**. 
6. Regressar à página de detalhes do dispositivo IoT limite e clique em **atualizar**. Deverá ver o novo **filtermodule** executar juntamente com o **tempSensor** módulo e a **IoT Edge runtime**. 

## <a name="view-generated-data"></a>Dados de vista gerada

Para monitorizar as mensagens da nuvem enviados a partir do seu dispositivo de limite de IoT ao seu IoT hub do dispositivo:
1. Configure a extensão do Toolkit de IoT do Azure com a cadeia de ligação do IoT hub: 
    1. Utilize o **vista | Explorador** comandos de menu para abrir o Explorador do VS Code. 
    3. No Explorador de, clique em **dispositivos do HUB IOT** e, em seguida, clique em **...** . Clique em **definir a cadeia de ligação do IoT Hub** e introduza a cadeia de ligação para o IoT hub que o dispositivo de limite de IoT liga na janela de pop-up. 

        Para localizar a cadeia de ligação, clique no mosaico do hub IoT no portal do Azure e, em seguida, clique em **políticas de acesso partilhado**. No **políticas de acesso partilhado**, clique em de **iothubowner** política e copie a ligação do IoT Hub da cadeia no **iothubowner** janela.   

1. Para monitorizar os dados que chegam ao IoT hub, utilize o **vista | Comando paleta... | IoT: Iniciar a monitorização de mensagem D2C** comandos de menu. 
2. Para parar a monitorização de dados, utilize o **vista | Comando paleta... | IoT: Parar a monitorização D2C mensagem** comandos de menu. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, vai criar um módulo de limite de IoT que contém o código para filtrar dados não processados gerados pelo seu dispositivo de limite de IoT. Pode continuar a sessão em qualquer um dos seguintes tutoriais para saber mais sobre outras formas de limite de IoT do Azure pode ajudar a que ativar dados em informações empresariais no limite.

> [!div class="nextstepaction"]
> [Implementar a função do Azure como um módulo](tutorial-deploy-function.md)
> [implementar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
