> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

Este artigo fornece instruções detalhadas sobre o [código de exemplo Olá Mundo][lnk-helloworld-sample] para ilustrar os componentes fundamentais da arquitetura do [SDK do Gateway do Azure IoT][lnk-gateway-sdk]. Este exemplo utiliza o SDK do Gateway para criar um gateway simples que regista uma mensagem "olá mundo" num ficheiro a cada cinco segundos.

Estas instruções abrangem:

- **Conceitos**: uma descrição geral conceptual dos componentes que compõem qualquer gateway que crie com o SDK do Gateway.  
- **Arquitetura de exemplo de Olá Mundo**: descreve como os conceitos se aplicam ao exemplo Olá Mundo e como os componentes se encaixam.
- **Como criar o exemplo**: os passos necessários para criar o exemplo.
- **Como executar o exemplo**: os passos necessários para executar o exemplo. 
- **Resultado típico**: um exemplo do resultado a esperar quando executa o exemplo.
- **Fragmentos de código**: uma coleção de fragmentos de código para mostrar como o exemplo Olá Mundo implementa os componentes-chave do gateway.

## Conceitos do SDK do Gateway

Antes de examinar o código do exemplo ou criar o seu próprio gateway do campo com o SDK do Gateway, deve compreender os conceitos-chave que sustentam a arquitetura do SDK.

### Módulos

Pode criar um gateway com o SDK de Gateway do Azure IoT através da criação e montagem de *módulos*. Os módulos utilizam *mensagens* para trocar dados entre si. Um módulo recebe uma mensagem, efetua algumas ações na mesma, opcionalmente, transforma-a numa nova mensagem e, em seguida, publica-a para ser processada por outros módulos. Alguns módulos podem produzir apenas novas mensagens e nunca processar as mensagens recebidas. Uma cadeia de módulos cria um pipeline de processamento de dados e cada módulo realiza uma transformação dos dados a um dado momento nesse pipeline.

![][1]
 
O SDK contém o seguinte:

- Módulos pré-escritos que efetuam funções comuns do gateway.
- As interfaces que um programador pode utilizar para escrever módulos personalizados.
- A infraestrutura necessária para implementar e executar um conjunto de módulos.

O SDK fornece uma camada de abstração que lhe permite criar gateways para executar numa variedade de sistemas operativos e de plataformas.

![][2]

### Mensagens

Apesar de a ideia dos módulos a transmitir mensagens entre si ser uma forma conveniente de conceptualizar o funcionamento do gateway, esta não reflete com precisão o que acontece. Os módulos utilizam um barramento de mensagem para comunicar entre si, publicam mensagens no barramento e o barramento difunde as mensagens para todos os módulos a si ligados.

Um módulo utiliza a função **MessageBus_Publish** para publicar uma mensagem para o barramento de mensagem. O barramento de mensagem entrega mensagens a um módulo ao invocar uma função de chamada de retorno. Uma mensagem é composta por um conjunto de propriedades chave/valor e conteúdo transmitido como um bloco de memória.

![][3]

Cada módulo é responsável por filtrar as mensagens, uma vez que o barramento de mensagem utiliza um mecanismo de difusão para disponibilizar cada mensagem a cada módulo ligado a si. Um módulo só deve realizar ações nas mensagens que se destinam ao mesmo. A filtragem de mensagens cria o pipeline de mensagens de forma eficaz. Normalmente, um módulo filtra as mensagens que recebe ao utilizar as propriedades da mensagem para identificar as mensagens que deve processar.

## Arquitetura do exemplo Olá Mundo

O exemplo Olá Mundo ilustra os conceitos descritos na secção anterior. O exemplo Olá Mundo implementa um gateway que tem um pipeline composto por dois módulos:

-   O módulo *olá mundo* cria uma mensagem a cada cinco segundos e passa-a para o módulo de registo.
-   O módulo *registo* escreve num ficheiro as mensagens que recebe.

![][4]

Tal como descrito na secção anterior, o módulo Olá Mundo não passa mensagens diretamente para o módulo de registo a cada cinco segundos. Em vez disso, publica uma mensagem no barramento de mensagem a cada cinco segundos.

O módulo de registo recebe a mensagem do barramento de mensagem e inspeciona as respetivas propriedades num filtro. Se o módulo de registo determinar que deve processar a mensagem, escreve o conteúdo da mesma num ficheiro.

O módulo de registo consome apenas mensagens do barramento de mensagem, nunca publica mensagens novas no barramento.

![][5]

A figura acima mostra a arquitetura do exemplo Olá Mundo e os caminhos relativos para os ficheiros de origem que implementam partes diferentes da amostra no [repositório][lnk-gateway-sdk]. Explore o código por si ou utilize os fragmentos de código abaixo como guia.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk


<!--HONumber=Aug16_HO1-->


