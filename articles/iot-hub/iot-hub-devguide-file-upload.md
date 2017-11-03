---
title: Compreender o carregamento de ficheiros do IoT Hub do Azure | Microsoft Docs
description: Guia para programadores - a funcionalidade de carregamento de ficheiros do IoT Hub para gerir o carregamento de ficheiros a partir de um dispositivo para um contentor do blob storage do Azure.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 75a6b9bc3ecfe6d6901bb38e312d62333f38daf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-files-with-iot-hub"></a>Carregar ficheiros com o IoT Hub

Como detalhadas no [pontos finais de IoT Hub] [ lnk-endpoints] artigo, um dispositivo pode iniciar o carregamento de ficheiros enviando uma notificação através de um ponto final orientado para o dispositivo (**/devices/ {"deviceId"} / ficheiros**). Quando um dispositivo notifica o IoT Hub que um carregamento estiver concluído, o IoT Hub envia uma mensagem de notificação de carregamento de ficheiros através de **/messages/servicebound/filenotifications** endpoint orientado para o serviço.

Em vez de mensagens mediar através do IoT Hub próprio, o IoT Hub em vez disso, funciona como um distribuidor para uma conta de armazenamento do Azure associada. Um dispositivo de solicita um token de armazenamento do IoT Hub que é específica para o ficheiro no dispositivo que pretende carregar. O dispositivo utiliza o URI de SAS para carregar o ficheiro para o armazenamento e, quando o carregamento estiver concluído o dispositivo envia uma notificação de conclusão ao IoT Hub. IoT Hub verifica o carregamento de ficheiros está concluído e, em seguida, adiciona uma mensagem de notificação de carregamento de ficheiros para o ponto final notificação do ficheiro de orientado para o serviço.

Antes de carregar um ficheiro ao IoT Hub a partir de um dispositivo, tem de configurar o seu hub por [associar um Storage do Azure] [ lnk-associate-storage] conta ao mesmo.

O dispositivo pode, em seguida, [inicializar um carregamento] [ lnk-initialize] e, em seguida, [notificar o IoT hub] [ lnk-notify] quando a conclusão do carregamento. Opcionalmente, quando um dispositivo notifica o IoT Hub que o carregamento estiver concluído, o serviço pode gerar um [mensagem de notificação][lnk-service-notification].

### <a name="when-to-use"></a>Quando utilizar

Utilize o carregamento de ficheiros para enviar os ficheiros de suporte de dados e lotes de telemetria grande foi carregado por dispositivos ligados intermitentemente ligados ou comprimido para poupar largura de banda.

Consulte [orientações de comunicação do dispositivo para nuvem] [ lnk-d2c-guidance] se em dúvida entre a utilização de propriedades comunicadas, mensagens do dispositivo para nuvem ou carregamento do ficheiro.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associar uma conta de armazenamento do Azure IoT Hub

Para utilizar a funcionalidade de carregamento de ficheiros, primeiro tem de associar uma conta de armazenamento do Azure para o IoT Hub. Pode concluir esta tarefa através de [portal do Azure][lnk-management-portal], ou através de programação através de [fornecedor de recursos do IoT Hub REST APIs] [ lnk-resource-provider-apis]. Assim que tiver associado uma conta de armazenamento do Azure seu IoT Hub, o serviço devolve um URI de SAS a um dispositivo quando o dispositivo inicia um pedido de carregamento de ficheiros.

> [!NOTE]
> O [SDKs IoT do Azure] [ lnk-sdks] processam automaticamente ao obter o URI de SAS, carregar o ficheiro e notificar o IoT Hub de um carregamento concluído.


## <a name="initialize-a-file-upload"></a>Inicializar um carregamento de ficheiros
IoT Hub tem um ponto final especificamente para os dispositivos para pedir um URI de SAS para o armazenamento para carregar um ficheiro. Para iniciar o processo de carregamento de ficheiros, o dispositivo envia um pedido POST para `{iot hub}.azure-devices.net/devices/{deviceId}/files` com o corpo JSON seguinte:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub devolve os seguintes dados, o dispositivo utiliza para carregar o ficheiro:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Preterido: inicializar um carregamento de ficheiros com uma ação obter

> [!NOTE]
> Esta secção descreve a funcionalidade preterida como receber um URI de SAS do IoT Hub. Utilize o método POST descrito anteriormente.

IoT Hub tem dois pontos finais REST para suportar o carregamento de ficheiros, um para obter o URI de SAS para armazenamento e outro para notificar o IoT hub de um carregamento concluído. O dispositivo inicia o processo de carregamento do ficheiro enviando um GET para o IoT hub no `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. O IoT hub devolve:

* Um URI de SAS específico para o ficheiro para ser carregado.
* Um ID de correlação para ser utilizado quando o carregamento estiver concluído.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notificar o IoT Hub de um carregamento de ficheiros concluídos

O dispositivo é responsável por carregar o ficheiro para o armazenamento com os SDKs de armazenamento do Azure. Quando o carregamento estiver concluído, o dispositivo envia um pedido POST para `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` com o corpo JSON seguinte:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

O valor de `isSuccess` é um booleano que representa se o ficheiro foi carregado com êxito. O código de estado para `statusCode` é o estado para o carregamento do ficheiro para o armazenamento e o `statusDescription` corresponde do `statusCode`.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência, poderá obter mais informações sobre como carregar ficheiros a partir de um dispositivo.

## <a name="file-upload-notifications"></a>Notificações de carregamento de ficheiros

Opcionalmente, quando um dispositivo notifica o IoT Hub que um carregamento estiver concluído, o IoT Hub pode gerar uma mensagem de notificação que contém a localização de armazenamento e o nome do ficheiro.

Conforme explicado no [pontos finais][lnk-endpoints], o IoT Hub disponibiliza notificações de carregamento de ficheiros através de um ponto final de serviço com acesso à (**/messages/servicebound/fileuploadnotifications**) como mensagens. A semântica de receção de notificações de carregamento de ficheiros são iguais às mensagens da nuvem para o dispositivo e ter o mesmo [ciclo de vida de mensagem][lnk-lifecycle]. Cada mensagem obtida a partir do ponto final de notificação do carregamento de ficheiros é um registo JSON com as seguintes propriedades:

| Propriedade | Descrição |
| --- | --- |
| EnqueuedTimeUtc |Timestamp com a indicação de que a notificação foi criada. |
| DeviceId |**DeviceId** do dispositivo que carregou o ficheiro. |
| BlobUri |URI do ficheiro carregado. |
| BlobName |Nome do ficheiro carregado. |
| LastUpdatedTime |Timestamp que indica quando o ficheiro foi atualizado pela última vez. |
| BlobSizeInBytes |Tamanho do ficheiro carregado. |

**Exemplo**. Este exemplo mostra a mensagem de notificação de carregar o corpo de um ficheiro.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opções de configuração de notificação de carregamento de ficheiros

Cada IoT hub expõe as seguintes opções de configuração para notificações de carregamento de ficheiros:

| Propriedade | Descrição | Intervalo e predefinido |
| --- | --- | --- |
| **enableFileUploadNotifications** |Controla se as notificações de carregamento de ficheiros são escritas para o ponto final de notificações de ficheiro. |Bool. Predefinição: True. |
| **fileNotifications.ttlAsIso8601** |TTL predefinido para notificações de carregamento de ficheiros. |ISO_8601 intervalo até 48 H (no mínimo 1 minuto). Predefinição: 1 hora. |
| **fileNotifications.lockDuration** |Duração do bloqueio para a fila de notificações de carregamento de ficheiros. |5 a 300 segundos (mínimo 5 segundos). Predefinição: 60 segundos. |
| **fileNotifications.maxDeliveryCount** |Contagem máxima de entrega para o ficheiro de fila de notificação de carregar. |1 a 100. Predefinição: 100. |

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no guia de programadores do IoT Hub incluem:

* [Pontos finais de IoT Hub] [ lnk-endpoints] descreve os vários pontos finais que cada IoT hub expõe para operações de gestão e de tempo de execução.
* [Limitação e quotas] [ lnk-quotas] descreve as quotas e limitação comportamentos que se aplicam ao serviço do IoT Hub.
* [Azure SDKs IoT do serviço e dispositivo] [ lnk-sdks] indica o idioma de vários SDKs que pode utilizar ao desenvolver aplicações de serviço e dispositivo que interagem com o IoT Hub.
* [A linguagem de consulta do IoT Hub] [ lnk-query] descreve o idioma de consulta pode utilizar para obter informações a partir do IoT Hub sobre os dispositivos duplos e tarefas.
* [Suporte do IoT Hub MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como carregar ficheiros a partir de dispositivos com o IoT Hub, poderá estar interessado nos seguintes tópicos do guia de programadores do IoT Hub:

* [Gerir identidades de dispositivo no IoT Hub][lnk-devguide-identities]
* [Controlar o acesso ao IoT Hub][lnk-devguide-security]
* [Utilizar dispositivos duplos para sincronizar o estado e configurações][lnk-devguide-device-twins]
* [Invocar um método direto num dispositivo][lnk-devguide-directmethods]
* [Agenda de tarefas em vários dispositivos][lnk-devguide-jobs]

Se pretender experimentar alguns dos conceitos descritos neste artigo, poderá estar interessado no tutorial seguinte do IoT Hub:

* [Como carregar ficheiros a partir de dispositivos para a nuvem com o IoT Hub][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
