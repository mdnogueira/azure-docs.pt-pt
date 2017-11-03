## <a name="typical-output"></a>Resultado típico

O exemplo seguinte mostra a saída escrita para o ficheiro de registo pelo exemplo Olá mundo. O resultado é formatado para legibilidade:

```json
[{
    "time": "Mon Apr 11 13:42:50 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:42:50 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:42:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:43:00 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:45:00 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Fragmentos de código

Esta secção descreve algumas partes principais do código no exemplo hello\_world.

### <a name="iot-edge-gateway-creation"></a>Criação do gateway de IoT Edge

Para criar um gateway, implemente um *processo gateway*. Este programa cria a infraestrutura interna (o mediador), carrega os módulos de limite de IoT e configura o processo de gateway. O IoT Edge fornece a função **Gateway\_Create\_From\_JSON**, para lhe permitir arrancar um gateway a partir de um ficheiro JSON. Para utilizar o **Gateway\_criar\_de\_JSON** funcionar, introduzir o caminho para um ficheiro JSON que especifica os módulos de limite de IoT a carregar.

Pode encontrar o código para o processo de gateway no *Olá, mundo* exemplo no [Main] [ lnk-main-c] ficheiro. Para melhorar a legibilidade, o fragmento seguinte mostra uma versão abreviada do código de processo do gateway. Este exemplo cria um gateway e, em seguida, aguarda que o utilizador prima a tecla **ENTER** antes de fechar o gateway.

```C
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

O ficheiro de definições JSON contém uma lista de módulos de limite de IoT a carregar e as ligações entre os módulos. Cada módulo IoT limite tem de especificar um:

* **name**: um nome exclusivo para o módulo.
* **carregador**: um carregador que sabe como carregar o módulo pretendido. Carregadores são um ponto de extensão para carregar diferentes tipos de módulos. Limite de IoT fornece carregadores para utilização com módulos escritos em nativo C, Node.js, Java e .NET. O exemplo Olá mundo utiliza apenas o carregador de C nativo porque todos os módulos neste exemplo são dinâmicas bibliotecas escritas no C. Para obter mais informações sobre como utilizar os módulos de limite de IoT escritos em idiomas diferentes, consulte o [Node.js](https://github.com/Azure/iot-edge/blob/master/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample), ou [.NET](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample) amostras.
    * **nome**: o nome do carregador utilizado para carregar o módulo.
    * **entrypoint**: caminho para a biblioteca que contém o módulo. No Linux, esta biblioteca é um ficheiro do SO, no Windows, esta biblioteca é um ficheiro. dll. O ponto de entrada é específico para o tipo de carregador utilizado. O ponto de entrada do carregador de Node.js é um ficheiro. js. O ponto de entrada do Java loader é um classpath e um nome de classe. O ponto de entrada do carregador de .NET é um nome de assemblagem e um nome de classe.

* **args**: quaisquer informações de configuração de que o módulo precise.

O código seguinte mostra o JSON utilizado para declarar todos os o limite de IoT módulos para o exemplo Olá mundo no Linux. A necessidade de quaisquer argumentos por parte de um módulo depende da estrutura desse módulo. Neste exemplo, o módulo de registo assume um argumento que é o caminho para o ficheiro de saída e o módulo hello\_world não assume qualquer argumento.

```json
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

O ficheiro JSON também contém as ligações entre os módulos que são transmitidos para o mediador. As ligações têm duas propriedades:

* **origem**: um nome de módulo do `modules` secção, ou `\*`.
* **sink**: um nome de módulo da secção `modules`.

Cada ligação define uma rota e direção de mensagem. Mensagens do **origem** módulo são entregues para o **sink** módulo. Pode definir o **origem** módulo `\*`, que indica que o **sink** módulo recebe mensagens de qualquer módulo.

O código seguinte mostra o JSON utilizado para configurar ligações entre módulos utilizados no exemplo hello\_world no Linux. Todas as mensagens produzidas pelo módulo `hello_world` são consumidas pelo módulo `logger`.

```json
"links":
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="helloworld-module-message-publishing"></a>Publicação de mensagens do módulo hello\_world

Pode encontrar o código utilizado pelo módulo hello\_world para publicar mensagens no ficheiro ["hello_world.c"][lnk-helloworld-c]. O fragmento seguinte mostra uma versão modificada do código com comentários adicionais e algum código de processamento de erros removido, para melhorar a legibilidade:

```C
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

O Olá\_módulo mundo nunca processa mensagens que outros módulos de limite de IoT publiquem para o mediador. Consequentemente, a implementação da chamada de retorno da mensagem no módulo hello\_world é uma função sem operações.

```C
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-processing"></a>Processamento de mensagens do módulo de registo

O módulo de logger recebe mensagens do mediador e escreve-as num ficheiro. Nunca publica mensagens. Por conseguinte, o código do módulo Logger nunca chama a função **Broker_Publish**.

O **Logger_Receive** funcionar o [Logger] [ lnk-logger-c] ficheiro é a chamada de retorno invoca o mediador para entregar mensagens para o módulo de registo. O fragmento seguinte mostra uma versão modificada com comentários adicionais e algum código de processamento de erros removido, para melhorar a legibilidade:

```C
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, foi executado um gateway de IoT Edge simple que escreve mensagens para um ficheiro de registo. Para executar uma amostra que envia mensagens do hub IoT, consulte:

- [Limite de IoT – enviar mensagens do dispositivo-nuvem com um dispositivo simulado com Linux][lnk-gateway-simulated-linux] 
- [Limite de IoT – enviar mensagens do dispositivo-nuvem com um dispositivo simulado com Windows][lnk-gateway-simulated-windows].


<!-- Links -->
[lnk-main-c]: https://github.com/Azure/iot-edge/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/iot-edge/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/iot-edge/blob/master/modules/logger/src/logger.c
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-gateway-simulated-linux]: ../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md
[lnk-gateway-simulated-windows]: ../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md