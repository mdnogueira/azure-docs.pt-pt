> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#/node.js](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-direct-methods.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-direct-methods.md)

IoT Hub do Azure é um serviço completamente gerido que permite fiável e seguras comunicações bidirecionais entre milhões de dispositivos e uma solução de back-end. Tutoriais anteriores ([introdução ao IoT Hub] e [enviar mensagens da nuvem para o dispositivo com o IoT Hub]) ilustrar a dispositivo para nuvem e da nuvem para o dispositivo mensagens funcionalidades básicas do IoT Hub. IoT Hub também dá-lhe a capacidade de invocar métodos não durável nos dispositivos da nuvem. Métodos diretos representam uma interação de pedido-resposta com um dispositivo semelhante a uma chamada HTTPS, em que são ou não bem-sucedidos imediatamente (após um tempo limite especificado de um utilizador) permitir que o utilizador saber o estado da chamada. [Invocar um método direto num dispositivo] [ lnk-devguide-methods] descreve diretos métodos em mais detalhe e oferece orientação para quando utilizar métodos diretos em vez de mensagens da nuvem para o dispositivo ou propriedades pretendidas.

Este tutorial mostrar-lhe como:

* Utilize o portal do Azure para criar um hub IoT e criar uma identidade de dispositivo no seu IoT hub.
* Crie uma aplicação de dispositivo simulado que tem um método direto, que pode ser chamado pela nuvem.
* Crie uma aplicação de consola que chama um método direto na aplicação do dispositivo simulado através do seu IoT hub.


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[enviar mensagens da nuvem para o dispositivo com o IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[introdução ao IoT Hub]: ../articles/iot-hub/iot-hub-node-node-getstarted.md