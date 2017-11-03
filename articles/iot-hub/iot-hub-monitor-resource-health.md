---
title: Monitorizar o estado de funcionamento do seu IoT Hub do Azure | Microsoft Docs
description: Utilize o Monitor do Azure e o estado de funcionamento de recursos de Azure para monitorizar o seu IoT Hub e diagnosticar problemas rapidamente
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: 3051af03d0c1433db98bcc674a072188e7ce80e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorizar o estado de funcionamento do Azure IoT Hub e diagnosticar problemas rapidamente

As empresas que implementam o IoT Hub do Azure esperam um desempenho fiável a partir dos respetivos recursos. Para ajudar a manter uma veja fechar nas suas operações, o IoT Hub está totalmente integrado [Azure Monitor] [ lnk-AM] e [estado de funcionamento de recursos de Azure] [ lnk-ARH]. Estes dois serviços funcionam em conjunto para lhe fornecer os dados que precisa para manter as suas soluções de IoT cópias de segurança e em execução em bom estado. 

Monitor do Azure é uma única origem de monitorização e o registo para todos os seus serviços do Azure. Pode enviar os registos do Azure Monitor gera a análise de registos do OMS, os Event Hubs ou armazenamento do Azure para o processamento personalizado. Definições de métricas e diagnóstico do Azure Monitor dão-lhe visibilidade em tempo real sobre o desempenho dos seus recursos. Continuar a ler este artigo para saber como [utilização do Azure Monitor](#use-azure-monitor) com o seu IoT hub. 

Estado de funcionamento de recursos do Azure ajuda-o a diagnosticar e obter suporte, quando um problemas do Azure tem impacto sobre os recursos. Um dashboard personalizado fornece o estado de funcionamento atuais e anteriores, para os Hubs IoT. Continuar a ler este artigo para saber como [utilizar o Azure Resource Health](#use-azure-resource-health) com o seu IoT hub. 

Para além de integração com esses dois serviços, o IoT Hub também fornece as suas próprias métricas que pode utilizar para compreender o estado dos seus recursos de IoT. Para obter mais informações, consulte [métricas de compreender o IoT Hub][lnk-metrics].

## <a name="use-azure-monitor"></a>Utilizar o Azure Monitor

Monitor do Azure fornece informações de diagnóstico de nível de recursos, o que significa que pode monitorizar as operações que tenham lugar no seu IoT hub. 

Substitui de definições de diagnóstico do Azure Monitor as operações de IoT Hub monitorizar. Se tiver atualmente operações do utilizador de monitorização, deve migrar os fluxos de trabalho. Para obter mais informações, consulte [migrar de operações de definições de monitorização para o diagnóstico][lnk-migrate].

Para saber mais sobre as métricas específicas e os eventos que monitoriza os Monitor do Azure, consulte [suportado métricas com a monitorização do Azure] [ lnk-AM-metrics] e [suportado categorias, esquemas e serviços do Azure Os registos de diagnóstico][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Compreender os registos

Monitor do Azure controla operações diferentes que ocorrem no IoT Hub. Cada categoria tem um esquema que define a forma como são comunicados eventos dessa categoria. 

#### <a name="connections"></a>Ligações

A categoria de ligações controla erros que ocorrem quando os dispositivos ligarem ou desligar de um hub IoT. Esta categoria de controlo é útil para identificar as tentativas de ligação não autorizado e para controlar quando uma ligação é perdida para dispositivos nas áreas de conectividade fraca.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Comandos da nuvem para dispositivo

A categoria de comandos da nuvem para dispositivo controla erros ocorridos no IoT hub e estão relacionados com o pipeline de mensagens da nuvem para o dispositivo. Esta categoria inclui erros que ocorrem quando enviar mensagens da nuvem para o dispositivo (por exemplo, o remetente não autenticado), receber mensagens da nuvem para o dispositivo (por exemplo, a contagem de entrega excedido) e receber comentários da mensagem da nuvem para o dispositivo (tais como comentários expiraram). Esta categoria não interceta erros de um dispositivo que processa incorretamente uma mensagem da nuvem para o dispositivo se a mensagem da nuvem para o dispositivo foi entregue com êxito.

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>Operações de identidade de dispositivo

A categoria de operações de identidade do dispositivo controla erros que ocorrem ao tentar criar, atualizar ou eliminar uma entrada no registo de identidade do seu IoT hub. Esta categoria de controlo é útil para cenários de aprovisionamento.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "get",
    "category": "DeviceIdentityOperations",
    "level": "Error",    
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="routes"></a>Rotas

A categoria de encaminhamento de mensagens controla erros ocorridos durante a avaliação de rota de mensagem e estado de funcionamento do ponto final como percetível pelo IoT Hub. Esta categoria inclui eventos, tal como quando uma regra avalia como "", ao IoT Hub marca um ponto final como mensagens não e quaisquer outros erros recebidos a partir de um ponto final. Esta categoria não incluem erros específicos sobre as mensagens (por exemplo, o dispositivo limitação erros), que são reportados sob a categoria "telemetria do dispositivo".

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Telemetria do dispositivo

A categoria de telemetria do dispositivo controla erros ocorridos no IoT hub e estão relacionados com o pipeline de telemetria. Esta categoria inclui erros que ocorrem durante o envio de eventos de telemetria (tais como a limitação) e a receção de eventos de telemetria (por exemplo, o leitor não autorizado). Esta categoria não é possível detetar erros causados por código em execução no próprio dispositivo.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
    "location": "Resource location"
}
```

#### <a name="file-upload-operations"></a>Operações de carregamento de ficheiros

A categoria de carregamento do ficheiro controla erros ocorridos no IoT hub e estão relacionados com a funcionalidade de carregamento de ficheiros. Esta categoria inclui:

* Erros ocorridos com o URI de SAS, tal como quando expirar antes de um dispositivo notifica o hub de um carregamento concluído.
* Não foi possível carregamentos comunicados pelo dispositivo.
* Erros que ocorrem quando não for encontrado um ficheiro no armazenamento durante a criação de mensagem de notificação do IoT Hub.

Esta categoria não é possível detetar erros ocorridos diretamente enquanto o dispositivo está a carregar um ficheiro para o armazenamento.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "FileUploadOperations",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operações de nuvem para o dispositivo duplo

A categoria de operações da nuvem para o dispositivo duplo controla iniciou o serviço de eventos no dispositivos duplos. Estas operações podem incluir get duplo, atualizar propriedades comunicadas e subscrever a propriedades pretendidas

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "read",
    "category": "C2DTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operações de dispositivo para nuvem duplo

A categoria de operações do dispositivo para nuvem duplo controla iniciadas por dispositivo eventos nos dispositivos duplos. Estas operações podem incluir get duplo, atualizar ou substituir etiquetas e atualizar ou substituir propriedades pretendidas. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "update",
    "category": "D2CTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
    "location": "Resource location"
}
```

#### <a name="twin-queries"></a>Consultas de duplo

A categoria de consultas duplo relatórios nos pedidos de consulta para dispositivos duplos que sejam iniciados na nuvem. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "query",
    "category": "TwinQueries",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="jobs-operations"></a>Operações de tarefas

A categoria de operações de tarefas relatórios de pedidos de tarefas para atualizar dispositivos duplos ou da invocação de métodos diretos em vários dispositivos. Estes pedidos são iniciados na nuvem. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "jobCompleted",
    "category": "JobsOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
    "location": "Resource location"
}
```

#### <a name="direct-methods"></a>Métodos diretos

A categoria de métodos direta controla as interações reponse pedido enviadas para os dispositivos individuais. Estes pedidos são iniciados na nuvem. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Registos de leitura a partir do Event Hubs do Azure

Depois de configurar o registo através das definições de diagnóstico de eventos, pode criar aplicações que são enviados os registos de leitura, de modo a que pode tomar medidas com base nas informações nos mesmos. Este código de exemplo obtém os registos de um hub de eventos:

```
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
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
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Utilizar o estado de funcionamento de recursos do Azure

Utilize o estado de funcionamento de recursos de Azure para monitorizar se o seu IoT hub se encontra em execução. Também pode saber se a uma falha regional é afetar o estado de funcionamento do seu IoT hub. Para compreender detalhes específicos sobre o estado de funcionamento do seu IoT Hub do Azure, recomendamos que lhe [utilização do Azure Monitor](#use-azure-monitor). 

IoT Hub do Azure indica o estado de funcionamento com um nível regional. Se existir uma falha regional afetar o seu IoT hub, o estado de funcionamento é apresentado como **desconhecido**. Para saber mais sobre as verificações de estado de funcionamento específico que efetua o estado de funcionamento do Azure recursos, consulte [tipos de recursos e o estado de funcionamento verifica-se no estado de funcionamento de recursos do Azure][lnk-ARH-checks].

Para verificar o estado de funcionamento dos seus hubs IoT, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Navegue para **estado de funcionamento do serviço** > **estado de funcionamento do recurso**.
1. Das caixas de lista pendente, selecione a sua subscrição e **IoT Hub**.

Para obter mais informações sobre como interpretar os dados de estado de funcionamento, consulte [descrição geral do Estado de funcionamento de recursos do Azure][lnk-ARH]

## <a name="next-steps"></a>Passos seguintes

- [Compreender as métricas do IoT Hub][lnk-metrics]
- [Monitorização remota do IoT e notificações com Azure Logic Apps ligar o seu IoT hub e a caixa de correio][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md