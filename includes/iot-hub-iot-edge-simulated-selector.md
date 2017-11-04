> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

Estas instruções do [nuvem dispositivo simulado carregar exemplo] mostra-lhe como utilizar [Azure IoT Edge] [ lnk-sdk] para enviar telemetria do dispositivo para a nuvem ao IoT Hub a partir de dispositivos simulados .

Estas instruções abrangem:

* **Arquitetura**: as informações da arquitetura sobre a [nuvem dispositivo simulado carregar exemplo].
* **Criação e execução**: os passos necessários para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura

O [nuvem dispositivo simulado carregar exemplo] mostra como criar um gateway que envia telemetria dos dispositivos simulados para um hub IoT. Um dispositivo pode não conseguir ligar diretamente ao IoT Hub, porque o dispositivo:

* Não utilize um protocolo de comunicações abrangido pelo IoT Hub.
* Não é suficientemente inteligente lembrar-se a identidade atribuída pelo IoT Hub.

Um gateway de IoT pode resolver estes problemas das seguintes formas:

* O gateway compreende o protocolo utilizado pelo dispositivo, recebe a telemetria do dispositivo para a nuvem do dispositivo e reencaminha as mensagens ao IoT Hub, utilizando um protocolo abrangido pelo IoT hub.

* O gateway mapeia as identidades do IoT Hub para dispositivos e age como um proxy quando um dispositivo envia mensagens ao IoT Hub.

O diagrama seguinte mostra os componentes principais de exemplo, incluindo os módulos de limite de IoT:

![Diagrama - mensagem dispositivo simulado passa através do gateway do IoT Hub][1]

Este exemplo contém três módulos que compõem o gateway:
1. Módulo de ingestão de protocolo
1. MAC &lt;-&gt; Módulo de ID do Hub IoT
1. Módulo de comunicação do Hub IoT

Os módulos não passam mensagens diretamente entre si. Os módulos publicar mensagens para um mediador interno que fornece as mensagens para os outros módulos utilizando um mecanismo de subscrição. Para obter mais informações, consulte [introdução ao Azure IoT Edge][lnk-gw-getstarted].

![Módulos de gateway comunicam com mediador - diagrama][2]

### <a name="protocol-ingestion-module"></a>Módulo de ingestão de protocolo

O módulo de ingestão de protocolo é o ponto de partida para o processo de colocar os dados dos dispositivos, através do gateway e para a nuvem. 

No exemplo, este módulo:

1. Cria dados de temperatura simulada. Se utilizar dispositivos físicos, o módulo lê dados a partir desses dispositivos físicos.
1. Cria uma mensagem.
1. Para o conteúdo da mensagem, coloca os dados de temperatura simulada.
1. Adiciona uma propriedade com um endereço MAC falsificado à mensagem.
1. Disponibiliza a mensagem para o módulo seguinte da cadeia.

O módulo de ingestão de protocolo é **simulated_device.c** no código fonte.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; Módulo de ID do Hub IoT

MAC &lt; - &gt; módulo do IoT Hub ID funciona como um conversor. Esta amostra utiliza um endereço MAC como identificador de dispositivo exclusivo e correlaciona-o com uma identidade de dispositivo Hub IoT. No entanto, pode escrever o seu próprio módulo que utiliza um identificador exclusivo diferente. Por exemplo, os seus dispositivos podem ter exclusivos de números de série ou os dados de telemetria podem incluir um nome único para o dispositivo incorporado.

No exemplo, este módulo:

1. Verifica a existência de mensagens em fila que tem uma propriedade de endereço MAC.
1. Se houver um endereço MAC, adiciona outra propriedade com uma chave do dispositivo IoT Hub na mensagem. 
1. Disponibiliza a mensagem para o módulo seguinte da cadeia.

O programador configura um mapeamento entre os endereços MAC e identidades de IoT Hub para associar os dispositivos simulados identidades de dispositivo do IoT Hub. O programador adiciona o mapeamento manualmente como parte da configuração do módulo.

MAC &lt; - &gt; módulo de ID de Hub IoT é **identitymap.c** no código fonte. 

### <a name="iot-hub-communication-module"></a>Módulo de comunicação do Hub IoT

O módulo de comunicação do IoT Hub é aberta uma ligação HTTPS única do gateway para o IoT Hub. HTTPS é um dos três protocolos abrangidos pelo IoT Hub. Este módulo evita que tenha que que abrir uma ligação para cada dispositivo multiplexação ligações a partir de todos os dispositivos através de uma ligação. Esta abordagem permite que um único gateway ligar a vários dispositivos. 

No exemplo, este módulo:

1. Aceita mensagens com um IoT Hub propriedade de chave do dispositivo que foi atribuída pelo módulo de anterior. 
1. Envia o conteúdo da mensagem ao IoT Hub, utilizando o protocolo HTTPS. 

O módulo de comunicação do IoT Hub é **iothub.c** no código fonte.

## <a name="before-you-get-started"></a>Antes de começar

Antes de começar, tem de:

* [Criar um hub IoT] [ lnk-create-hub] na sua subscrição do Azure. Tem o nome do seu hub para estas instruções de exemplo. Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Adicionar dois dispositivos ao seu IoT hub e anote os respetivos IDs e as chaves de dispositivo. Pode utilizar o [Explorador de dispositivo] [ lnk-device-explorer] ou [iothub explorer] [ lnk-iothub-explorer] ferramentas para adicionar dispositivos ao IoT hub e obter as respetivas chaves.


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[nuvem dispositivo simulado carregar exemplo]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md