> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)
> 
> 

Este artigo fornece instruções detalhadas sobre o [código de exemplo Olá Mundo][lnk-helloworld-sample] para ilustrar os componentes fundamentais da arquitetura do [SDK do Gateway do Azure IoT][lnk-gateway-sdk]. Este exemplo utiliza o SDK do Gateway do Hub IoT para criar um gateway simples que regista uma mensagem "olá, mundo" num ficheiro a cada cinco segundos.

Estas instruções abrangem:

* **Conceitos**: uma descrição geral conceptual dos componentes que compõem qualquer gateway que crie com o SDK do Gateway do IoT.  
* **Arquitetura de exemplo de Olá Mundo**: descreve como os conceitos se aplicam ao exemplo Olá Mundo e como os componentes se encaixam.
* **Como criar o exemplo**: os passos necessários para criar o exemplo.
* **Como executar o exemplo**: os passos necessários para executar o exemplo. 
* **Resultado típico**: um exemplo do resultado a esperar quando executa o exemplo.
* **Fragmentos de código**: uma coleção de fragmentos de código para mostrar como o exemplo Olá Mundo implementa os componentes-chave do gateway.

## <a name="azure-iot-gateway-sdk-concepts"></a>Conceitos do SDK do Gateway do Azure IoT
Antes de examinar o código do exemplo ou criar o seu próprio gateway do campo com o SDK do Gateway do IoT, deve compreender os conceitos-chave que sustentam a arquitetura do SDK.

### <a name="modules"></a>Módulos
Pode criar um gateway com o SDK de Gateway do Azure IoT através da criação e montagem de *módulos*. Os módulos utilizam *mensagens* para trocar dados entre si. Um módulo recebe uma mensagem, efetua algumas ações na mesma, opcionalmente, transforma-a numa nova mensagem e, em seguida, publica-a para ser processada por outros módulos. Alguns módulos podem produzir apenas novas mensagens e nunca processar as mensagens recebidas. Uma cadeia de módulos cria um pipeline de processamento de dados e cada módulo realiza uma transformação dos dados a um dado momento nesse pipeline.

![Uma cadeia de módulos no gateway criada com o SDK de Gateway do Azure IoT][1]

O SDK contém o seguinte:

* Módulos pré-escritos que efetuam funções comuns do gateway.
* As interfaces que um programador pode utilizar para escrever módulos personalizados.
* A infraestrutura necessária para implementar e executar um conjunto de módulos.

O SDK fornece uma camada de abstração que lhe permite criar gateways para executar numa variedade de sistemas operativos e de plataformas.

![Camada de abstração do SDK do Gateway do Azure IoT][2]

### <a name="messages"></a>Mensagens
Apesar de a ideia dos módulos a transmitir mensagens entre si ser uma forma conveniente de conceptualizar o funcionamento do gateway, esta não reflete com precisão o que acontece. Os módulos utilizam mediadores para comunicarem entre si, publicam mensagens no mediador (bus, pubsub ou qualquer outro padrão de mensagens) e, depois, deixam que o mediador encaminhe as mensagens para os módulos ligados a ele.

Os módulos utilizam a função **Broker_Publish** para publicar mensagens no mediador. O mediador entrega as mensagens a um módulo ao invocar uma função de chamada de retorno. Uma mensagem é composta por um conjunto de propriedades chave/valor e conteúdo transmitido como um bloco de memória.

![A função de Mediador no SDK de Gateway do Azure IoT][3]

### <a name="message-routing-and-filtering"></a>Encaminhamento e filtragem de mensagens
Existem duas formas de direcionar as mensagens para os módulos corretos. Pode ser transmitido um conjunto de ligações para o mediador, para que este saiba qual é a origem e o sink de cada módulo, ou o módulo pode filtrar as propriedades das mensagens. Os módulos só devem tomar medidas relativamente a uma mensagem se esta se destinar aos mesmos. São as ligações e a filtragem de mensagens que criam, efetivamente, um pipeline de mensagens.

## <a name="hello-world-sample-architecture"></a>Arquitetura do exemplo Olá Mundo
O exemplo Olá Mundo ilustra os conceitos descritos na secção anterior. O exemplo Olá Mundo implementa um gateway que tem um pipeline composto por dois módulos:

* O módulo *olá mundo* cria uma mensagem a cada cinco segundos e passa-a para o módulo de registo.
* O módulo *registo* escreve num ficheiro as mensagens que recebe.

![Arquitetura do exemplo “Olá, mundo” criada com o SDK de Gateway do Azure IoT][4]

Tal como descrito na secção anterior, o módulo Olá Mundo não passa mensagens diretamente para o módulo de registo a cada cinco segundos. Em vez disso, publica uma mensagem no mediador a cada cinco segundos.

O módulo Logger recebe a mensagem a partir do mediador e toma medidas relativamente à mesma, escrevendo os conteúdos da mensagem num ficheiro.

O módulo Logger só consome mensagens do mediador, nunca publica mensagens novas no mesmo.

![Como o mediador encaminha as mensagens entre módulos no SDK de Gateway do Azure IoT][5]

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

<!--HONumber=Nov16_HO3-->


