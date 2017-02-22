## <a name="typical-output"></a>Resultado típico
Segue-se um exemplo do resultado escrito no ficheiro de registo pelo exemplo Olá Mundo. Foram adicionados carateres de Nova linha e Tabulação para melhorar a legibilidade:

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Fragmentos de código
Esta secção descreve algumas partes principais do código no exemplo Olá Mundo.

### <a name="gateway-creation"></a>Criação do gateway
O programador deve escrever o *processo do gateway*. Este programa cria a infraestrutura interna (o mediador), carrega os módulos e configura tudo para que funcione corretamente. O SDK fornece a função **Gateway_Create_From_JSON** para permitir o arranque de um gateway a partir de um ficheiro JSON. Para utilizar a função **Gateway_Create_From_JSON**, deve introduzir o caminho para um ficheiro JSON que especifique os módulos a carregar. 

Pode encontrar o código para o processo de gateway no exemplo Olá Mundo no ficheiro [main.c][lnk-main-c]. Para melhorar a legibilidade, o fragmento abaixo mostra uma versão abreviada do código de processo do gateway. Este programa cria um gateway e, em seguida, aguarda que o utilizador prima a tecla **ENTER** antes de fechar o gateway. 

```
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

O ficheiro de definições JSON contém uma lista de módulos a carregar e ligações entre os módulos.
Cada módulo tem de especificar um:

* **name**: um nome exclusivo para o módulo.
* **carregador**: um carregador que sabe como carregar o módulo pretendido.  Carregadores são um ponto de extensão para carregar diferentes tipos de módulos. Fornecemos carregadores para utilização com módulos de escrita em C nativo, Node.js, Java e .NET. A amostra Hello World utiliza apenas o carregador "nativo" uma vez que todos os módulos nesta amostra são bibliotecas dinâmicas escritas em C. Veja amostras de [Node.js](https://github.com/Azure/azure-iot-gateway-sdk/blob/develop/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/azure-iot-gateway-sdk/tree/develop/samples/java_sample), ou [.NET](https://github.com/Azure/azure-iot-gateway-sdk/tree/develop/samples/dotnet_binding_sample) para obter mais informações sobre como utilizar módulos escritos em linguagens diferentes.
    * **nome**: nome do carregador utilizado para carregar o módulo.  
    * **entrypoint**: caminho para a biblioteca que contém o módulo. Para o Linux, é um ficheiro .so, no Windows, é um ficheiro. dll. Tenha em atenção que este ponto de entrada é específico para o tipo de carregador utilizado. Por exemplo, o ponto de entrada do carregador Node. js é um ficheiro.js, o ponto de entrada do carregador Java é um classpath + nome de classe e o ponto de entrada do carregador .NET é um nome de assemblagem + nome da classe.

* **args**: quaisquer informações de configuração de que o módulo precise.

O código seguinte mostra o JSON utilizado para declarar todos os módulos para a amostra Hello World no Linux. A necessidade de quaisquer argumentos por parte de um módulo depende da estrutura desse módulo. Neste exemplo, o módulo de registo assume um argumento que é o caminho para o ficheiro de saída e o módulo Olá Mundo não assume qualquer argumento.

```
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

O ficheiro JSON também contém as ligações entre os módulos que vão ser transmitidos para o mediador. As ligações têm duas propriedades:

* **origem**: um nome de módulo da secção `modules` ou "\*".
* **sink**: um nome de módulo da secção `modules`.

Cada ligação define uma rota e direção de mensagem. As mensagens do módulo `source` são entregues no módulo `sink`. `source` poderá estar definido como "\*", o que indica que as mensagens de qualquer módulo serão recebidas pelo `sink`.

O código seguinte mostra o JSON utilizado para configurar ligações entre módulos utilizados na amostra Hello World no Linux. Todas as mensagens produzidas pelo módulo `hello_world` serão consumidas pelo módulo `logger`.

```
"links": 
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="hello-world-module-message-publishing"></a>Publicação de mensagens do módulo Olá Mundo
Pode encontrar o código utilizado pelo módulo "Olá Mundo" para publicar mensagens no ficheiro ["hello_world.c"][lnk-helloworld-c]. O fragmento abaixo mostra uma versão modificada com comentários adicionais e algum código de processamento de erros removido para melhorar a legibilidade:

```
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

### <a name="hello-world-module-message-processing"></a>Processamento de mensagens do módulo Olá Mundo
O módulo Olá Mundo não tem necessidade de processar mensagens publicadas por outros módulos no mediador. Isto torna a implementação da chamada de retorno da mensagem no módulo Olá Mundo uma função sem operações.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Processamento e publicação de mensagens do módulo de registo
O módulo Logger recebe mensagens do mediador e escreve-as num ficheiro. Nunca publica mensagens. Por conseguinte, o código do módulo Logger nunca chama a função **Broker_Publish**.

A função **Logger_Recieve** no ficheiro [logger.c][lnk-logger-c] é a chamada de retorno que o mediador invoca para a entrega de mensagens ao módulo Logger. O fragmento abaixo mostra uma versão modificada com comentários adicionais e algum código de processamento de erros removido para melhorar a legibilidade:

```
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
Para saber mais sobre como utilizar o SDK do Gateway de IoT, veja o seguinte:

* [SDK do Gateway de IoT – enviar mensagens dispositivo-cloud com um dispositivo simulado com Linux][lnk-gateway-simulated].
* [SDK do Gateway do Azure IoT][lnk-gateway-sdk] no GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md

<!--HONumber=Dec16_HO1-->


