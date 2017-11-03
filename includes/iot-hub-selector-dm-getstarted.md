> [!div class="op_single_selector"]
> * [Dispositivo: Node.js Serviço: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Dispositivo: Node.js Serviço: C#](../articles/iot-hub/iot-hub-csharp-node-device-management-get-started.md)
> * [Dispositivos: Serviço c#: c#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Dispositivo: Serviço de Java: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)

Aplicações de back-end pode utilizar primitivos do IoT Hub do Azure, tal como [dispositivo duplo] [ lnk-devtwin] e [direcionar métodos][lnk-c2dmethod], para iniciar e monitorizar dispositivos remotamente ações de gestão nos dispositivos. Este tutorial mostra como uma aplicação de back-end e uma aplicação de dispositivo podem trabalhar em conjunto para iniciar e monitorizar um reinício do dispositivo remoto utilizar o IoT Hub.

Utilize um método direto para iniciar as ações de gestão de dispositivos (por exemplo, o reinício, reposição de fábrica e atualização de firmware) a partir de uma aplicação de back-end na nuvem. O dispositivo é responsável por:

* A processar o pedido de método enviado a partir do IoT Hub.
* A iniciar a ação de específicos do dispositivo correspondente no dispositivo.
* Fornecer atualizações de estado através de *comunicadas propriedades* ao IoT Hub.

Pode utilizar uma aplicação de back-end na nuvem para executar consultas de duplo de dispositivo para elaborar relatórios sobre o progresso das suas ações de gestão de dispositivos.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
