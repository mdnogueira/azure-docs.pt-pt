---
title: "Introdução ao duplos de dispositivos do IoT Hub do Azure (.NET/.NET) | Microsoft Docs"
description: "Como utilizar dispositivos duplos do IoT Hub do Azure para adicionar etiquetas e, em seguida, utilizar uma consulta do IoT Hub. Utilizar o dispositivo IoT do Azure SDK para .NET para implementar a aplicação de dispositivo simulado e o serviço de IoT do Azure SDK para .NET implementar uma aplicação de serviço que adiciona as etiquetas e executa a consulta do IoT Hub."
services: iot-hub
documentationcenter: node
author: dsk-2015
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: 6073d594117e69676b753a1e3af25fffa3583a2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-twins-netnet"></a>Começar a utilizar dispositivos duplos (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terá destas aplicações de consola .NET:

* **CreateDeviceIdentity**, uma aplicação .NET que cria uma identidade de dispositivo e a chave de segurança associada para ligar a aplicação de dispositivo simulado.
* **AddTagsAndQuery**, uma aplicação de back-end do .NET que adiciona as etiquetas e a consulta dispositivos duplos.
* **ReportConnectivity**, uma aplicação de dispositivo do .NET que simula um dispositivo que liga ao seu IoT hub com a identidade de dispositivo que criou anteriormente e reporta a condição de conectividade.

> [!NOTE]
> O artigo [SDKs IoT do Azure] [ lnk-hub-sdks] fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivo e o back-end.
> 
> 

Para concluir este tutorial precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Se pretender criar a identidade do dispositivo através de programação em vez disso, leia a secção correspondente do [ligar o seu dispositivo simulado ao seu IoT hub através do .NET] [ lnk-device-identity-csharp] artigo.

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
Nesta secção, vai criar uma aplicação de consola de .NET que liga ao seu hub como **myDeviceId**e, em seguida, atualiza as respetivas propriedades comunicadas para conter as informações que esteja ligada através de uma rede celular.

1. No Visual Studio, adicione um projeto Visual C# no Ambiente de Trabalho Clássico do Windows à atual solução, utilizando o modelo de projeto **Aplicação de Consola**. Nomeie o projeto **ReportConnectivity**.
   
    ![Nova aplicação de dispositivo Visual c# clássico do Windows][img-createdeviceapp]
    
1. No Explorador de soluções, clique com botão direito do **ReportConnectivity** projeto e, em seguida, clique em **gerir pacotes NuGet...** .
1. No **Gestor de pacotes NuGet** janela, selecione **procurar** e procure **microsoft.azure.devices.client**. Selecione **instalar** para instalar o **Microsoft.Azure.Devices.Client** do pacote e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o [dispositivos IoT do Azure SDK] [ lnk-nuget-client-sdk] NuGet pacote e as respetivas dependências.
   
    ![Aplicação de cliente de janela do Gestor de pacotes NuGet][img-clientnuget]
1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação de dispositivo que anotou na secção anterior.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Adicione o seguinte método à classe **Programa**:

       public static async void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
                Console.WriteLine("Retrieving twin");
                await Client.GetTwinAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    O **cliente** objeto expõe todos os métodos necessários para interagir com dispositivos duplos do dispositivo. O código mostrado acima, inicializa a **cliente** objeto e, em seguida, obtém o dispositivo duplo para **myDeviceId**.

1. Adicione o seguinte método à classe **Programa**:
   
        public static async void ReportConnectivity()
        {
            try
            {
                Console.WriteLine("Sending connectivity data as reported property");
                
                TwinCollection reportedProperties, connectivity;
                reportedProperties = new TwinCollection();
                connectivity = new TwinCollection();
                connectivity["type"] = "cellular";
                reportedProperties["connectivity"] = connectivity;
                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

   O código acima atualizações **myDeviceId**do reportado a propriedade com as informações de conectividade.

1. Por fim, adicione as seguintes linhas ao método **Main**:
   
       try
       {
            InitClient();
            ReportConnectivity();
       }
       catch (Exception ex)
       {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
       }
       Console.WriteLine("Press Enter to exit.");
       Console.ReadLine();

1. No Explorador de soluções, abra o **definir projetos de arranque...**  e certifique-se a **ação** para **ReportConnectivity** projeto é **iniciar**. Compilar a solução.
1. Executar esta aplicação ao clicar no **ReportConnectivity** projeto e selecionar **depurar**, seguido **iniciar nova instância**. Deverá ver obter as informações de duplo e, em seguida, enviar conectividade como um *comunicadas propriedade*.
   
    ![Executar a aplicação de dispositivo a conectividade do relatório][img-rundeviceapp]
    
    
1. Agora que o dispositivo comunicou as respetivas informações de conectividade, deverão ser apresentados em ambas as consultas. Execute o .NET **AddTagsAndQuery** aplicação para executar consultas novamente. Neste momento **myDeviceId** deve aparecer em ambos os resultados da consulta.
   
    ![Conectividade de dispositivo reportada com êxito][img-tagappsuccess]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionar metadados do dispositivo como etiquetas a partir de uma aplicação de back-end e escreveu uma aplicação de dispositivo simulado para informações de conectividade do dispositivo de relatório no dispositivo duplo. Também aprendeu como consultar estas informações utilizando a linguagem de consulta do SQL Server como o IoT Hub.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub] [ lnk-iothub-getstarted] tutorial,
* configurar dispositivos utilizando propriedades pretendida do dispositivo duplo com a [utilização pretendida propriedades para configurar dispositivos] [ lnk-twin-how-to-configure] tutorial,
* controlar dispositivos interativamente (como ativar uma ventoinha a partir de uma aplicação controlados pelo utilizador) com o [utilizar métodos diretos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

