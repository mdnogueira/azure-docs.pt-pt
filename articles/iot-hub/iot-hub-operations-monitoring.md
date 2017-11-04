---
title: "Operações de IoT Hub do Azure monitorização | Microsoft Docs"
description: "Como utilizar operações de IoT Hub do Azure de monitorização para monitorizar o estado de operações no seu IoT hub em tempo real."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: nberdy
ms.openlocfilehash: 3eafa32907c8f68cfc44cb2771d625349ff42003
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-operations-monitoring"></a>Monitorização de operações de IoT Hub

Monitorização de operações de IoT Hub permite-lhe monitorizar o estado das operações no seu IoT hub em tempo real. IoT Hub controla os eventos em várias categorias de operações. Pode participar num envio de eventos de uma ou mais categorias para um ponto final do seu IoT hub para processamento. Pode monitorizar os dados de erros ou configurar o processamento de mais complexo com base nos padrões de dados.

>[!NOTE]
>Monitorização de operações de IoT Hub foi preterido e será removida do IoT Hub no futuro. Para monitorizar o estado de funcionamento do IoT Hub e operações, consulte [monitorizar o estado de funcionamento do Azure IoT Hub e diagnosticar problemas rapidamente][lnk-monitor]. Para obter mais informações sobre a linha cronológica descontinuação, consulte [monitorizar as suas soluções de IoT do Azure com o Monitor do Azure e o estado de funcionamento de recursos de Azure][lnk-blog-announcement].

IoT Hub monitoriza seis categorias de eventos:

* Operações de identidade de dispositivo
* Telemetria do dispositivo
* Mensagens da nuvem para dispositivo
* Ligações
* Carregamentos de ficheiros
* Encaminhamento de mensagens

> [!IMPORTANT]
> Monitorização de operações de IoT Hub não garante a entrega ordenada ou fiável de eventos. Consoante a infraestrutura subjacente do IoT Hub, alguns eventos podem ser perdidos ou entregar fora de ordem. Utilize operações de monitorização para gerar alertas com base nos sinais de erro, como as tentativas de ligação com falhas ou interrupções de ligação de alta frequência para dispositivos específicos. Não deverá confiar nas operações de monitorização de eventos para criar um arquivo consistente para o estado do dispositivo, por exemplo, um arquivo de controlo ligado ou desligado de estado de um dispositivo. 

## <a name="how-to-enable-operations-monitoring"></a>Como ativar a monitorização de operações

1. Crie um hub IoT. Pode encontrar instruções sobre como criar um hub IoT a [começar] [ lnk-get-started] guia.

1. Abra o painel do seu IoT hub. A partir daí, clique em **operações de monitorização**.

    ![A configuração no portal de monitorização de operações de acesso][1]

1. Selecione as categorias de monitorização que pretende monitorizar e, em seguida, clique em **guardar**. Os eventos estão disponíveis para ler a partir do ponto final compatível com o Event Hub listado no **definições de monitorização**. O ponto final de IoT Hub é chamado `messages/operationsmonitoringevents`.

    ![Configurar operações de monitorização no seu IoT hub][2]

> [!NOTE]
> Selecionar **verboso** monitorização para o **ligações** categoria faz com que o IoT Hub gerar mensagens de diagnóstico adicionais. Para todas as outras categorias, o **verboso** definição alterações a quantidade de informações do IoT Hub inclui em cada mensagem de erro.

## <a name="event-categories-and-how-to-use-them"></a>Categorias de eventos e como utilizá-los

Cada operações de monitorização categoria controla um tipo diferente de interação com o IoT Hub e cada categoria de monitorização com um esquema que define a forma como são estruturados eventos dessa categoria.

### <a name="device-identity-operations"></a>Operações de identidade de dispositivo

A categoria de operações de identidade do dispositivo controla erros que ocorrem ao tentar criar, atualizar ou eliminar uma entrada no registo de identidade do seu IoT hub. Esta categoria de controlo é útil para cenários de aprovisionamento.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Telemetria do dispositivo

A categoria de telemetria do dispositivo controla erros ocorridos no IoT hub e estão relacionados com o pipeline de telemetria. Esta categoria inclui erros que ocorrem durante o envio de eventos de telemetria (tais como a limitação) e a receção de eventos de telemetria (por exemplo, o leitor não autorizado). Esta categoria não é possível detetar erros causados por código em execução no próprio dispositivo.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Comandos da nuvem para dispositivo

A categoria de comandos da nuvem para dispositivo controla erros ocorridos no IoT hub e estão relacionados com o pipeline de mensagens da nuvem para o dispositivo. Esta categoria inclui erros que ocorrem quando enviar mensagens da nuvem para o dispositivo (por exemplo, o remetente não autenticado), receber mensagens da nuvem para o dispositivo (por exemplo, a contagem de entrega excedido) e receber comentários da mensagem da nuvem para o dispositivo (tais como comentários expiraram). Esta categoria não interceta erros de um dispositivo que processa incorretamente uma mensagem da nuvem para o dispositivo se a mensagem da nuvem para o dispositivo foi entregue com êxito.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Ligações

A categoria de ligações controla erros que ocorrem quando os dispositivos ligarem ou desligar de um hub IoT. Esta categoria de controlo é útil para identificar as tentativas de ligação não autorizado e para controlar quando uma ligação é perdida para dispositivos nas áreas de conectividade fraca.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Carregamentos de ficheiros

A categoria de carregamento do ficheiro controla erros ocorridos no IoT hub e estão relacionados com a funcionalidade de carregamento de ficheiros. Esta categoria inclui:

* Erros ocorridos com o URI de SAS, tal como quando expirar antes de um dispositivo notifica o hub de um carregamento concluído.
* Não foi possível carregamentos comunicados pelo dispositivo.
* Erros que ocorrem quando não for encontrado um ficheiro no armazenamento durante a criação de mensagem de notificação do IoT Hub.

Esta categoria não é possível detetar erros ocorridos diretamente enquanto o dispositivo está a carregar um ficheiro para o armazenamento.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Encaminhamento de mensagens

A categoria de encaminhamento de mensagens controla erros ocorridos durante a avaliação de rota de mensagem e estado de funcionamento do ponto final como percetível pelo IoT Hub. Esta categoria inclui eventos, tal como quando uma regra avalia como "", ao IoT Hub marca um ponto final como mensagens não e quaisquer outros erros recebidos a partir de um ponto final. Esta categoria não incluem erros específicos sobre as mensagens (por exemplo, o dispositivo limitação erros), que são reportados sob a categoria "telemetria do dispositivo".

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="view-events"></a>Ver eventos

Pode utilizar o *iothub explorer* ferramenta para testar rapidamente a que o seu IoT hub está a gerar eventos de monitorização. Para instalar a ferramenta, consulte as instruções de [iothub explorer] [ lnk-iothub-explorer] repositório do GitHub.

1. Certifique-se de que o **ligações** monitorização categoria está definido como **verboso** no portal.

1. Numa linha de comandos, execute o seguinte comando para ler a partir do ponto final de monitorização:

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. Na linha de comandos outra, execute o seguinte comando para simular um dispositivo que envia mensagens do dispositivo para nuvem:

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. A primeira linha de comandos mostra os eventos de monitorização, como o dispositivo simulado liga ao seu IoT hub.

## <a name="connect-to-the-monitoring-endpoint"></a>Ligar ao ponto final de monitorização

O ponto final de monitorização no IoT hub é um ponto final compatível com o Event Hub. Pode utilizar qualquer mecanismo que funciona com os Hubs de eventos para ler mensagens de monitorização a partir deste ponto final. O exemplo seguinte cria um leitor básico que não é adequado para uma implementação com débito elevado. Para obter mais informações sobre como processar mensagens a partir do Event Hubs, veja o tutorial [Introdução ao Event Hubs][lnk-eventhubs-tutorial].

Para ligar ao ponto final de monitorização, necessita de uma cadeia de ligação e o nome do ponto final. Os passos seguintes mostram como localizar os valores necessários no portal:

1. No portal, navegue para o painel de recursos do IoT Hub.

1. Escolha **operações de monitorização**e anote o **nome compatível com o Event Hub** e **endpoint compatível com o Event Hub** valores:

    ![Valores de ponto final compatível com o Hub de eventos][img-endpoints]

1. Escolha **políticas de acesso partilhado**, em seguida, escolha **serviço**. Anote o **chave primária** valor:

    ![Chave primária de política de acesso partilhado serviço][img-service-key]

O seguinte exemplo de código do c# é obtido a partir de um Visual Studio **ambiente de trabalho clássico do Windows** aplicação de consola c#. O projeto tem o **Windowsazure** pacote NuGet instalado.

* Substitua o marcador de posição pela cadeia de ligação uma cadeia de ligação que utiliza o **endpoint compatível com o Event Hub** e serviço **chave primária** valores que apontou anteriormente, conforme mostrado no exemplo seguinte:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Substitua o marcador de nome de ponto final monitorização com o **nome compatível com o Event Hub** valor que apontou anteriormente.

```cs
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia para programadores do IoT Hub][lnk-devguide]
* [Simulando um dispositivo com o Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
