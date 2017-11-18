---
title: Dispositivos de IoT do Azure SDK para C - IoTHubClient | Microsoft Docs
description: "Como utilizar a biblioteca de IoTHubClient do dispositivo IoT do Azure SDK para C para criar aplicações de dispositivos que comunicam com um IoT hub."
services: iot-hub
documentationcenter: 
author: olivierbloch
manager: timlt
editor: 
ms.assetid: 828cf2bf-999d-4b8a-8a28-c7c901629600
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: obloch
ms.openlocfilehash: 8428857bcd444f99ba2c0f6b31ff662d5596b591
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Dispositivos de IoT do Azure SDK para C – mais informações sobre IoTHubClient
O [primeiro artigo](iot-hub-device-sdk-c-intro.md) nesta série introduzida o **dispositivos de IoT do Azure SDK para C**. Esse artigo explicado que existem duas camadas de arquitetura no SDK. A base é a **IoTHubClient** biblioteca que gere diretamente a comunicação com o IoT Hub. Há também o **serializador** biblioteca baseia-se em cima de ou para fornecer serviços de serialização. Neste artigo fornecemos detalhes adicionais sobre o **IoTHubClient** biblioteca.

O artigo anterior descrito como utilizar o **IoTHubClient** biblioteca para enviar eventos para o IoT Hub e receber mensagens. Este artigo expande essa debate ao explicar como gerir mais precisamente *quando* enviar e receber dados, introduzindo-lhe o **APIs de nível inferior**. Também vamos explicar como anexar propriedades de eventos (e provenientes de mensagens) utilizando a propriedade de funcionalidades de processamento de **IoTHubClient** biblioteca. Por último, iremos fornecer explicação adicional de diferentes formas para processar mensagens recebidas a partir do IoT Hub.

O artigo é concluída ao que abrangem alguns tópicos diversas, incluindo mais sobre as credenciais de dispositivo e como alterar o comportamento de **IoTHubClient** através de opções de configuração.

Utilizaremos o **IoTHubClient** amostras SDK para explicar estes tópicos. Se pretender acompanhar, veja o **iothub\_cliente\_exemplo\_http** e **iothub\_cliente\_exemplo\_amqp** aplicações que estão incluídas no dispositivo IoT do Azure SDK para C. tudo descritos nas secções seguintes é demonstrado nestes exemplos.

Pode encontrar o [ **dispositivos de IoT do Azure SDK para C** ](https://github.com/Azure/azure-iot-sdk-c) os detalhes de vista e repositório GitHub da API no [referência da API de C](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-lower-level-apis"></a>As APIs de nível inferior
O artigo anterior descrito o funcionamento básico a **IotHubClient** no contexto do **iothub\_cliente\_exemplo\_amqp** aplicação. Por exemplo, é explicado como ao inicializar a biblioteca a utilizar este código.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Também é descrita como enviar eventos utilizando esta chamada de função.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

O artigo também descrita como receber mensagens ao registar a uma função de chamada de retorno.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O artigo também mostrado como a fim de libertar recursos com o código como o seguinte.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

No entanto, existem funções complementar a cada um destas APIs:

* IoTHubClient\_odas\_CreateFromConnectionString
* IoTHubClient\_odas\_SendEventAsync
* IoTHubClient\_odas\_SetMessageCallback
* IoTHubClient\_odas\_destruir

Estas funções de todos os incluem "Odas" no nome da API. Além disso, os parâmetros de cada uma destas funções são idênticos para os seus homólogos não odas. No entanto, o comportamento destas funções é diferente de uma forma importante.

Quando chamar **IoTHubClient\_CreateFromConnectionString**, as bibliotecas subjacentes criar um novo thread que é executado em segundo plano. Envia eventos para este thread e recebe mensagens do IoT Hub. Nenhum desse thread é criado quando trabalhar com as APIs do "odas". A criação do thread em segundo plano está para efeitos práticos ao programador. Não tem preocupar explicitamente enviar eventos e receber mensagens do IoT Hub – ocorre automaticamente em segundo plano. Em contrapartida, a "odas" APIs dão-lhe explícito controlo sobre a comunicação com o IoT Hub, se o ficheiro necessário.

Para melhor compreender isto, vamos ver um exemplo:

Quando chamar **IoTHubClient\_SendEventAsync**, que, na verdade, está a fazer é colocar o evento numa memória intermédia. O thread de segundo plano que criou quando chamar **IoTHubClient\_CreateFromConnectionString** continuamente monitoriza desta memória intermédia e envia quaisquer dados que contém ao IoT Hub. Isto ocorre em segundo plano ao mesmo tempo que o thread principal está a efetuar outras tarefas.

Da mesma forma, quando efetua o registo uma função de chamada de retorno para mensagens utilizando **IoTHubClient\_SetMessageCallback**, está a que dá instruções para o SDK para que o thread de segundo plano invocar a função de chamada de retorno quando uma mensagem é recebida, independentemente do thread principal.

"Odas" APIs não criar um thread de segundo plano. Em vez disso, uma nova API tem de ser chamada para explicitamente enviar e receber dados a partir do IoT Hub. Isto é demonstrado no exemplo seguinte.

O **iothub\_cliente\_exemplo\_http** aplicação que está incluída no SDK demonstra as APIs de nível inferior. Este exemplo, vamos enviar eventos ao IoT Hub com o código como o seguinte:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

As primeiros três linhas criar a mensagem e a última linha envia o evento. No entanto, tal como mencionado anteriormente, "Enviar" o evento, significa que os dados simplesmente são colocados numa memória intermédia. Nada é transmitido na rede quando chamamos **IoTHubClient\_odas\_SendEventAsync**. Por ordem para, efetivamente, a entrada os dados ao IoT Hub, tem de chamar **IoTHubClient\_odas\_DoWork**, tal como neste exemplo:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Este código (da **iothub\_cliente\_exemplo\_http** aplicação) repetidamente chama **IoTHubClient\_odas\_DoWork**. Sempre que **IoTHubClient\_odas\_DoWork** é chamado, envia alguns eventos de memória intermédia ao IoT Hub e obtém uma mensagem em fila que está a ser enviada para o dispositivo. As maiúsculas e minúsculas última significa que se registou é uma função de chamada de retorno de mensagens, em seguida, a chamada de retorno é invocada (partindo do princípio de todas as mensagens são colocadas em cópia de segurança). Iremos seria registou uma função de chamada de retorno com o código como o seguinte:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

O motivo que **IoTHubClient\_odas\_DoWork** é frequentemente designado ciclo for-que sempre que é denominado, envia *algumas* colocado na memória intermédia eventos ao IoT Hub e obtém *seguinte* mensagem colocada em fila para o dispositivo. Cada chamada não é garantida para enviar eventos colocados em memória intermédia todos ou obter todas as mensagens em fila. Se pretender enviar todos os eventos na memória intermédia e, em seguida, prosseguir no processamento adicional, pode substituir este ciclo com o código como o seguinte:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Este código chama **IoTHubClient\_odas\_DoWork** até terem sido enviados todos os eventos na memória intermédia ao IoT Hub. Tenha em conta de que isto também implica que todas as mensagens em fila foram recebidas. Parte do motivo para isto é que a verificar mensagens "all" não é determinística como uma ação. O que acontece se é possível obter "tudo" das mensagens, mas, em seguida, outra é enviada para o dispositivo imediatamente após? Uma melhor forma de lidar com que está com um tempo limite programados e. Por exemplo, a função de chamada de retorno da mensagem foi possível repor um temporizador sempre que é invocado. Em seguida, pode escrever a lógica para continuar o processamento se, por exemplo, nenhuma mensagem recebida nos últimos *X* segundos.

Quando está a terminar ingressing eventos e receber mensagens, não se esqueça de chamada à função correspondente para limpar os recursos.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Basicamente é apenas um conjunto de APIs para enviar e receber dados com um thread de segundo plano e outro conjunto de APIs que suporta a mesma coisa sem o thread de segundo plano. Muitos os programadores podem preferir as APIs não ODAS, mas as APIs de nível inferior são úteis quando o programador quiser explícito controlo sobre as transmissões de rede. Por exemplo, alguns dispositivos recolhem os dados ao longo do tempo e eventos de entrada apenas em intervalos especificados (por exemplo, uma vez uma hora ou uma vez por dia). As APIs de nível inferior dão-lhe a capacidade para controlar explicitamente quando enviar e receber dados a partir do IoT Hub. Outras pessoas simplesmente preferirão simplicidade que fornecem as APIs de nível inferior. Tudo acontece no thread principal em vez de algumas acontecer de trabalho em segundo plano.

Lembre-se ao modelo que escolher estar consistente em que as APIs que utiliza. Se iniciar chamando **IoTHubClient\_odas\_CreateFromConnectionString**, certifique-se de utilizar apenas as APIs de nível inferior correspondentes para qualquer trabalho seguimento:

* IoTHubClient\_odas\_SendEventAsync
* IoTHubClient\_odas\_SetMessageCallback
* IoTHubClient\_odas\_destruir
* IoTHubClient\_odas\_DoWork

O oposto também se aplica. Se iniciar com **IoTHubClient\_CreateFromConnectionString**, em seguida, utilize as APIs não odas para qualquer processamento adicional.

O dispositivo IoT do Azure SDK para C, consulte o **iothub\_cliente\_exemplo\_http** aplicação para obter um exemplo completo das APIs de nível inferior. O **iothub\_cliente\_exemplo\_amqp** aplicação pode ser referenciada por um exemplo completo de não - odas APIs.

## <a name="property-handling"></a>Processamento de propriedade
Até ao momento quando que já descrito envio de dados, iremos tiver sido referir-se para o corpo da mensagem. Por exemplo, considere este código:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Neste exemplo envia uma mensagem ao IoT Hub com o texto "Olá mundo". No entanto, o IoT Hub também permite que as propriedades para ser anexados a cada mensagem. As propriedades são pares nome/valor que podem ser anexados à mensagem. Por exemplo, vamos pode modificar o código anterior para anexar uma propriedade de mensagem:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Vamos começar por chamar **IoTHubMessage\_propriedades** e passou-o identificador da nossa mensagem. O que vamos voltar é um **mapa\_PROCESSAR** referência que permite-nos começar a adicionar propriedades. A última opção é conseguido ao chamar **mapa\_AddOrUpdate**, que aceita uma referência para um mapa\_identificador, o nome de propriedade e o valor da propriedade. Com esta API adicionamos tantos propriedades como podemos como.

Quando o evento é lida **Event Hubs**, o recetor pode enumerar as propriedades e obter os valores correspondentes. Por exemplo, no .NET seria fazê acedendo a [coleção de propriedades no objeto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

No exemplo anterior, iremos está a expor propriedades para um evento que podemos enviar ao IoT Hub. Propriedades também podem ser anexadas a mensagens recebidas a partir do IoT Hub. Se quisermos obter as propriedades a partir de uma mensagem, podemos utilizar código como o seguinte na nossa função de chamada de retorno de mensagem:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

A chamada para **IoTHubMessage\_propriedades** devolve o **mapa\_PROCESSAR** referência. Vamos então, passe essa referência **mapa\_GetInternals** para obter uma referência a uma matriz de pares de nome/valor (bem como uma contagem das propriedades). Nesse momento é tão simples como de enumerar as propriedades para obter os valores que queremos.

Não tem de utilizar as propriedades na sua aplicação. No entanto, se precisar de colocá-los sobre eventos ou provenientes de mensagens, o **IoTHubClient** biblioteca torna mais fácil.

## <a name="message-handling"></a>Processamento de mensagens
Conforme indicado anteriormente, quando chegam mensagens do IoT Hub a **IoTHubClient** biblioteca responde invocando uma função de chamada de retorno registado. Não há um parâmetro de retorno desta função que deserves algumas explicação adicional. Eis um excerpt da função de chamada de retorno no **iothub\_cliente\_exemplo\_http** aplicação de exemplo:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Tenha em atenção que o tipo de retorno **IOTHUBMESSAGE\_disposição\_resultado** e neste caso específico, Devolvemos **IOTHUBMESSAGE\_ACEITES**. Existem outros valores que pode devolver a partir desta função que alteram o **IoTHubClient** biblioteca reage para a chamada de retorno da mensagem. Seguem-se as opções.

* **IOTHUBMESSAGE\_ACEITES** – a mensagem foi processada com êxito. O **IoTHubClient** biblioteca não será invocar a função de chamada de retorno com a mesma mensagem.
* **IOTHUBMESSAGE\_rejeitado** – não foi possível processar a mensagem e que não existe nenhum desire para fazê-lo no futuro. O **IoTHubClient** biblioteca não deve invocar a função de chamada de retorno com a mesma mensagem.
* **IOTHUBMESSAGE\_ABANDONED** – a mensagem não foi processada com êxito, mas o **IoTHubClient** biblioteca deve invocar a função de chamada de retorno com a mesma mensagem.

Para as duas primeiras devolvem códigos, o **IoTHubClient** biblioteca envia uma mensagem ao IoT Hub com a indicação de que a mensagem deve ser eliminada da fila de dispositivo e não entregar novamente. O efeito net é o mesmo (a mensagem é eliminada da fila de dispositivo), mas se a mensagem foi aceite ou rejeitada ainda é registada.  Gravar este distinção é útil para os remetentes da mensagem, que podem oiça comentários e saber se um dispositivo tem aceite ou rejeitou uma mensagem específica.

No último caso uma mensagem também é enviada ao IoT Hub, mas ele indica que a mensagem deve ser reenviada. Normalmente, irá abandonar uma mensagem se encontrar algum erro, mas pretende tentar novamente a processar a mensagem. Em contrapartida, rejeitar uma mensagem é adequada quando ocorrer um erro irrecuperável (ou se, simplesmente, decidir que não pretende processar a mensagem).

Em qualquer caso, tenha em atenção os códigos de retorno diferentes, de modo a que pode elicit o comportamento pretendido no **IoTHubClient** biblioteca.

## <a name="alternate-device-credentials"></a>Credenciais de dispositivo alternativo
Tal como explicado anteriormente, a primeira coisa a fazer quando trabalhar com o **IoTHubClient** biblioteca é obter um **IOTHUB\_cliente\_PROCESSAR** com uma chamada, tais como o seguinte:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Os argumentos **IoTHubClient\_CreateFromConnectionString** são a cadeia de ligação do dispositivo e um parâmetro que indica o protocolo são utilizadas para comunicar com IoT Hub. A cadeia de ligação do dispositivo tem um formato que aparece da seguinte forma:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Existem quatro tipos de informações nesta cadeia: nome do IoT Hub, sufixo do IoT Hub, ID de dispositivo e chave de acesso partilhado. Obter o nome de domínio completamente qualificado (FQDN) de um hub IoT, quando criar a instância do hub IoT no portal do Azure — Isto dá-lhe o nome do hub IoT (a primeira parte do FQDN) e o sufixo do hub IoT (o resto do FQDN). Obter o ID de dispositivo e a chave de acesso partilhado quando registar o seu dispositivo com o IoT Hub (conforme descrito no [artigo anterior](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** dá-lhe uma forma de inicializar a biblioteca. Se preferir, pode criar um novo **IOTHUB\_cliente\_PROCESSAR** utilizando estes parâmetros individuais, em vez da cadeia de ligação do dispositivo. Isto é conseguido com o seguinte código:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Isto elimina a mesma coisa como **IoTHubClient\_CreateFromConnectionString**.

Pode parecer que pretenda utilizar óbvios **IoTHubClient\_CreateFromConnectionString** em vez deste método de inicialização mais verboso. Tenha em atenção, no entanto, que, quando registar um dispositivo no IoT Hub, obter é um ID de dispositivo e a chave do dispositivo (não uma cadeia de ligação). O *Explorador de dispositivo* ferramenta SDK introduzida no [artigo anterior](iot-hub-device-sdk-c-intro.md) utiliza bibliotecas no **SDK do serviço do Azure IoT** para criar a cadeia de ligação do dispositivo o ID de dispositivo, dispositivo, da chave e do nome de anfitrião do IoT Hub. Por isso, ao chamar **IoTHubClient\_odas\_criar** poderá ser preferível porque guarda o passo de gerar uma cadeia de ligação. Utilize qualquer método é conveniente.

## <a name="configuration-options"></a>Opções de configuração
Até ao momento tudo descrito sobre a forma como o **IoTHubClient** biblioteca funciona reflete o comportamento predefinido. No entanto, existem algumas opções que pode definir para alterar a forma como funciona a biblioteca. Isto é conseguido ao tirar partido de **IoTHubClient\_odas\_SetOption** API. Neste exemplo, considere:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Existem duas opções são frequentemente utilizadas:

* **SetBatching** (bool) – se **verdadeiro**, em seguida, são enviados dados enviados ao IoT Hub em lotes. Se **falso**, em seguida, são enviadas mensagens individualmente. A predefinição é **falso**. Tenha em atenção que o **SetBatching** opção só se aplica para o protocolo HTTPS e não para os protocolos MQTT ou AMQP.
* **Tempo limite** (int não assinado) – este valor é representado em milissegundos. Se a enviar um pedido HTTPS ou receber uma resposta demora mais tempo superior a esse tempo, em seguida, a ligação exceder o tempo limite.

A opção de lotes é importante. Por predefinição, os eventos de ingresses biblioteca individualmente (um único evento é que passa ao **IoTHubClient\_odas\_SendEventAsync**). Se a opção de lotes **verdadeiro**, a biblioteca recolhe os eventos tantos possível da memória intermédia (até o tamanho máximo de mensagem que irá aceitar o IoT Hub).  O lote de evento é enviado ao IoT Hub numa única chamada de HTTPS (os eventos individuais são incluídos para uma matriz JSON). Ativar a criação de batches normalmente resulta em ganhos de desempenho grande, uma vez que está a reduzir ida e volta de rede. Reduz significativamente largura de banda, uma vez que está a enviar um conjunto de cabeçalhos HTTPS com um lote de evento, em vez de um conjunto de cabeçalhos para cada evento individual. A menos que tenha um motivo específico para o fazer, caso contrário, normalmente, deverá ativar a criação de batches.

## <a name="next-steps"></a>Passos seguintes
Este artigo descreve em detalhe o comportamento de **IoTHubClient** biblioteca encontrada no **dispositivos de IoT do Azure SDK para C**. Com esta informação, deve ter uma boa compreensão das funcionalidades do **IoTHubClient** biblioteca. O [artigo seguinte](iot-hub-device-sdk-c-serializer.md) fornece semelhante detalhes sobre o **serializador** biblioteca.

Para saber mais sobre como desenvolver para o IoT Hub, veja o [SDKs IoT do Azure][lnk-sdks].

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
