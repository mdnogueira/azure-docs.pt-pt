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
ms.openlocfilehash: 0f0591a9f2b8c932cb9a03e7186dbf6877b96197
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Desenvolver e implementar um módulo de limite de IoT c# para o seu dispositivo simulado – pré-visualização

Pode utilizar módulos de limite de IoT para implementar o código que implementa a lógica de negócio diretamente nos seus dispositivos de limite de IoT. Este tutorial explica-lhe como criar e implementar um módulo de limite de IoT que filtra os dados de sensores. Irá utilizar o dispositivo simulado contorno de IoT que criou na implementação do Azure IoT limite num dispositivo simulado [Windows] [ lnk-tutorial1-win] ou [Linux] [ lnk-tutorial1-lin]tutoriais. Neste tutorial, ficará a saber como:    

> [!div class="checklist"]
> * Utilize o Visual Studio Code para criar um módulo de limite de IoT com base no .NET core 2.0
> * Utilize o Visual Studio Code e Docker para criar uma imagem de docker e publicá-lo no seu registo 
> * Implementar o módulo para o seu dispositivo de limite de IoT
> * Dados de vista gerada


O módulo de limite de IoT que criou neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Só envia mensagens upstream se a temperatura for superior um limiar especificado. Este tipo de análise no limite é útil para reduzir a quantidade de dados comunicados para e armazenados na nuvem. 

## <a name="prerequisites"></a>Pré-requisitos

* O dispositivo de limite de IoT do Azure que criou no início rápido ou primeiro tutorial.
* A cadeia de ligação de chave primária para o dispositivo de limite de IoT.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão de limite de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# para a extensão do Visual Studio Code (com tecnologia OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) no mesmo computador que tenha o Visual Studio Code. A edição de Comunidade (CE) é suficiente para este tutorial. 
* [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Criar um registo de contentores
Neste tutorial, utilize a extensão de limite de IoT do Azure para o VS Code para construir um módulo e criar um **imagem contentor** partir dos ficheiros. Em seguida, push esta imagem para um **registo** que armazena e gere as imagens. Por fim, implementar a imagem a partir do seu registo para executar no seu dispositivo de limite de IoT.  

Pode utilizar qualquer registo compatível com o Docker para este tutorial. Dois populares Docker registo serviços disponíveis na nuvem são [registo de contentor do Azure](https://docs.microsoft.com/azure/container-registry/) e [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o registo de contentor do Azure. 

1. No [portal do Azure](https://portal.azure.com), selecione **crie um recurso** > **contentores** > **registo de contentor do Azure** .
2. Atribua o seu registo de um nome, escolha uma subscrição, escolha um grupo de recursos e o SKU de conjunto **básico**. 
3. Selecione **Criar**.
4. Assim que for criado o seu registo de contentor, navegue até ao mesmo e selecione **chaves de acesso**. 
5. Ativar/desativar **utilizador de Admin** para **ativar**.
6. Copie os valores para **servidor de início de sessão**, **Username**, e **palavra-passe**. Irá utilizar estes valores mais tarde no tutorial. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto do módulo de limite de IoT
A mostrar os passos seguintes, como criar um módulo de limite de IoT com base no .NET core 2.0 utilizando o Visual Studio Code e a extensão de limite de IoT do Azure.
1. No Visual Studio Code, selecione **vista** > **integrada Terminal** para abrir o terminal integrado VS Code.
3. No terminal integrado, introduza o seguinte comando para instalar (ou atualizar) as **AzureIoTEdgeModule** modelo no dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Crie um projeto para o módulo de novo. O comando seguinte cria a pasta do projeto, **FilterModule**, na pasta de trabalho atual:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Selecione **ficheiro** > **Abrir pasta**.
4. Navegue para o **FilterModule** pasta e clique em **selecionar pasta** para abrir o projeto no VS Code.
5. No Explorador de VS Code, clique em **Program.cs** para abri-lo.

   ![Abertos Program. CS][1]

6. Adicionar o `temperatureThreshold` variável para o **programa** classe. Esta variável define o valor pode exceder a temperatura medida para que os dados a ser enviadas ao IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Adicionar o `MessageBody`, `Machine`, e `Ambient` classes para o **programa** classe. Estas classes definem o schema esperado para o corpo de mensagens a receber.

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

8. No **Init** método, o código que cria e configura um **DeviceClient** objeto. Este objeto permite que o módulo ligar a execução do Azure IoT Edge local para enviar e receber mensagens. A cadeia de ligação utilizada no **Init** método é fornecido para o módulo pelo tempo de execução do limite de IoT. Depois de criar o **DeviceClient**, o código regista uma chamada de retorno para a receção de mensagens do hub IoT limite através de **input1** ponto final. Substitua o `SetInputMessageHandlerAsync` método com um novo e adicione um `SetDesiredPropertyUpdateCallbackAsync` método para atualizações de propriedades pretendido. Para efetuar esta alteração, substitua a última linha do **Init** método com o seguinte código:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Adicionar o `onDesiredPropertiesUpdate` método para o **programa** classe. Este método recebe atualizações nas propriedades de pretendido de duplo o módulo e atualiza o **temperatureThreshold** variável para corresponder. Todos os módulos têm os seus próprios duplo de módulo, que lhe permite configurar o código em execução no interior de um módulo diretamente a partir da nuvem.

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

10. Substitua o `PipeMessage` método com o `FilterMessages` método. Este método é denominado sempre que o módulo recebe uma mensagem do hub IoT Edge. Filtra de mensagens em fila que temperatures inferior ao limiar de temperatura definidas através de duplo o módulo de relatórios. Também adiciona o **MessageType** propriedade à mensagem com o valor definido **alerta**. 

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

11. Para compilar o projeto, clique com botão direito do **FilterModule.csproj** ficheiro no Explorer e clique **módulo criar limite de IoT**. Este processo compila o módulo e exporta o binário e as respetivas dependências para uma pasta que é utilizado para criar uma imagem de Docker.

   ![Criar o módulo de limite de IoT][2]

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Criar uma imagem de Docker e publicá-lo no seu registo

1. No Explorador de VS Code, expanda o **Docker** pasta. Em seguida, expanda a pasta para a sua plataforma de contentor, quer **linux x64** ou **windows nano**.

   ![Selecione a plataforma de contentor do Docker][3]

2. Clique com botão direito do **Dockerfile** do ficheiro e clique em **imagem de Docker do módulo de limite de IoT criar**. 
3. No **selecionar pasta** janela, procurar ou introduzir `./bin/Debug/netcoreapp2.0/publish`. Clique em **selecionar pasta como EXE_DIR**.
4. Na caixa de texto de pop-up na parte superior da janela VS Code, introduza o nome da imagem. Por exemplo: `<your container registry address>/filtermodule:latest`. O endereço de registo do contentor é o mesmo que o servidor de início de sessão que copiou do seu registo. Deverá estar no formato `<your container registry name>.azurecr.io`.
5. Iniciar sessão para Docker, introduzindo o comando seguinte no terminal VS Code integrado: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Utilize o nome de utilizador, a palavra-passe e o servidor de início de sessão que copiou do seu registo de contentor do Azure quando o criou.

3. A imagem de emissão para o seu repositório de Docker. Selecione **vista** > **paleta de comando** e procure o **Edge: imagem de Docker do módulo de limite de Push de IoT** comandos de menu. Introduza o nome da imagem na caixa de texto de pop-up na parte superior da janela VS Code. Utilize o mesmo nome de imagem utilizada no passo 1.d.

## <a name="add-registry-credentials-to-edge-runtime"></a>Adicionar credenciais de registo ao tempo de execução do contorno
Adicione as credenciais para o registo para o tempo de execução do Edge no computador onde está a executar o dispositivo de limite. Estas credenciais conceder o acesso de tempo de execução para o contentor de extração. 

- Para o Windows, execute o seguinte comando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Para Linux, execute o seguinte comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Executar a solução

1. No [portal do Azure](https://portal.azure.com), navegue até ao seu IoT hub.
2. Aceda a **IoT Edge (pré-visualização)** e selecione o seu dispositivo de limite de IoT.
3. Selecione **definir módulos**. 
2. Verifique se o **tempSensor** módulo é preenchido automaticamente. Se não for, utilize os seguintes passos para adicioná-lo:
    1. Selecione **Adicionar módulo IoT Edge**.
    2. No **nome** campo, introduza `tempSensor`.
    3. No **URI de imagem** campo, introduza `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deixe as outras definições inalteradas e clique em **guardar**.
9. Adicionar o **filterModule** módulo que criou nas secções anteriores. 
    1. Selecione **Adicionar módulo IoT Edge**.
    2. No **nome** campo, introduza `filterModule`.
    3. No **URI de imagem** campo, introduza o seu endereço de imagem; por exemplo `<your container registry address>/filtermodule:latest`.
    4. Verifique o **ativar** caixa para que pode editar o duplo de módulo. 
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
13. No **especificar rotas** passo, copie o JSON abaixo na caixa de texto. Módulos publicar todas as mensagens para o tempo de execução do limite. As regras de declarativas em tempo de execução definem onde as mensagens de fluxo. Neste tutorial, precisa de dois rotas. A rota primeiro transportes mensagens do sensor de temperatura para o módulo de filtro através de ponto final "input1", que é o ponto final que configurou com o **FilterMessages** processador. A segunda rota transportes mensagens do módulo de filtro ao IoT Hub. Nesta rota `upstream` é um destino especial que diz ao Hub Edge para enviar mensagens para o IoT Hub. 

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
    1. Abra o Explorador de VS Code selecionando **vista** > **Explorer**. 
    3. No Explorador de, clique em **dispositivos do HUB IOT** e, em seguida, clique em **...** . Clique em **definir a cadeia de ligação do IoT Hub** e introduza a cadeia de ligação para o IoT hub que o dispositivo de limite de IoT liga na janela de pop-up. 

        Para localizar a cadeia de ligação, clique no mosaico do hub IoT no portal do Azure e, em seguida, clique em **políticas de acesso partilhado**. No **políticas de acesso partilhado**, clique em de **iothubowner** política e copie a ligação do IoT Hub da cadeia no **iothubowner** janela.   

1. Para monitorizar os dados que chegam ao IoT hub, selecione **vista** > **paleta de comando** e procure o **IoT: iniciar a monitorização de mensagem D2C** comandos de menu. 
2. Para parar a monitorização de dados, utilize o **IoT: parar a monitorização D2C mensagem** comandos de menu. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, vai criar um módulo de limite de IoT que contém o código para filtrar dados não processados gerados pelo seu dispositivo de limite de IoT. Pode continuar a sessão em qualquer um dos seguintes tutoriais para saber mais sobre outras formas de limite de IoT do Azure pode ajudar a que ativar dados em informações empresariais no limite.

> [!div class="nextstepaction"]
> [Implementar a função do Azure como um módulo](tutorial-deploy-function.md)
> [implementar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png