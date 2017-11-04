> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

Este artigo apresenta instruções detalhadas sobre o [código de exemplo Hellow World][lnk-helloworld-sample] para ilustrar os componentes fundamentais da arquitetura do [Azure IoT Edge][lnk-iot-edge]. Este exemplo utiliza o Azure IoT Edge para criar um gateway simples que regista uma mensagem "hello world" num ficheiro a cada cinco segundos.

Estas instruções abrangem:

* **Olá, mundo exemplo de arquitetura**: descreve como [conceitos de arquitetura do Azure IoT Edge] [ lnk-edge-concepts] aplicam-se ao exemplo Olá mundo e como os componentes se encaixam.
* **Como criar o exemplo**: os passos necessários para criar o exemplo.
* **Como executar o exemplo**: os passos necessários para executar o exemplo. 
* **Resultado típico**: um exemplo do resultado a esperar quando executa o exemplo.
* **Fragmentos de código**: uma coleção de fragmentos de código para mostrar a forma como o exemplo Olá mundo implementa componentes chave de gateway de IoT Edge.


## <a name="hello-world-sample-architecture"></a>Arquitetura do exemplo Olá Mundo
O exemplo Olá Mundo ilustra os conceitos descritos na secção anterior. O exemplo Olá mundo implementa um gateway de IoT limite que tem um pipeline composto por dois módulos de limite de IoT:

* O módulo *olá mundo* cria uma mensagem a cada cinco segundos e passa-a para o módulo de registo.
* O módulo *registo* escreve num ficheiro as mensagens que recebe.

![Arquitetura do exemplo “Hello World” criada com o Azure IoT Edge][4]

Tal como descrito na secção anterior, o módulo Olá Mundo não passa mensagens diretamente para o módulo de registo a cada cinco segundos. Em vez disso, publica uma mensagem no mediador a cada cinco segundos.

O módulo Logger recebe a mensagem a partir do mediador e toma medidas relativamente à mesma, escrevendo os conteúdos da mensagem num ficheiro.

O módulo Logger só consome mensagens do mediador, nunca publica mensagens novas no mesmo.

![Como o mediador encaminha as mensagens entre módulos no Azure IoT Edge][5]

A ilustração anterior mostra a arquitetura do exemplo Olá mundo e os caminhos relativos para ficheiros de origem que implementam partes diferentes da amostra no [repositório][lnk-iot-edge]. Explore o código ou utilize os fragmentos de código neste artigo como guia.

<!-- Images -->
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md