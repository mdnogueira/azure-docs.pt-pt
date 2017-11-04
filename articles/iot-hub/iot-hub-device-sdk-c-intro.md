---
title: O dispositivo IoT do Azure SDK para C | Microsoft Docs
description: "Introdução ao dispositivo IoT do Azure SDK para C e saiba como criar aplicações de dispositivos que comunicam com um IoT hub."
services: iot-hub
documentationcenter: 
author: olivierbloch
manager: timlt
editor: 
ms.assetid: e448b061-6bdd-470a-a527-15ec03cca7b9
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: obloch
ms.openlocfilehash: 9f0ea741355c6403cde6f02d8263dd8bff7e553b
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-device-sdk-for-c"></a>Dispositivos de IoT do Azure SDK para C

O **dispositivos IoT do Azure SDK** é um conjunto de bibliotecas concebidos para simplificar o processo de envio de mensagens e receber mensagens do **IoT Hub do Azure** serviço. Existem diferentes variações do SDK, cada direcionada para uma plataforma específica, mas este artigo descreve o **dispositivos de IoT do Azure SDK para C**.

O dispositivo IoT do Azure SDK para C é escrito em ANSI C (C99) para maximizar a portabilidade. Esta funcionalidade torna as bibliotecas adequada para operar em várias plataformas e dispositivos, especialmente quando minimiza o disco e requisitos de espaço de memória é uma prioridade.

Existem uma vasta gama de plataformas onde o SDK tem sido testada (consulte o [Azure certificadas para o catálogo de dispositivos de IoT](https://catalog.azureiotsuite.com/) para obter detalhes). Embora este artigo inclui instruções de código de exemplo em execução na plataforma do Windows, o código descrito neste artigo é idêntico em toda a gama de plataformas suportadas.

Este artigo apresenta-lhe a arquitetura do dispositivo IoT do Azure SDK para C. -Demonstra como inicializar a biblioteca de dispositivo, envia os dados ao IoT Hub e receber mensagens a partir do mesmo. As informações neste artigo devem ser suficientes para começar a utilizar o SDK, mas também fornece indicações para informações adicionais sobre as bibliotecas.

## <a name="sdk-architecture"></a>Arquitetura do SDK

Pode encontrar o [ **dispositivos de IoT do Azure SDK para C** ](https://github.com/Azure/azure-iot-sdk-c) os detalhes de vista e repositório GitHub da API no [referência da API de C](https://azure.github.io/azure-iot-sdk-c/index.html).

A versão mais recente das bibliotecas pode ser encontrada no **mestre** ramo do repositório:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

* O principal do SDK está a **iothub\_cliente** pasta que contém a implementação da camada de API mais baixa no SDK: o **IoTHubClient** biblioteca. O **IoTHubClient** biblioteca contém APIs implementar em bruto de mensagens para enviar mensagens ao IoT Hub e receber mensagens a partir do IoT Hub. Quando utilizar esta biblioteca, é responsáveis por implementar a serialização da mensagem, mas outros detalhes de comunicar com o IoT Hub são tratados por si.
* O **serializador** pasta contém funções de programa auxiliar e exemplos mostram como serializar dados antes de enviar para o IoT Hub do Azure utilizando a biblioteca de cliente. A utilização do serializador não é obrigatória e é fornecida para efeitos práticos. Para utilizar o **serializador** biblioteca, pode define um modelo que especifica os dados para enviar para o IoT Hub e as mensagens de espera para receber a partir do mesmo. Assim que o modelo está definido, o SDK fornece-lhe uma superfície de API que permite-lhe trabalhar facilmente com mensagens dispositivo-nuvem e nuvem para o dispositivo sem se preocupar com os detalhes de serialização. A biblioteca depende de outras bibliotecas de open source para implementam o transporte através de protocolos como MQTT e AMQP.
* O **IoTHubClient** biblioteca depende de outras bibliotecas de open source para:
  * O [Azure C partilhado utilitário](https://github.com/Azure/azure-c-shared-utility) biblioteca, que fornece uma funcionalidade comum para as tarefas básicas (por exemplo, cadeias, manipulação de lista de e/s) necessária entre os vários SDKs C relacionadas com o Azure.
  * O [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) biblioteca, o que é uma implementação do lado do cliente AMQP otimizado para dispositivos de recurso restringido.
  * O [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) biblioteca, o que é uma biblioteca para fins gerais que implementar o protocolo MQTT e otimizados para dispositivos de recurso restringido.

Utilização destas bibliotecas é mais fácil de compreender ao observar o código de exemplo. As secções seguintes descrevem várias aplicações de exemplo que estão incluídas no SDK. Estas instruções deverá dar-lhe um bom funcionamento para as diversas capacidades das camadas da arquitetura do SDK e uma introdução ao como funcionam as APIs.

## <a name="before-you-run-the-samples"></a>Antes de executar os exemplos

Antes de poder executar os exemplos no dispositivo IoT do Azure SDK para C, tem [criar uma instância do serviço IoT Hub](iot-hub-create-through-portal.md) na sua subscrição do Azure. Em seguida, conclua as seguintes tarefas:

* Preparar o ambiente de desenvolvimento
* Obter credenciais de dispositivo.

### <a name="prepare-your-development-environment"></a>Preparar o ambiente de desenvolvimento

Pacotes que são fornecidos para comuns plataformas (como NuGet para Windows ou apt_get para Debian e Ubuntu) e os exemplos utilizam estes pacotes quando disponível. Em alguns casos, tem de compilar o SDK para ou no seu dispositivo. Se precisar de compile o SDK, consulte [preparar o ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) no repositório GitHub.

Para obter o código de aplicação de exemplo, transfira uma cópia do SDK a partir do GitHub. Obter a cópia da origem do **mestre** ramo do [repositório do GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Obter as credenciais de dispositivo

Agora que tem o código de origem de exemplo, a seguinte coisa a fazer é obter um conjunto de credenciais de dispositivo. Para um dispositivo poder aceder a um IoT hub, primeiro tem de adicionar o dispositivo no registo de identidade do IoT Hub. Quando adicionar o seu dispositivo, obter um conjunto de credenciais de dispositivo que necessita para o dispositivo conseguir estabelecer ligação ao IoT hub. As aplicações de exemplo abordadas na secção seguinte esperam estas credenciais no formato de um **a cadeia de ligação do dispositivo**.

Existem várias ferramentas open source para ajudar a gerir o seu IoT hub.

* Uma aplicação do Windows chamada [Explorador de dispositivo](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
* Uma ferramenta CLI plataforma node.js chamado [iothub explorer](https://github.com/azure/iothub-explorer).

Este tutorial utiliza o gráfico *Explorador de dispositivo* ferramenta. Também pode utilizar o *iothub explorer* ferramenta se preferir utilizar uma ferramenta CLI.

A ferramenta Explorador do dispositivo utiliza as bibliotecas de serviço do Azure IoT para executar várias funções no IoT Hub, incluindo a adição de dispositivos. Se utilizar a ferramenta Explorador do dispositivo para adicionar um dispositivo, que obtém uma cadeia de ligação para o seu dispositivo. É necessário esta cadeia de ligação para executar as aplicações de exemplo.

Se não estiver familiarizado com a ferramenta Explorador do dispositivo, o procedimento seguinte descreve como utilizá-la para adicionar um dispositivo e obter uma cadeia de ligação do dispositivo.

Para instalar a ferramenta Explorador do dispositivo, consulte [como utilizar o Explorador do dispositivo para dispositivos do IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

Quando executar o programa, consulte esta interface:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Introduza o **cadeia de ligação do Hub IoT** no primeiro campo e clique **atualização**. Este passo configura a ferramenta de modo a que este consegue comunicar com IoT Hub.

Quando é configurada a cadeia de ligação do IoT Hub, clique em de **gestão** separador:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Este separador é onde irá gerir os dispositivos registados no seu IoT hub.

Criar um dispositivo, clicando a **criar** botão. Apresenta uma caixa de diálogo com um conjunto de chaves pré-preenchidos (principais e secundários). Introduza um **ID de dispositivo** e, em seguida, clique em **criar**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Quando o dispositivo é criado, os dispositivos listam atualizações com todos os dispositivos registados, incluindo o que acabou de criar. Se clique no seu novo dispositivo, consulte este menu:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Se optar por **copie a cadeia de ligação para o dispositivo selecionado**, a cadeia de ligação do dispositivo é copiada para a área de transferência. Manter uma cópia da cadeia de ligação do dispositivo. Apenas quando executar as aplicações de exemplo descritas nas secções seguintes.

Quando tiver concluído os passos acima, está pronto para iniciar a execução de alguns códigos. Ambos os exemplos de ter uma constante na parte superior do ficheiro de origem principal que permite-lhe introduzir uma cadeia de ligação. Por exemplo, a linha correspondente a partir de **iothub\_cliente\_exemplo\_mqtt** aplicação aparece da seguinte forma.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Utilizar a biblioteca de IoTHubClient

Dentro do **iothub\_cliente** pasta no [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) repositório, há um **amostras** pasta que contenha uma aplicação denominada **iothub\_cliente\_exemplo\_mqtt**.

A versão do Windows do **iothub\_cliente\_exemplo\_mqtt** aplicação inclui a seguinte solução do Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-mqtt.PNG)

> [!NOTE]
> Se abrir este projeto no Visual Studio 2017, aceita os pedidos para redirigir o projeto para a versão mais recente.

Esta solução contém um único projeto. Existem quatro pacotes de NuGet instalados nesta solução:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Precisa sempre o **Microsoft.Azure.C.SharedUtility** quando estiver a trabalhar com o SDK do pacote. Este exemplo utiliza o protocolo MQTT, por conseguinte, tem de incluir o **Microsoft.Azure.umqtt** e **Microsoft.Azure.IoTHub.MqttTransport** pacotes (existem pacotes equivalentes para AMQP e HTTPS). Porque este exemplo utiliza o **IoTHubClient** biblioteca, também tem de incluir o **Microsoft.Azure.IoTHub.IoTHubClient** pacote na sua solução.

Pode encontrar a implementação para a aplicação de exemplo no **iothub\_cliente\_exemplo\_mqtt.c** ficheiro de origem.

Os seguintes passos utilizarem esta aplicação de exemplo para guiá-lo a que tem necessário para utilizar o **IoTHubClient** biblioteca.

### <a name="initialize-the-library"></a>Inicializar a biblioteca

> [!NOTE]
> Antes de começar a trabalhar com as bibliotecas, poderá ter de efetuar alguns inicialização específicos da plataforma. Por exemplo, se planear utilizar AMQP no Linux tem de inicializar a biblioteca de OpenSSL. Exemplos de [repositório do GitHub](https://github.com/Azure/azure-iot-sdk-c) chamada à função de utilitário **plataforma\_init** quando o cliente inicia e chame o **plataforma\_deinit** função antes de sair. Estas funções são declaradas no ficheiro de cabeçalho platform.h. Examine as definições destas funções para a sua plataforma de destino no [repositório](https://github.com/Azure/azure-iot-sdk-c) para determinar se tem de incluir qualquer código de inicialização de plataforma específica no seu cliente.

Para começar a trabalhar com as bibliotecas, primeiro alocar um identificador de cliente do IoT Hub:

```c
if ((iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Passar uma cópia da cadeia de ligação de dispositivo obtida a ferramenta Explorador do dispositivo para esta função. Designar também o protocolo de comunicações a utilizar. Este exemplo utiliza MQTT, mas AMQP e HTTPS também são opções.

Quando tiver um **IOTHUB\_cliente\_PROCESSAR**, pode começar a chamar as APIs para enviar e receber mensagens e de IoT Hub.

### <a name="send-messages"></a>Enviar mensagens

A aplicação de exemplo configura um ciclo para enviar mensagens para o seu IoT hub. O fragmento seguinte:

- Cria uma mensagem.
- Adiciona uma propriedade para a mensagem.
- Envia uma mensagem.

Em primeiro lugar, crie uma mensagem:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Sempre que enviar uma mensagem, especifique uma referência a uma função de chamada de retorno que é invocada quando os dados são enviados. Neste exemplo, a função de chamada de retorno é chamada **SendConfirmationCallback**. O fragmento seguinte mostra esta função de chamada de retorno:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Tenha em atenção a chamada para o **IoTHubMessage\_destruir** funcionar quando tiver terminado com a mensagem. Esta função liberta os recursos alocados quando criou a mensagem.

### <a name="receive-messages"></a>Receber mensagens

Receber uma mensagem é uma operação assíncrona. Em primeiro lugar, registe a chamada de retorno para invocar quando o dispositivo recebe uma mensagem:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
...
```

O último parâmetro existe um ponteiro nulo a que pretende. No exemplo, é um ponteiro para um número inteiro, mas pode ser um apontador para uma estrutura de dados mais complexa. Este parâmetro permite que a função de chamada de retorno funcionar no estado partilhado com a função chamadora desta função.

Quando o dispositivo recebe uma mensagem, a função de chamada de retorno registado é invocada. Obtém esta função de chamada de retorno:

* O id de mensagem e o id de correlação da mensagem.
* O conteúdo da mensagem.
* Quaisquer propriedades personalizadas da mensagem.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Utilize o **IoTHubMessage\_GetByteArray** função para obter a mensagem, que, neste exemplo, é uma cadeia.

### <a name="uninitialize-the-library"></a>Uninitialize a biblioteca

Quando tiver terminado eventos de envio e receção de mensagens, pode uninitialize a biblioteca de IoT. Para fazê-lo, emita a chamada de função seguintes:

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Esta chamada liberta os recursos anteriormente atribuídos pelo **IoTHubClient\_CreateFromConnectionString** função.

Como pode ver, é fácil de enviar e receber mensagens com a **IoTHubClient** biblioteca. A biblioteca processa os detalhes de comunicar com o IoT Hub, incluindo o protocolo a utilizar (de perspetiva do programador, esta é uma opção de configuração simples).

O **IoTHubClient** biblioteca também fornece controlo preciso sobre como serializar os dados do seu dispositivo envia ao IoT Hub. Em alguns casos, este nível de controlo é uma vantagem, mas outros é um detalhe de implementação que não pretende ser preocupado com. Se for esse o caso, poderá considerar a utilização de **serializador** biblioteca, o que é descrita na secção seguinte.

## <a name="use-the-serializer-library"></a>Utilizar a biblioteca de serializador

Concecionais o **serializador** biblioteca encontra-se do **IoTHubClient** biblioteca no SDK. Utiliza o **IoTHubClient** biblioteca para a comunicação subjacente com o IoT Hub, mas adiciona funcionalidades de modelação que remova o fardo de lidar com a serialização da mensagem do programador. Como funciona esta biblioteca é melhor demonstrado por um exemplo.

Dentro de **serializador** pasta no [repositório azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c), é um **amostras** chamado de pasta que contém uma aplicação **simplesample\_mqtt**. A versão do Windows deste exemplo inclui a seguinte solução do Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_mqtt.PNG)

> [!NOTE]
> Se abrir este projeto no Visual Studio 2017, aceita os pedidos para redirigir o projeto para a versão mais recente.

Tal como acontece com o exemplo anterior, este inclui vários pacotes de NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Que viu a maioria destes pacotes no exemplo anterior, mas **Microsoft.Azure.IoTHub.Serializer** há de novo. Este pacote é necessário quando utiliza o **serializador** biblioteca.

Pode encontrar a implementação da aplicação exemplo o **simplesample\_mqtt.c** ficheiro.

As secções seguintes guiá-lo através das partes principais deste exemplo.

### <a name="initialize-the-library"></a>Inicializar a biblioteca

Para começar a trabalhar com o **serializador** biblioteca, chame a inicialização APIs:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

A chamada para o **serializador\_init** é uma única chamada de função e inicializa a biblioteca subjacente. Em seguida, chame o **IoTHubClient\_odas\_CreateFromConnectionString** função, o que é a mesma API em como o **IoTHubClient** exemplo. Esta chamada define a cadeia de ligação do dispositivo (esta chamada é também onde escolher o protocolo que pretende utilizar). Este exemplo utiliza MQTT como transporte, mas pode utilizar AMQP ou HTTPS.

Por fim, chame o **criar\_modelo\_instância** função. **WeatherStation** é o espaço de nomes do modelo e **ContosoAnemometer** é o nome do modelo. Assim que a instância de modelo for criada, pode utilizá-lo para iniciar o envio e receção de mensagens. No entanto, é importante compreender que um modelo é.

### <a name="define-the-model"></a>Definir o modelo

Um modelo no **serializador** biblioteca define as mensagens que o dispositivo pode enviar ao IoT Hub e as mensagens, denominadas *ações* na linguagem de modelagem, pode receber. Definir um modelo com um conjunto de C macros como o **simplesample\_mqtt** aplicação de exemplo:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

O **começar\_espaço de nomes** e **final\_espaço de nomes** macros ambos os usar o espaço de nomes do modelo como um argumento. É esperado que nada entre estes macros é a definição do seu modelo ou modelos e as estruturas de dados que utilizam os modelos.

Neste exemplo, é um modelo único chamado **ContosoAnemometer**. Este modelo define dois tipos de dados que o dispositivo pode enviar ao IoT Hub: **DeviceId** e **WindSpeed**. Também define três ações (mensagens) que o dispositivo pode receber: **TurnFanOn**, **TurnFanOff**, e **SetAirResistance**. Cada elemento de dados tem um tipo e cada ação tem um nome (e opcionalmente um conjunto de parâmetros).

Os dados e as ações definidas no modelo de definem uma superfície de API que pode utilizar para enviar mensagens ao IoT Hub e responder às mensagens enviadas para o dispositivo. A utilização deste modelo é melhor compreendida através de um exemplo.

### <a name="send-messages"></a>Enviar mensagens

O modelo define os dados que pode enviar para o IoT Hub. Neste exemplo, o que significa que um dos itens de dados de dois definidos utilizando o **WITH_DATA** macro. Existem vários passos necessários para enviar **DeviceId** e **WindSpeed** valores para um hub IoT. O primeiro consiste em definir os dados que pretende enviar:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

O modelo definido anteriormente permite-lhe definir os valores definindo membros de um **struct**. Em seguida, a serialização da mensagem que pretende enviar:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Este código serializa a dispositivo para a nuvem para uma memória intermédia (referenciado pela **destino**). O código, em seguida, invoca o **sendMessage** função para enviar a mensagem ao IoT Hub:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```


O segundo para o último parâmetro da **IoTHubClient\_odas\_SendEventAsync** é uma referência a uma função de chamada de retorno que é chamada quando os dados são enviados com êxito. Segue-se a função de chamada de retorno do exemplo:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

O segundo parâmetro é um ponteiro para o contexto de utilizador o mesmo apontador transmitido a **IoTHubClient\_odas\_SendEventAsync**. Neste caso, o contexto é um contador simple, mas pode ser qualquer coisa que pretende.

É tudo há para envio de mensagens do dispositivo para a nuvem. A única coisa que à esquerda para cobrir é como receber mensagens.

### <a name="receive-messages"></a>Receber mensagens

Receber uma mensagem funciona da mesma forma como as mensagens de forma funcionam no **IoTHubClient** biblioteca. Em primeiro lugar, registe uma função de chamada de retorno de mensagem:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Em seguida, escrever a função de chamada de retorno que é invocada quando é recebida uma mensagem:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Este código é automático – é o mesmo para qualquer solução. Esta função recebe a mensagem e encarrega-se de encaminhamento para a função adequada através da chamada para **executar\_comando**. A função chamada depende neste momento a definição das ações no seu modelo.

Quando definir uma ação no seu modelo, está a necessários para implementar uma função que é chamada quando o dispositivo receber a mensagem correspondente. Por exemplo, se o seu modelo define esta ação:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Defina uma função com esta assinatura:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Tenha em atenção a como o nome da função corresponde ao nome da ação no modelo e se os parâmetros da função correspondem aos parâmetros especificados para a ação. O primeiro parâmetro é sempre necessário e contém um ponteiro para a instância do modelo.

Quando o dispositivo recebe uma mensagem que corresponde a esta assinatura, denomina-se a função correspondente. Por conseguinte, para além de ter de incluir o código automático de **IoTHubMessage**, as mensagens a receber é apenas um fim de definir uma função simple para cada ação definida no seu modelo.

### <a name="uninitialize-the-library"></a>Uninitialize a biblioteca

Quando tiver terminado o envio de dados e a receção de mensagens, pode uninitialize a biblioteca de IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Cada uma destas três funções está alinhada com três funções de inicialização descritas anteriormente. Chamar estas APIs garante que libertar recursos alocados anteriormente.

## <a name="next-steps"></a>Passos Seguintes

Este artigo abrange as noções básicas da utilização de bibliotecas no **dispositivos de IoT do Azure SDK para C**. -Lhe forneceu informações suficientes para compreender o que está incluído no SDK, a arquitetura e como começar a trabalhar com exemplos do Windows. O seguinte artigo continua a descrição do SDK por explicar [mais informações sobre a biblioteca de IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Para saber mais sobre como desenvolver para o IoT Hub, veja o [SDKs IoT do Azure][lnk-sdks].

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Simulando um dispositivo com o Azure IoT Edge][lnk-iotedge]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
