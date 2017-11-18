---
title: Dispositivos de IoT do Azure SDK para C - serializador | Microsoft Docs
description: "Como utilizar a biblioteca de serializador do dispositivo IoT do Azure SDK para C para criar aplicações de dispositivos que comunicam com um IoT hub."
services: iot-hub
documentationcenter: 
author: olivierbloch
manager: timlt
editor: 
ms.assetid: defbed34-de73-429c-8592-cd863a38e4dd
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2016
ms.author: obloch
ms.openlocfilehash: d8b9e147b68d16c6c166e92cbabf5b5b63e23e8d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Do Azure IoT device SDK para C – mais informações sobre o serializador
O [primeiro artigo](iot-hub-device-sdk-c-intro.md) nesta série introduzida o **dispositivos de IoT do Azure SDK para C**. O seguinte artigo fornecida uma descrição mais detalhada do [ **IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). Este artigo conclusão da cobertura do SDK ao fornecer uma descrição mais detalhada do componente restantes: o **serializador** biblioteca.

O artigo introdutório descrito como utilizar o **serializador** biblioteca para enviar eventos para e receber mensagens a partir do IoT Hub. Neste artigo, iremos expandir este debate, fornecendo uma explicação mais completa de como modelar os seus dados com o **serializador** idioma macro. O artigo também inclui mais detalhes sobre como a biblioteca serializa mensagens (e em alguns casos, como pode controlar o comportamento de serialização). Também Vamos descrever algumas parâmetros que pode modificar, determinam o tamanho dos modelos de que criar.

Por fim, o artigo revisits alguns tópicos abrangidos nos artigos anteriores, tais como a mensagem e processamento de propriedade. À medida que irá descobrir, trabalho essas funcionalidades da mesma forma utilizando o **serializador** biblioteca tal como com a **IoTHubClient** biblioteca.

Tudo descrito neste artigo baseia-se no **serializador** amostras SDK. Se pretender acompanhar, veja o **simplesample\_amqp** e **simplesample\_http** aplicações incluídas no dispositivo IoT do Azure SDK para C.

Pode encontrar o [ **dispositivos de IoT do Azure SDK para C** ](https://github.com/Azure/azure-iot-sdk-c) os detalhes de vista e repositório GitHub da API no [referência da API de C](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-modeling-language"></a>Linguagem de modelagem
O [artigo introdutório](iot-hub-device-sdk-c-intro.md) nesta série introduzida o **dispositivos de IoT do Azure SDK para C** modeling language através o exemplo fornecido no **simplesample\_amqp** aplicação:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Como pode ver, a linguagem de modelagem baseia-se num C macros. Iniciar sempre a definição com **BEGIN\_espaço de nomes** e sempre terminar com **final\_espaço de nomes**. É comum o espaço de nomes de nome para a sua empresa ou, tal como neste exemplo, o projeto que está a trabalhar.

O que fica dentro do espaço de nomes são as definições de modelo. Neste caso, não há um único modelo para um anemometer. Novamente, o modelo de nome pode ser qualquer coisa, mas normalmente isto com o nome para o dispositivo ou o tipo de dados que pretende do exchange com o IoT Hub.  

Modelos contém uma definição de eventos pode entrada ao IoT Hub (o *dados*), bem como as mensagens de poder receber IoT Hub (o *ações*). Como pode ver do exemplo, os eventos ter um tipo e um nome; ações de ter um nome e os parâmetros opcionais (cada um com um tipo).

O que não é demonstrado neste exemplo são tipos de dados adicionais que são suportados pelo SDK. Iremos abranger esse seguinte.

> [!NOTE]
> IoT Hub refere-se aos dados de um dispositivo envia ao mesmo como *eventos*, enquanto a linguagem de modelagem refere-se ao mesmo como *dados* (definidos utilizando **WITH_DATA**). Da mesma forma, o IoT Hub refere-se aos dados que envie para dispositivos como *mensagens*, enquanto a linguagem de modelagem refere-se ao mesmo como *ações* (definidos utilizando **WITH_ACTION**). Lembre-se de que estes termos podem ser utilizados neste artigo-no alternadamente.
> 
> 

### <a name="supported-data-types"></a>Tipos de dados suportados
Os seguintes tipos de dados são suportados em modelos criados com o **serializador** biblioteca:

| Tipo | Descrição |
| --- | --- |
| duplo |dupla precisão flutuante ponto número |
| Int |número inteiro de 32 bits |
| Número de vírgula flutuante |número de ponto flutuante de precisão única |
| longa |número inteiro longo |
| int8\_t |número inteiro de 8 bits |
| Int16\_t |número inteiro de 16 bits |
| Int32\_t |número inteiro de 32 bits |
| Int64\_t |número inteiro de 64 bits |
| bool |Valor booleano |
| ASCII\_char\_ptr |Cadeia de ASCII |
| EDM\_DATA\_TEMPO\_DESLOCAMENTO |compensação de tempo data |
| EDM\_GUID |GUID |
| EDM\_BINÁRIO |Binário |
| DECLARAR\_STRUCT |tipo de dados complexo |

Vamos começar com o último tipo de dados. O **DECLARE\_STRUCT** permite-lhe definir os tipos de dados complexas, que são agrupamentos dos outros tipos primitivos. Estes agrupamentos permitem-nos definir um modelo que tem este aspeto:

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Nosso modelo contém um evento de dados individual do tipo **TestType**. **TestType** é um tipo complexo que inclui vários membros coletivamente demonstram os tipos primitivos suportados pelo **serializador** modeling language.

Com um modelo como este, mas pode escrever o código para enviar dados para o IoT Hub que aparece da seguinte forma:

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Basicamente, iremos estiver a atribuir um valor para todos os membros do **teste** estrutura e, em seguida, ao chamar **SendAsync** para enviar o **teste** eventos de dados para a nuvem. **SendAsync** é uma função de programa auxiliar que enviará um evento de dados individual ao IoT Hub:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Esta função serializa o evento de dados indicado e envia-a para o IoT Hub com **IoTHubClient\_SendEventAsync**. Este é o mesmo código abordado nos artigos anteriores (**SendAsync** contém a lógica para uma função conveniente).

É uma função auxiliar utilizada no código anterior **GetDateTimeOffset**. Esta função transforma tempo indicado num valor de tipo **EDM\_data\_tempo\_deslocamento**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Se executar este código, a seguinte mensagem é enviada ao IoT Hub:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Tenha em atenção que a serialização JSON, que é o formato gerado pelo **serializador** biblioteca. Tenha também em atenção que cada membro do objeto JSON serializado corresponde aos membros de **TestType** que que definimos no nosso modelo. Os valores correspondem exatamente também são utilizados no código. No entanto, tenha em atenção que os dados binários são codificado em base64: "AQID" é o base64 codificação de {0x01, 0x02, 0x03}.

Este exemplo demonstra a vantagem de utilizar o **serializador** biblioteca – permite que enviemos JSON para a nuvem, sem ter de lidar explicitamente com serialização na nossa aplicação. Todos os temos preocupar é definir os valores dos eventos de dados no nosso modelo e, em seguida, chamar APIs simples para enviar esses eventos para a nuvem.

Com estas informações, iremos pode definir modelos que incluam o intervalo de tipos de dados suportadas, incluindo os tipos complexos (foi mesmo incluímos tipos complexos dentro de outros tipos complexos). No entanto, ele serializado JSON gerado pelo exemplo acima aparece um ponto importante. *Como* podemos enviar dados com o **serializador** biblioteca determina exatamente como o JSON foi formado. Esse ponto específico é que iremos abranger seguinte.

## <a name="more-about-serialization"></a>Mais informações sobre serialização
A secção anterior realça um exemplo de saída gerado pelo **serializador** biblioteca. Nesta secção, vamos explicar como a biblioteca serializa dados e como pode controlar esse comportamento utilizando a APIs de serialização.

Para produzir o debate sobre serialização, iremos irá trabalhar com um novo modelo baseado num thermostat. Em primeiro lugar, vamos fornecem algum conhecimento sobre o cenário estamos a tentar endereço.

Queremos modelar um thermostat que mede a temperatura e humidade. Cada conjunto de dados vai ser enviadas ao IoT Hub diferente. Por predefinição, os ingresses thermostat um evento de temperatura uma vez cada dois minutos; um evento de humidade é ingressed uma vez a cada 15 minutos. Quando o evento é ingressed, tem de incluir um timestamp que mostra o tempo que a temperatura correspondente ou humidade foi medida.

Tendo em conta neste cenário, iremos irá demonstrar duas formas diferentes para modelar os dados e vamos explicar o efeito que modelação tem na saída serializada.

### <a name="model-1"></a>Modelo 1
Segue-se a primeira versão de um modelo que suporta o cenário anterior:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Tenha em atenção que o modelo inclui dois eventos de dados: **temperatura** e **humidade**. Ao contrário dos exemplos anteriores, o tipo de cada evento é uma estrutura definida utilizando **DECLARE\_STRUCT**. **TemperatureEvent** inclui uma medida de temperatura e um carimbo; **HumidityEvent** contém uma medida de humidade e um carimbo. Este modelo permite-numa forma natural para modelar os dados para o cenário descrito acima. Podemos enviar um evento para a nuvem, quer enviaremos uma temperatura/timestamp ou um par de humidade/timestamp.

Podemos enviar um evento de temperatura nuvem com o código como o seguinte:

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Iremos irá utilizar valores codificados para relativos à temperatura e humidade no código de exemplo, mas imagine que é, na verdade, está a obter estes valores através da amostragem os sensores correspondentes no thermostat.

O código acima utiliza o **GetDateTimeOffset** programa auxiliar que foi introduzida anteriormente. Por motivos que vai ser desmarque posteriores, este código separa explicitamente a tarefa de serialização e enviar o evento. O código anterior serializa o evento de temperatura para uma memória intermédia. Em seguida, **sendMessage** é uma função de programa auxiliar (incluído no **simplesample\_amqp**) que envia o evento ao IoT Hub:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Este código é um subconjunto do **SendAsync** auxiliar descrito na secção anterior, pelo que iremos não passam-lo novamente aqui.

Quando é executado o código anterior para enviar o evento de temperatura, este formato serializado do evento é enviado ao IoT Hub:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Estamos a enviar uma temperatura que é do tipo **TemperatureEvent** e essa estrutura contém um **temperatura** e **tempo** membro. Isto é diretamente refletido nos dados serializados.

Da mesma forma, podemos enviar um evento de humidade com este código:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

O formato serializado que é enviado ao IoT Hub aparece da seguinte forma:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Novamente, este é o esperado.

Com este modelo, pode imagine eventos como adicionais podem facilmente ser adicionados. Definir as estruturas mais utilizando **DECLARE\_STRUCT**e incluir os eventos correspondentes no modelo com **WITH\_dados**.

Agora, vamos modificar o modelo de modo a que inclui os mesmos dados, mas com uma estrutura diferente.

### <a name="model-2"></a>Modelo 2
Considere este modelo alternativo ao acima:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Neste caso, iremos tiver eliminado o aperto o **DECLARE\_STRUCT** macros e está simplesmente a definir os itens de dados do nosso cenário utilizando tipos simples da linguagem de modelagem.

Apenas para o momento em que vamos ignorar a **tempo** eventos. Com que reserve, eis o código para entrada **temperatura**:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envia o seguinte evento serializado ao IoT Hub:

```
{"Temperature":75}
```

E o código para enviar o evento de humidade aparece da seguinte forma:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Este código envia esta ao IoT Hub:

```
{"Humidity":45}
```

Até ao momento ainda não existem nenhum surpresas. Agora vamos alterar como utilizamos a macro de SERIALIZAR.

O **SERIALIZAR** macro pode demorar vários eventos de dados como argumentos. Isto permite-nos serializar o **temperatura** e **humidade** eventos em conjunto e enviá-las ao IoT Hub numa chamada:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Pode adivinhar que o resultado deste código é que os dados de dois eventos são enviados ao IoT Hub:

[

{"Temperatura": 75},

{"Humidade": 45}

]

Por outras palavras, que seria de esperar que este código é igual a enviar **temperatura** e **humidade** separadamente. É apenas para efeitos práticos para passar a ambos os eventos para **SERIALIZAR** na mesma chamada. No entanto, não for esse o caso. Em vez disso, o código acima envia este evento de dados individual ao IoT Hub:

{"Temperatura": "Humidade" 75,: 45}

Isto pode parecer um porque o nosso modelo define **temperatura** e **humidade** como dois *separado* eventos:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Mais para o ponto, não tenha modelo estes eventos onde **temperatura** e **humidade** estão na mesma estrutura:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Se este modelo é utilizado, seria mais fácil de compreender como **temperatura** e **humidade** seriam enviados na mesma mensagem serializada. No entanto, não pode ser limpar porquê funciona dessa forma quando passa ambos os eventos de dados para **SERIALIZAR** utilizando o modelo de 2.

Este comportamento é mais fácil de compreender se sabe os pressupostos que o **serializador** biblioteca é efetuar. Para fazer sentido deste vamos voltar para o nosso modelo:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Considere este modelo orientado para objetos termos. Neste caso, iremos estiver a modelação um dispositivo físico (um thermostat) e que o dispositivo inclui os atributos como **temperatura** e **humidade**.

Podemos enviar o estado completo do nosso modelo com o código como o seguinte:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Pressupondo que os valores de temperatura, humidade e a hora estão definidas, seria vemos um evento semelhante enviadas ao IoT Hub:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Por vezes, poderá apenas pretender enviar *algumas* propriedades do modelo para a nuvem (isto é particularmente verdadeiro se o seu modelo contém um grande número de eventos de dados). É útil enviar apenas um subconjunto de eventos de dados, tal como no nosso exemplo anterior:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Isto gera exatamente o mesmo evento serializado como se tinha definido um **TemperatureEvent** com um **temperatura** e **tempo** membro, tal como estamos com o modelo de 1. Neste caso, foi capaz de gerar exatamente o mesmo evento serializado utilizando um modelo diferente (modelo de 2) porque telefonámos **SERIALIZAR** de forma diferente.

O ponto importante é que se passar vários eventos de dados para **SERIALIZAR,** , em seguida, pressupõe que cada evento é uma propriedade de um único objeto JSON.

A abordagem das melhores depende, e como acha que sobre o seu modelo. Se estiver a enviar "eventos" para a nuvem e cada evento contém um conjunto definido de propriedades, a primeira abordagem torna muito sentido. Caso em que pretende utilizar **DECLARE\_STRUCT** definir a estrutura de cada evento e, em seguida, inclua-los no seu modelo com o **WITH\_dados** macro. Em seguida, enviar cada evento como foi feito no primeiro exemplo acima. Esta abordagem, teria de passar apenas um evento de dados individual para **SERIALIZADOR**.

Se tem em consideração o seu modelo de forma orientado para objetos, em seguida, a abordagem segundo pode se adequarem à. Neste caso, os elementos definidos utilizando **WITH\_dados** são "Propriedades" do seu objeto. Passar qualquer subconjunto de eventos para **SERIALIZAR** que pretender, dependendo da quantidade da sua estado do objeto "" que pretende enviar para a nuvem.

Abordagem nether está correto ou erradas. Basta ter em consideração o **serializador** funciona de biblioteca e escolha a abordagem de modelação que melhor se adequa às suas necessidades.

## <a name="message-handling"></a>Processamento de mensagens
Até ao momento este artigo apenas tem abordados o envio de eventos ao IoT Hub e, ainda não resolvido receber mensagens. O motivo para isto é que é necessário saber sobre a receção de mensagens em grande parte tem sido abordado num [anteriormente artigo](iot-hub-device-sdk-c-intro.md). Recuperar desse artigo que processa mensagens ao registar a uma função de chamada de retorno de mensagem:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Em seguida, escrever a função de chamada de retorno que é invocada quando é recebida uma mensagem:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

Esta implementação do **IoTHubMessage** chamadas de função específica para cada ação no seu modelo. Por exemplo, se o seu modelo define esta ação:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Tem de definir uma função com esta assinatura:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** , em seguida, é chamado quando essa mensagem é enviada para o seu dispositivo.

Ainda estamos ainda não explicados é aspeto a versão serializada da mensagem. Por outras palavras, se pretender enviar um **SetAirResistance** mensagem para o seu dispositivo, o que does esse aspeto como?

Se estiver a enviar uma mensagem para um dispositivo, deverá fazê-lo através do SDK do serviço de IoT do Azure. Ainda precisa de saber que cadeia para enviar para invocar uma ação específica. O formato geral para enviar uma mensagem for apresentada da seguinte forma:

```
{"Name" : "", "Parameters" : "" }
```

Está a enviar um objeto JSON serializado com duas propriedades: **nome** é o nome da ação (mensagem) e **parâmetros** contém os parâmetros de ação.

Por exemplo, para invocar **SetAirResistance** pode enviar esta mensagem para um dispositivo:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

O nome da ação tem de corresponder exatamente uma ação definida no seu modelo. Os nomes de parâmetro tem de corresponder ao bem. Tenha também em atenção sensibilidade às maiúsculas e minúsculas. **Nome** e **parâmetros** estão sempre em maiúsculas. Certifique-se corresponder as maiúsculas e minúsculas do nome da ação e parâmetros no seu modelo. Neste exemplo, é o nome da ação "SetAirResistance" e não "setairresistance".

As outras duas ações **TurnFanOn** e **TurnFanOff** pode ser invocado pelo envio destas mensagens para um dispositivo:

```
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Esta secção descrito tudo o que precisa de saber quando mensagens de eventos de envio e receção com o **serializador** biblioteca. Antes de continuar, vamos abrange alguns parâmetros que pode configurar, controlam é ampliação do seu modelo.

## <a name="macro-configuration"></a>Configuração de macro
Se estiver a utilizar o **serializador** biblioteca encontra-se uma parte importante do SDK a ter em consideração na biblioteca do azure-c-partilhado-utilitário.
Se tiver clonar o repositório do Azure-iot-sdk-c a partir do GitHub, utilizando a opção - recursivo, em seguida, encontrará nesta biblioteca de utilitário partilhado aqui:

```
.\\c-utility
```

Se não tiver clonado da biblioteca, pode encontrá-lo [aqui](https://github.com/Azure/azure-c-shared-utility).

Na biblioteca utilitário partilhado, encontrará a seguinte pasta:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Esta pasta contém uma solução do Visual Studio denominada **macro\_utils\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

O programa nesta solução gera o **macro\_utils.h** ficheiro. Há uma macro predefinido\_ficheiro utils.h incluído com o SDK. Esta solução permite-lhe modificar alguns parâmetros e, em seguida, recriar o ficheiro de cabeçalho com base nestes parâmetros.

Os dois parâmetros de chaves para se deve preocupar com são **nArithmetic** e **nMacroParameters** que estão definidas nestas duas linhas encontradas na macro\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Estes valores são os parâmetros de predefinição incluídos com o SDK. Cada parâmetro tem o significado seguinte:

* nMacroParameters – controla parâmetros quantos pode ter num um DECLARE\_definição de macro de modelo.
* nArithmetic – controla o número total de membros permitidos num modelo.

O motivo que estes parâmetros são importantes é porque controlarem grande como o seu modelo pode ser. Por exemplo, considere esta definição de modelo:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Tal como mencionado anteriormente, **DECLARE\_modelo** é apenas uma macro C. Os nomes do modelo e o **WITH\_dados** instrução (ainda macro outra) é os parâmetros de **DECLARE\_modelo**. **nMacroParameters** define o número de parâmetros que podem ser incluídos no **DECLARE\_modelo**. Efetivamente, isto define quantos dados eventos ação declarações e pode ter. Como tal, com o limite predefinido de 124 Isto significa que pode definir um modelo com uma combinação de ações sobre 60 e eventos de dados. Se tentar excedem este limite, irá receber erros de compilador que ter um aspeto semelhantes a isto:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

O **nArithmetic** parâmetro é mais sobre os trabalhos internos da linguagem de macro de que a aplicação.  Controla o número total de membros pode ter no seu modelo, incluindo **DECLARE_STRUCT** macros. Se começar a ver erros do compilador como esta, em seguida, tente aumentar **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Se pretender alterar estes parâmetros, modifique os valores na macro\_utils.tt ficheiro, recompilar a macro\_utils\_h\_generator.sln solução e execute o programa compilado. Ao fazê-lo, uma nova macro\_utils.h ficheiro é gerado e colocado no.\\ comuns\\diretório inc.

Para poder utilizar a nova versão da macro\_utils.h, remova o **serializador** pacote NuGet da solução e no seu lugar incluem o **serializador** projeto do Visual Studio. Isto permite que o seu código compilar relativamente ao código de origem da biblioteca serializador. Isto inclui a macro atualizada\_utils.h. Se pretender efetuar este procedimento para **simplesample\_amqp**, comece por remover o pacote NuGet para a biblioteca de serializador da solução:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Em seguida, adicione este projeto a sua solução Visual Studio:

> . \\c\\serializador\\criar\\windows\\serializer.vcxproj
> 
> 

Quando tiver terminado, a solução deve ter o seguinte aspeto:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Agora quando compilar a solução, a macro atualizada\_utils.h está incluído na sua binário.

Tenha em atenção que a aumentar estes valores suficientemente elevadas pode exceder os limites do compilador. Para este ponto, o **nMacroParameters** é o parâmetro com o qual se deve preocupar principal. A especificação de C99 Especifica que um mínimo de 127 parâmetros são permitidos numa definição de macro. O compilador do Microsoft segue a especificação de exatamente (e tem um limite de 127), pelo que não poderá aumentar **nMacroParameters** para lá da predefinição. Outros compilers podem permitir-lhe fazê-lo (por exemplo, o compilador GNU suporta um limite superior).

Até ao momento tiver abrangidos praticamente tudo o que precisa de saber sobre como escrever código com o **serializador** biblioteca. Antes e acaba, vamos revê alguns tópicos de artigos anteriores que pode estar a pensar sobre.

## <a name="the-lower-level-apis"></a>As APIs de nível inferior
A aplicação de exemplo em que este artigo focado **simplesample\_amqp**. Este exemplo utiliza o nível mais elevado (a não-"odas") APIs para eventos de enviar e receber mensagens. Se utilizar estas APIs, é executado um thread de segundo plano que se encarrega de envio de eventos e receber mensagens. No entanto, pode utilizar as APIs de (odas) de nível inferior para eliminar este thread de segundo plano e tirar explícito controlo sobre quando enviar eventos ou receber mensagens da nuvem.

Conforme descrito num [artigo anterior](iot-hub-device-sdk-c-iothubclient.md), há um conjunto de funções que consiste as APIs do nível mais elevadas:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_destruir

Estas APIs são demonstrados **simplesample\_amqp**.

Há também um semelhante conjunto de APIs de nível inferior.

* IoTHubClient\_odas\_CreateFromConnectionString
* IoTHubClient\_odas\_SendEventAsync
* IoTHubClient\_odas\_SetMessageCallback
* IoTHubClient\_odas\_destruir

Tenha em atenção que as APIs de nível inferior funcionam exatamente da mesma forma, conforme descrito nos artigos anteriores. Pode utilizar o primeiro conjunto de APIs, se pretender que o thread de segundo plano para processar eventos de envio e receção de mensagens. Utilizar o segundo conjunto de APIs se quiser explícito controlo sobre quando enviar e receber dados a partir do IoT Hub. O conjunto de trabalho APIs igualmente bem com o **serializador** biblioteca.

Para obter um exemplo de como as APIs de nível inferior são utilizadas com o **serializador** biblioteca, consulte o **simplesample\_http** aplicação.

## <a name="additional-topics"></a>Tópicos adicionais
Alguns outros tópicos mencionar novamente são propriedade processamento, utilizando as credenciais de dispositivo alternativo e opções de configuração. Estes são todos os tópicos abrangidos um [artigo anterior](iot-hub-device-sdk-c-iothubclient.md). O ponto principal é que todas estas funcionalidades funcionam da mesma forma com o **serializador** biblioteca tal como com a **IoTHubClient** biblioteca. Por exemplo, se pretende anexar propriedades a um evento a partir do seu modelo, utilize **IoTHubMessage\_propriedades** e **mapa**\_**AddorUpdate**, da mesma forma, conforme descrito anteriormente:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Se o evento foi gerado a partir de **serializador** biblioteca ou tiver sido criado manualmente utilizando o **IoTHubClient** não importa a biblioteca.

Para as credenciais de dispositivo alternativo, utilizando **IoTHubClient\_odas\_criar** funciona apenas, bem como **IoTHubClient\_CreateFromConnectionString** para alocar um **IOTHUB\_cliente\_PROCESSAR**.

Por fim, se estiver a utilizar o **serializador** biblioteca, pode definir as opções de configuração com **IoTHubClient\_odas\_SetOption** tal como fez quando utilizar o **IoTHubClient** biblioteca.

Uma funcionalidade que é exclusiva para o **serializador** biblioteca estão a inicialização de APIs. Antes de começar a trabalhar com a biblioteca, tem de chamar **serializador\_init**:

```
serializer_init(NULL);
```

Isto é feito antes de chamar **IoTHubClient\_CreateFromConnectionString**.

Da mesma forma, quando tiver terminado a última chamada, irá efetuar a trabalhar com a biblioteca, é **serializador\_deinit**:

```
serializer_deinit();
```

Caso contrário, todas as outras funcionalidades listadas acima funcionam da mesma **serializador** biblioteca tal como **IoTHubClient** biblioteca. Para obter mais informações sobre qualquer um destes tópicos, consulte o [artigo anterior](iot-hub-device-sdk-c-iothubclient.md) nesta série.

## <a name="next-steps"></a>Passos seguintes
Este artigo descreve em detalhe os aspetos exclusivos a **serializador** biblioteca contida no **dispositivos de IoT do Azure SDK para C**. Com as informações fornecidas que devem ter uma boa compreensão sobre como utilizar modelos de eventos de enviar e receber mensagens a partir do IoT Hub.

Isto também conclui a série de três partes sobre como desenvolver aplicações com o **dispositivos de IoT do Azure SDK para C**. Deve ser suficiente informações não só começar, mas dão-lhe uma compreensão profunda sobre do como funcionam as APIs. Para obter informações adicionais, existem alguns exemplos o SDK não abordado aqui. Caso contrário, o [documentação do SDK](https://github.com/Azure/azure-iot-sdk-c) é um boa recursos para obter informações adicionais.

Para saber mais sobre como desenvolver para o IoT Hub, veja o [SDKs IoT do Azure][lnk-sdks].

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI para dispositivos de limite com limite de IoT do Azure][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
