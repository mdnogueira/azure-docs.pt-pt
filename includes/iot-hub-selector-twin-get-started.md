> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#/node.js](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). IoT Hub mantém um dispositivo duplo para cada dispositivo que se liga ao mesmo.

Utilize dispositivos duplos para:

* Armazenar metadados do dispositivo da sua solução de back-end.
* Comunicarão as informações de estado atual, tais como condições (por exemplo, o método de conectividade utilizado) e capacidades disponíveis da sua aplicação de dispositivo.
* Sincronize o estado de execução longa os fluxos de trabalho (tais como atualizações de firmware e de configuração) entre uma aplicação de dispositivo e uma aplicação de back-end.
* Consulta os metadados do dispositivo, a configuração ou o estado.

> [!NOTE]
> Dispositivos duplos foram concebidos para a sincronização de e para consultar as configurações de dispositivo e condições. Podem encontrar mais informations no quando utilizar dispositivos duplos [compreender dispositivos duplos][lnk-twins].

Dispositivos duplos são armazenados num IoT hub e contenham:

* *etiquetas*, os metadados do dispositivo acessível apenas ao solução de back-end;
* *pretender propriedades*, objetos JSON modificável pela solução de back-end e observable pela aplicação do dispositivo; e
* *comunicado propriedades*, objetos JSON modificável pela aplicação de dispositivo e ser lido pelo solução de back-end. As etiquetas e propriedades não podem conter matrizes, mas podem ser aninhados em objetos.

![][img-twin]

Além disso, o solução de back-end pode consultar dispositivos duplos com base em todos os dados acima.
Consulte [compreender dispositivos duplos] [ lnk-twins] para obter mais informações sobre dispositivos duplos e o [idioma de consulta do IoT Hub] [ lnk-query] referenciar para a consultar.


Este tutorial mostrar-lhe como:

* Criar uma aplicação de back-end que adiciona *etiquetas* para um dispositivo duplo e uma aplicação de dispositivo simulado que comunica o canal de conectividade como um *comunicadas propriedade* no dispositivo duplo.
* Consultar os dispositivos da sua aplicação de back-end utilizando filtros na etiquetas e propriedades que criou anteriormente.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md