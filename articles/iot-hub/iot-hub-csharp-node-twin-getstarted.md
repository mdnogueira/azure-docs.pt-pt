---
title: "Introdução ao duplos de dispositivos do IoT Hub do Azure (nó/.NET) | Microsoft Docs"
description: "Como utilizar dispositivos duplos do IoT Hub do Azure para adicionar etiquetas e, em seguida, utilizar uma consulta do IoT Hub. Utilizar o dispositivo IoT do Azure SDK para Node.js para implementar a aplicação de dispositivo simulado e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que adiciona as etiquetas e executa a consulta do IoT Hub."
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: elioda
ms.openlocfilehash: 4cf607e8e0ccd3aab06be54d715c2bf3777caeb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-netnode"></a>Começar a utilizar dispositivos duplos (.NET/nó)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terá uma .NET e uma aplicação de consola do Node.js:

* **AddTagsAndQuery.sln**, uma aplicação de back-end .NET, a qual adiciona as etiquetas e a consulta dispositivos duplos.
* **TwinSimulatedDevice.js**, uma aplicação Node.js que simula um dispositivo que liga ao seu IoT hub com a identidade de dispositivo que criou anteriormente e reporta a condição de conectividade.

> [!NOTE]
> O artigo [SDKs IoT do Azure] [ lnk-hub-sdks] fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivo e o back-end.
> 
> 

Para concluir este tutorial precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Criar a aplicação de serviço
Nesta secção, vai criar uma aplicação de consola do .NET (utilizando c#) adiciona os metadados de localização para o dispositivo duplo associado **myDeviceId**. Esta consulta, em seguida, os dispositivos duplos armazenados no hub IoT selecionar os dispositivos localizados nos EUA e, em seguida, aqueles que comunicou uma ligação de rede móvel.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **AddTagsAndQuery**.
   
    ![Novo projeto do Visual C# no Ambiente de Trabalho Clássico do Windows][img-createapp]
1. No Explorador de soluções, clique com botão direito do **AddTagsAndQuery** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
1. No **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **microsoft.azure.devices**. Selecione **instalar** para instalar o **Microsoft.Azure.Devices** do pacote e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço do Azure IoT][lnk-nuget-service-sdk] e às respetivas dependências.
   
    ![Janela Gestor de Pacote NuGet][img-servicenuget]
1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices;
1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Adicione o seguinte método à classe **Programa**:
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    O **RegistryManager** classe expõe todos os métodos necessários para interagir com dispositivos duplos do serviço. O código anterior primeiro inicializa a **registryManager** objeto, em seguida, obtém o dispositivo duplo para **myDeviceId**e, finalmente, atualiza as etiquetas com as informações de localização pretendida.
   
    Depois de atualizar, ser executada duas consultas: o primeiro seleciona apenas os dispositivos duplos de dispositivos localizados no **Redmond43** Jacinto e o segundo refines a consulta para selecionar apenas os dispositivos que também estão ligados através da rede celular.
   
    Tenha em atenção que o código anterior, quando cria o **consulta** objeto, especifica um número máximo de documentos devolvidos. O **consulta** objeto contém uma **HasMoreResults** booleana propriedade que pode utilizar para invocar a **GetNextAsTwinAsync** métodos múltiplas vezes para obter todos os resultados. Um método chamado **GetNextAsJson** está disponível para os resultados que são não dispositivos duplos, por exemplo, os resultados de consultas de agregação.
1. Por fim, adicione as seguintes linhas ao método **Main**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. No Explorador de soluções, abra o **definir projetos de arranque...**  e certifique-se a **ação** para **AddTagsAndQuery** projeto é **iniciar**. Compilar a solução.
1. Executar esta aplicação ao clicar no **AddTagsAndQuery** projeto e selecionar **depurar**, seguido **iniciar nova instância**. Deverá ver um dispositivo nos resultados para pedir a consulta para todos os dispositivos localizados no **Redmond43** e nenhum para a consulta que restringe os resultados para dispositivos que utilizam uma rede celular.
   
    ![Na janela de resultados da consulta][img-addtagapp]

Na secção seguinte, criar uma aplicação de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação de dispositivo
Nesta secção, pode cria uma aplicação de consola do Node.js que liga ao seu hub como **myDeviceId**e, em seguida, atualiza as respetivas propriedades comunicadas para conter as informações que esteja ligada através de uma rede celular.

1. Criar uma nova pasta vazia designada **reportconnectivity**. No **reportconnectivity** pasta, crie um novo ficheiro Package. JSON utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições.
   
    ```
    npm init
    ```
1. Na sua linha de comandos a **reportconnectivity** pasta, execute o seguinte comando para instalar o **azure-iot-device**, e **azure-iot-dispositivo-mqtt** pacote:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Com um editor de texto, crie um novo **ReportConnectivity.js** ficheiros o **reportconnectivity** pasta.
1. Adicione o seguinte código para o **ReportConnectivity.js** de ficheiros e substitua o marcador de posição para a cadeia de ligação do dispositivo com um copiou quando criou o **myDeviceId** identidade de dispositivo:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    O **cliente** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do dispositivo. O código anterior, depois, inicializa a **cliente** objeto, obtém o dispositivo duplo para **myDeviceId** e atualiza a respetiva propriedade comunicada com as informações de conectividade.
1. Executar a aplicação de dispositivo
   
        node ReportConnectivity.js
   
    Deverá ver a mensagem `twin state reported`.
1. Agora que o dispositivo comunicou as respetivas informações de conectividade, deverão ser apresentados em ambas as consultas. Execute o .NET **AddTagsAndQuery** aplicação para executar consultas novamente. Neste momento **myDeviceId** deve aparecer em ambos os resultados da consulta.
   
    ![][img-addtagapp2]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionar metadados do dispositivo como etiquetas a partir de uma aplicação de back-end e escreveu uma aplicação de dispositivo simulado para informações de conectividade do dispositivo de relatório no dispositivo duplo. Também aprendeu como consultar estas informações utilizando a linguagem de consulta do SQL Server como o IoT Hub.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
* configurar dispositivos utilizando propriedades pretendida do dispositivo duplo com a [utilização pretendida propriedades para configurar dispositivos] [ lnk-twin-how-to-configure] tutorial,
* controlar dispositivos interativamente (como ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador) com o [utilizar métodos diretos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

