---
title: "Agendar tarefas IoT hub do Azure (nó/.NET) | Microsoft Docs"
description: "Como agendar um trabalho do IoT Hub do Azure para invocar um método direto em vários dispositivos. Utilizar o dispositivo IoT do Azure SDK para Node.js para implementar as aplicações do dispositivo simulado e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço para executar a tarefa."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: juanpere
ms.openlocfilehash: e6795f09e275f9fcd38000d48710560244abc11d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="schedule-and-broadcast-jobs-netnodejs"></a>Tarefas de agendamento e de difusão (.NET/Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Utilize o IoT Hub do Azure para agendar e controlar as tarefas que atualizam milhões de dispositivos. Utilize tarefas para:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Uma tarefa encapsula num wrapper uma destas ações e controla a execução em relação a um conjunto de dispositivos que é definida por uma consulta do dispositivo duplo. Por exemplo, uma aplicação de back-end pode utilizar uma tarefa para invocar um método direto em 10 000 dispositivos que reinicia os dispositivos. Especifique o conjunto de dispositivos com uma consulta do dispositivo duplo e agendar a tarefa para ser executada num momento futuro. A evolução da tarefa controla como cada um dos dispositivos receber e executar o método direta de reinício.

Para obter mais informações sobre cada uma destas capacidades, consulte:

* Dispositivo duplo e propriedades: [começar a utilizar dispositivos duplos] [ lnk-get-started-twin] e [Tutorial: como utilizar as propriedades do dispositivo duplo][lnk-twin-props]
* Direcionar métodos: [guia para programadores do IoT Hub - métodos diretas] [ lnk-dev-methods] e [Tutorial: utilizar métodos diretos][lnk-c2d-methods]

Este tutorial mostrar-lhe como:

* Criar uma aplicação de dispositivo que implementa um método direto chamado **lockDoor** que pode ser chamado pela aplicação de back-end. A aplicação de dispositivo recebe também alterações de propriedade pretendido da aplicação de back-end.
* Criar uma aplicação de back-end que cria uma tarefa para chamar o **lockDoor** método direto em vários dispositivos. Outra tarefa envia atualizações da propriedade pretendido para vários dispositivos.

No final deste tutorial, terá de uma aplicação de dispositivo de consola do Node.js e uma aplicação de back-end do .NET (c#) consola:

**simDevice.js** que liga ao seu IoT hub, implementa o **lockDoor** direcionar o método e identificadores pretendido alterações de propriedade.

**ScheduleJob** tarefas que utiliza para chamar o **lockDoor** método direto e atualizar as propriedades do dispositivo duplo pretendida em vários dispositivos.

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Versão 4.0.x ou posterior do Node.js. O artigo [preparar o ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou Linux.
* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Agenda de tarefas para chamar um método direto e enviar atualizações ao dispositivo duplo

Nesta secção, vai criar uma aplicação de consola do .NET (utilizando c#) utiliza as tarefas para chamar o **lockDoor** método direto e enviar atualizações da propriedade pretendido para vários dispositivos.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **ScheduleJob**.

    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][img-createapp]

1. No Explorador de soluções, clique com botão direito do **ScheduleJob** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
1. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Este passo transfere, instala e adiciona uma referência para o [SDK do serviço do Azure IoT] [ lnk-nuget-service-sdk] NuGet pacote e as respetivas dependências.

    ![Janela Gestor de Pacote NuGet][img-servicenuget]
1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adicione o seguinte `using` instrução se não estiver já presente nas instruções predefinido.

    ```csharp
    using System.Threading.Tasks;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o marcador de posição pela cadeia de ligação do IoT Hub para o hub que criou na secção anterior.

    ```csharp
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static JobClient jobClient;
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            "deviceId='myDeviceId'",
            directMethod,
            DateTime.Now,
            10);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Adicione o seguinte método à classe **Programa**:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        var twin = new Twin();
        twin.Properties.Desired["Building"] = "43";
        twin.Properties.Desired["Floor"] = "3";
        twin.ETag = "*";

        JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
            "deviceId='myDeviceId'",
            twin,
            DateTime.Now,
            10);

        Console.WriteLine("Started Twin Update Job");
    }
    ```

1. Por fim, adicione as seguintes linhas ao método **Main**:

    ```csharp
    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. No Explorador de soluções, abra o **definir projetos de arranque...**  e certifique-se a **ação** para **ScheduleJob** projeto é **iniciar**. Compilar a solução.

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, crie uma aplicação de consola do Node.js que responde a um método direto chamado pela nuvem, que aciona um reinício do dispositivo simulado e utiliza as propriedades que relatados para ativar as consultas de duplo de dispositivo identificar dispositivos e quando estes pela última vez reiniciado.

1. Criar uma nova pasta vazia designada **simDevice**.  No **simDevice** pasta, crie um ficheiro de Package. JSON utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

1. Na sua linha de comandos a **simDevice** pasta, execute o seguinte comando para instalar o **azure-iot-device** e **azure-iot-dispositivo-mqtt** pacotes:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Com um editor de texto, crie um novo **simDevice.js** ficheiros o **simDevice** pasta.

1. Adicione o seguinte 'exigir' instruções no início do **simDevice.js** ficheiro:

    ```nodejs
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

1. Adicione uma variável **connectionString** e utilize-a para criar uma instância do **Cliente**. Certifique-se de que substitui os marcadores de posição com os valores adequados à sua configuração.

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Adicione a seguinte função para processar o **lockDoor** método.

    ```nodejs
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

1. Adicione o seguinte código para registar o processador para o **lockDoor** método.

    ```nodejs
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```

1. Guarde e feche o **simDevice.js** ficheiro.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Transient Fault Handling (Processamento de Erros Transitórios)][lnk-transient-faults] da MSDN.

## <a name="run-the-apps"></a>Executar as aplicações

Já está pronto para executar as aplicações.

1. Na linha de comandos no **simDevice** pasta, execute o seguinte comando para começar a escutar o método direta de reinício.

    ```cmd/sh
    node simDevice.js
    ```

1. Executar a aplicação de consola c# **ScheduleJob** clicando no **ScheduleJob** projeto, em seguida, selecionar **depurar** e **iniciar nova instância**.

1. Pode ver o resultado do dispositivo e aplicações de back-end.

    ![Executar as aplicações para agendar tarefas][img-schedulejobs]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, uma tarefa que utilizou para agendar um método direto para um dispositivo e a atualização das propriedades do dispositivo duplo.

Para continuar a introdução ao IoT Hub e padrões de gestão de dispositivos como remota através da atualização de firmware ondas eletromagnéticas, leia [Tutorial: como efetuar uma atualização de firmware][lnk-fwupdate].

Para saber mais sobre a implementação AI em dispositivos de limite com limite de IoT do Azure, consulte [introdução ao IoT Edge][lnk-iot-edge].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
