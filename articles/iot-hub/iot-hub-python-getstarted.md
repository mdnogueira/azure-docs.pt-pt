---
title: "Introdução ao Hub IoT do Azure (Python) | Microsoft Docs"
description: "Aprenda a enviar mensagens do dispositivo para a cloud para um hub IoT do Azure com os SDKs para Python. Crie um dispositivo simulado e aplicações de serviço para registar o seu dispositivo, enviar mensagens e ler as mensagens do hub IoT."
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: dkshir
ms.custom: na
ms.openlocfilehash: 7ebbac4464d793717f68a4cb7905c53d1f5c051a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Ligar o dispositivo simulado ao seu hub IoT com Python
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, terá duas aplicações Python:

* **CreateDeviceIdentity.py**, que cria uma identidade de dispositivo e a chave de segurança associada para ligar a aplicação do dispositivo simulado.
* **SimulatedDevice.py**, que liga ao seu hub IoT com a identidade de dispositivo que criou anteriormente e envia periodicamente uma mensagem de telemetria através do protocolo MQTT.

> [!NOTE]
> O artigo [Azure IoT SDKs (SDKs do Azure IoT)][lnk-hub-sdks] disponibiliza informações sobre os SDKs do Azure IoT que pode utilizar para criar quer as aplicações a executar em dispositivos, quer a sua solução de back-end.
> 
> 

Para concluir este tutorial, precisa do seguinte:

* [Python 2.x ou 3.x][lnk-python-download]. Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python à variável de ambiente específica da sua plataforma. Se estiver a utilizar Python 2.x, poderá ter de [instalar ou atualizar o *pip*, o sistema de gestão de pacotes de Python][lnk-install-pip].
* Se estiver a utilizar o SO Windows, o [pacote redistribuível Visual C++][lnk-visual-c-redist], para permitir a utilização de DLLs nativas de Python.
* [Node.js 4.0 ou posterior][lnk-node-download]. Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Esta é necessária para instalar a [ferramenta Explorador do Hub IoT][lnk-iot-hub-explorer].
* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.

> [!NOTE]
> Os pacotes *pip* para `azure-iothub-service-client` e `azure-iothub-device-client` estão atualmente disponíveis apenas para o SO Windows. Para Linux/Mac OS, veja as secções específicas de SO Linux e Mac na publicação [Prepare your development environment for Python][lnk-python-devbox] (Preparar o seu ambiente de desenvolvimento para Python).
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Criou o seu hub IoT. Utilize o nome de anfitrião e a cadeia de ligação do Hub IoT no resto deste tutorial.

> [!NOTE]
> Também pode utilizar a CLI baseada no Azure para criar facilmente o seu hub IoT numa linha de comandos. O artigo [Create an IoT hub using the Azure CLI 2.0][lnk-azure-cli-hub] (Criar um hub IoT com a CLI 2.0 do Azure) mostra-lhe os passos rápidos para o fazer. 
> 

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo
Esta secção mostra os passos para criar uma aplicação de consola Python que cria uma identidade de dispositivo no registo de identidades do seu hub IoT. Só é possível ligar um dispositivo ao hub IoT, se aquele tiver uma entrada no registo de identidade. Para obter mais informações, veja a secção **Identity Registry (Registo de Identidades)** do [Hub IoT developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity]. Ao executar esta aplicação de consola, será gerado um ID de dispositivo único e uma chave que o seu dispositivo pode utilizar para identificar-se quando enviar mensagens do dispositivo para a nuvem ao IoT Hub.

1. Abra uma linha de comandos e instale o **SDK do Serviço Hub IoT do Azure para Python**. Depois de instalar o SDK, feche a linha de comandos.

    ```
    pip install azure-iothub-service-client
    ```

2. Crie um ficheiro Python denominado **CreateDeviceIdentity.py**. Abra-o num [editor/IDE para Python à sua escolha][lnk-python-ide-list], como, por exemplo, o [IDLE][lnk-idle] predefinido.

3. Adicione o código seguinte para importar os módulos necessários do SDK do serviço:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. Adicione o código seguinte, substituindo o marcador de posição para `[IoTHub Connection String]` pela cadeia de ligação do hub IoT que criou na secção anterior. Pode utilizar qualquer nome como `DEVICE_ID`.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. Adicione a função seguinte para imprimir algumas das informações do dispositivo.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. Adicione a função seguinte para criar a identificação do dispositivo com o Gestor de Registos. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. Por fim, adicione a função principal da seguinte forma e guarde o ficheiro.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. Na linha de comandos, execute **CreateDeviceIdentity.py**, da seguinte forma:

    ```python
    python CreateDeviceIdentity.py
    ```
6. Deverá ver o dispositivo simulado a ser criado. Aponte o **deviceId** e a **primaryKey** do dispositivo. Vai precisar destes valores mais tarde quando criar uma aplicação que liga ao Hub IoT como um dispositivo.

    ![Dispositivo criado com êxito][1]

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos para permitir um acesso seguro ao Hub IoT. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity].
> 
> 


## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada
Esta secção mostra os passos para criar uma aplicação de consola Python que simula um dispositivo e envia mensagens do dispositivo para a cloud para o seu hub IoT.

1. Abra uma linha de comandos nova e instale o SDK de Dispositivo do Hub IoT do Azure para Python, da seguinte forma. Após a instalação, feche a linha de comandos.

    ```
    pip install azure-iothub-device-client
    ```
2. Crie um ficheiro chamado **SimulatedDevice.py**. Abra-o num editor/IDE para Python à sua escolha (por exemplo, o IDLE).

3. Adicione o código seguinte para importar os módulos necessários do SDK do dispositivo.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. Adicione o código seguinte e substitua o marcador de posição para `[IoTHub Device Connection String]` pela cadeia de ligação do seu dispositivo. Geralmente, a cadeia de ligação do dispositivo tem o formato `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`. Utilize o **deviceId** e a **primaryKey** do dispositivo que criou na secção anterior para substituir `<deviceId>` e `<primaryKey>` respetivamente. Substitua `<hostName>` pelo nome de anfitrião do hub IoT, geralmente como `<IoT hub name>.azure-devices.net`.

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. Adicione o código seguinte para definir o envio de uma chamada de retorno de confirmação. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. Adicione o código seguinte para inicializar o cliente do dispositivo.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        return client
    ```
7. Adicione a função seguinte para formatar e enviar uma mensagem do dispositivo simulado para o hub IoT.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. Por fim, adicione a função principal. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. Guarde e feche o ficheiro **SimulatedDevice.py**. Está agora pronto para executar esta aplicação.

> [!NOTE]
> Para facilitar, este tutorial não implementa nenhuma política de repetição. No código de produção, deve implementar as políticas de repetição (como um término exponencial), como sugerido no artigo [Transient Fault Handling (Processamento de Erros Transitórios)][lnk-transient-faults] da MSDN.
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>Receber mensagens do dispositivo simulado
Para receber mensagens de telemetria do seu dispositivo, tem de utilizar um ponto final compatível com os [Hubs de Eventos][lnk-event-hubs-overview] exposto pelo Hub IoT, que lê as mensagens do dispositivo para a cloud. Leia o tutorial [Get Started with Event Hubs][lnk-eventhubs-tutorial] (Introdução aos Hubs de Eventos) para obter informações sobre como processar mensagens a partir dos Hubs de Eventos relativas ao ponto final compatível com o Hub de Eventos do seu hub IoT. Os Hubs de Eventos ainda não suportam a telemetria em Python, pelo que pode criar uma aplicação de consola [Node.js](iot-hub-node-node-getstarted.md#D2C_node) ou [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) baseada nos Hubs de Eventos para ler as mensagens dos dispositivos para a cloud a partir do Hub IoT. Este tutorial mostra como utilizar a [ferramenta Explorador do Hub IoT][lnk-iot-hub-explorer] par ler as mensagens deste dispositivo.

1. Abra uma linha de comandos e instale o Explorador do Hub IoT. 

    ```
    npm install -g iothub-explorer
    ```

2. Execute o comando seguinte na linha de comandos para começar a monitorizar as mensagens do dispositivo para a cloud a partir do seu dispositivo. Utilize a cadeia de ligação do hub IoT no marcador de posição a seguir a `--login`.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. Abra uma nova linha de comandos e navegue para o diretório que contém o ficheiro **SimulatedDevice.py**.

4. Execute o ficheiro **SimulatedDevice.py**, que envia periodicamente dados de telemetria para o seu hub IoT. 
   
    ```
    python SimulatedDevice.py
    ```
5. Execute o Explorador do Hub IoT da secção anterior para ver as mensagens do dispositivo na linha de comandos. 

    ![Mensagens do dispositivo para a cloud Python][2]

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Utilizou esta identidade de dispositivo para que a aplicação do dispositivo simulado pudesse enviar mensagens do dispositivo para a cloud ao Hub IoT. Viu as mensagens recebidas pelo hub IoT com a ajuda da ferramenta Explorador do Hub IoT. 

Para explorar aprofundadamente o SDK Python para utilização no Hub IoT do Azure, visite este [repositório do GitHub][lnk-python-github]. Para rever as capacidades de mensagens do SDK do Serviço Hub IoT do Azure para Python, pode transferir e executar [iothub_messaging_sample.py][lnk-messaging-sample]. Para simulação do lado do dispositivo mediante a utilização do SDK de Dispositivo Hub IoT do Azure para Python, pode transferir e executar [iothub_client_sample.py][lnk-client-sample].

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, veja:

* [Ligar o seu dispositivo][lnk-connect-device]
* [Getting started with device management (Introdução à gestão de dispositivos)][lnk-device-management]
* [Começar a utilizar o Edge IoT do Azure][lnk-iot-edge]

Para saber como expandir a sua solução de IoT e processar mensagens do dispositivo para a cloud em escala, veja o tutorial [Process device-to-cloud messages (Processar mensagens do dispositivo para a cloud)][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
