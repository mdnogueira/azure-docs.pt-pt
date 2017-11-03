---
title: "Utilize as propriedades do IoT Hub do Azure dispositivo duplo (nó/.NET) | Microsoft Docs"
description: "Como utilizar dispositivos duplos do IoT Hub do Azure para configurar dispositivos. Utilizar o dispositivo IoT do Azure SDK para Node.js para implementar uma aplicação de dispositivo simulado e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que modifica uma configuração de dispositivo utilizando um dispositivo duplo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.openlocfilehash: 8f9626fc47e7d32cb104b960bea9b7de9efa6f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Utilize as propriedades do pretendido para configurar dispositivos
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

No final deste tutorial, terá de duas aplicações de consola:

* **SimulateDeviceConfiguration.js**, uma aplicação de dispositivo simulado que aguarda uma atualização da configuração pretendida e reporta o estado de um processo de atualização da configuração simulada.
* **SetDesiredConfigurationAndQuery**, uma aplicação de back-end de .NET, o que define a configuração pretendida num dispositivo e consulta o processo de atualização da configuração.

> [!NOTE]
> O artigo [SDKs IoT do Azure] [ lnk-hub-sdks] fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivo e o back-end.
> 
> 

Para concluir este tutorial precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.

Se seguiu o [começar a utilizar dispositivos duplos] [ lnk-twin-tutorial] tutorial, já tiver um IoT hub e uma identidade de dispositivo chamado **myDeviceId**. Nesse caso, pode avançar para o [criar a aplicação de dispositivo simulado] [ lnk-how-to-configure-createapp] secção.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Criar a aplicação de dispositivo simulada
Nesta secção, pode cria uma aplicação de consola do Node.js que liga ao seu hub como **myDeviceId**, tem de aguardar durante uma atualização da configuração pretendida e, em seguida, os relatórios de atualizações no processo de atualização de configuração simulada.

1. Criar uma nova pasta vazia designada **simulatedeviceconfiguration**. No **simulatedeviceconfiguration** pasta, crie um novo ficheiro Package. JSON utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições.
   
    ```
    npm init
    ```
1. Na sua linha de comandos a **simulatedeviceconfiguration** pasta, execute o seguinte comando para instalar o **azure-iot-device** e **azure-iot-dispositivo-mqtt** pacotes:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Com um editor de texto, crie um novo **SimulateDeviceConfiguration.js** ficheiros o **simulatedeviceconfiguration** pasta.
1. Adicione o seguinte código para o **SimulateDeviceConfiguration.js** de ficheiros e substitua o **{cadeia de ligação do dispositivo}** marcador de posição pela cadeia de ligação de dispositivo que copiou quando criou o **myDeviceId** identidade de dispositivo:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    O **cliente** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do dispositivo. Este código inicializa a **cliente** objeto, obtém o dispositivo duplo para **myDeviceId**e, em seguida, anexa um processador para a atualização no *pretendido propriedades*. O processador verifica que existe é um pedido de alteração da configuração real comparando a configIds, em seguida, invoca um método que inicia a alteração da configuração.
   
    Tenha em atenção que, com vista à, simplicidade, este código utiliza uma predefinição hard-coded para a configuração inicial. Uma aplicação real, provavelmente, seria carregar que a configuração de um local de armazenamento.
   
   > [!IMPORTANT]
   > Eventos de alteração de propriedade pretendido são sempre emitidos uma vez na ligação do dispositivo. Certifique-se que não há uma alteração real nas propriedades de pretendido antes de efetuar qualquer ação.
   > 
   > 
1. Adicione os métodos seguintes antes do `client.open()` invocação:
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    O **initConfigChange** método atualiza as propriedades comunicadas no objeto de duplo dispositivo local com o pedido de atualização de configuração e define o estado para **pendente**, em seguida, atualiza o dispositivo duplo no serviço. Depois de atualizar com êxito o dispositivo duplo, simula um processo de execução longa que termina na execução da **completeConfigChange**. Este método atualiza as propriedades de comunicado locais, definir o estado para **êxito** e remover o **pendingConfig** objeto. Em seguida, atualiza o dispositivo duplo no serviço.
   
    Tenha em atenção que, para poupar largura de banda, comunicados propriedades são atualizadas ao especificar apenas as propriedades de ser modificados (com o nome **patch** no código acima), em vez de substituir todo o documento.
   
   > [!NOTE]
   > Este tutorial não simular nenhum comportamento de atualizações de configuração em simultâneo. Alguns processos de atualização de configuração poderão acomodar as alterações de configuração de destino enquanto a atualização está a ser executado, alguns poderão ter de fila-los e algumas rejeitá-las com uma condição de erro. Certifique-se a ter em consideração o comportamento pretendido para o processo de configuração específica e adicione a lógica adequada antes de iniciar a alteração da configuração.
   > 
   > 
1. Execute a aplicação de dispositivo:
   
        node SimulateDeviceConfiguration.js
   
    Deverá ver a mensagem `retrieved device twin`. Manter a aplicação em execução.

## <a name="create-the-service-app"></a>Criar a aplicação de serviço
Nesta secção, irá criar uma aplicação de consola .NET que atualiza o *pretendido propriedades* no dispositivo duplo associado **myDeviceId** com um novo objeto de configuração de telemetria. Em seguida, consulta os dispositivos duplos armazenados no IoT hub e mostra a diferença entre as configurações desejadas e comunicadas do dispositivo.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **SetDesiredConfigurationAndQuery**.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][img-createapp]
1. No Explorador de soluções, clique com botão direito do **SetDesiredConfigurationAndQuery** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
1. Na janela **Gestor de Pacote NuGet**, selecione **Procurar**, pesquise **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço do Azure IoT][lnk-nuget-service-sdk] e às respetivas dependências.
   
    ![Janela Gestor de Pacote NuGet][img-servicenuget]
1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Adicione o seguinte método à classe **Programa**:
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            try
            {
                while (true)
                {
                    var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                    var results = await query.GetNextAsTwinAsync();
                    foreach (var result in results)
                    {
                        Console.WriteLine("Config report for: {0}", result.DeviceId);
                        Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine();
                    }
                    Thread.Sleep(10000);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }
   
    O **registo** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do serviço. Este código inicializa a **registo** objeto, obtém o dispositivo duplo para **myDeviceId**e, em seguida, atualiza as respetivas propriedades pretendidas com um novo objeto de configuração de telemetria.
    Depois disso, este consulta os dispositivos duplos armazenados no IoT hub a cada 10 segundos e imprime as configurações de telemetria pretendido e comunicados. Consulte o [idioma de consulta do IoT Hub] [ lnk-query] para saber como gerar relatórios avançados em todos os seus dispositivos.
   
   > [!IMPORTANT]
   > Esta aplicação consulta IoT Hub a cada 10 segundos para fins de ilustrativa. Utilize consultas para gerar relatórios de destinada ao utilizador entre vários dispositivos e não para detetar as alterações. Se a sua solução requer notificações em tempo real de eventos do dispositivo, utilize [duplo notificações][lnk-twin-notifications].
   > 
   > 
1. Por fim, adicione as seguintes linhas ao método **Main**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. No Explorador de soluções, abra o **definir projetos de arranque...**  e certifique-se a **ação** para **SetDesiredConfigurationAndQuery** projeto é **iniciar**. Compilar a solução.
1. Com **SimulateDeviceConfiguration.js** em execução, execute a aplicação .NET do Visual Studio com **F5** e deverá ver a alteração de configuração comunicada **êxito** para **pendente** para **êxito** novamente com o novo ativo de enviar uma frequência de cinco minutos em vez de 24 horas.

 ![Dispositivos configurados com êxito][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Não há um atraso de até um minuto entre a operação de relatório do dispositivo e o resultado da consulta. Isto é permitir que a infraestrutura de consulta para trabalhar em grande escala. Para obter vistas consistentes de utilização de duplo um único dispositivo de **getDeviceTwin** método o **registo** classe.
   > 
   > 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou uma configuração desejada como *pretendido propriedades* da solução de back-end e escreveu uma aplicação de dispositivo para detetar essa alteração e simular um processo de atualização de vários passos comunicar o seu estado através as propriedades que relatados.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
* agendar ou efetuar operações em grandes conjuntos de dispositivos Consulte o [agenda e as tarefas de difusão] [ lnk-schedule-jobs] tutorial.
* controlar dispositivos interativamente (como ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador), com o [utilizar métodos diretos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app
