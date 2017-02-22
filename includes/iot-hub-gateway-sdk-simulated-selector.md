> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)
> 
> 

Estas instruções da [amostra do Carregamento para a Cloud de Dispositivo Simulado] mostra como utilizar o [SDK do Gateway do Azure IoT][lnk-sdk] para enviar telemetria dispositivo para cloud para o Hub IoT a partir de dispositivos simulados.

Estas instruções abrangem:

1. **Arquitetura**: informações importantes da arquitetura relativamente à amostra do Carregamento para a Cloud de Dispositivo Simulado.
2. **Criação e execução**: os passos necessários para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura
A amostra do Carregamento para a Cloud de Dispositivo Simulado mostra como utilizar o SDK para criar um gateway que envia telemetria a partir de dispositivos simulados para um Hub IoT. Os dispositivos simulados não podem ligar diretamente ao Hub IoT porque:

* Os dispositivos não utilizam um protocolo de comunicação que o Hub IoT compreenda.
* Os dispositivos não são inteligentes o suficiente para se lembrarem da identidade que lhes foi atribuída pelo Hub IoT.

O gateway resolve estes problemas que afetam os dispositivos simulados das seguintes formas:

* O gateway compreende o protocolo utilizado pelos dispositivos simulados, recebe telemetria dispositivo para cloud dos dispositivos e reencaminha essas mensagens para o Hub IoT através de um protocolo que o Hub IoT compreende.
* O gateway armazena identidades do Hub IoT em nome dos dispositivos simulados e atua como proxy quando os dispositivos simulados enviam mensagens para o Hub IoT.

O seguinte diagrama apresenta os principais componentes da amostra, incluindo os módulos do gateway:

![][1]

> [!NOTE]
> Os módulos não passam mensagens diretamente entre si. Os módulos publicam mensagens num mediador interno que envia as mensagens para os outros módulos através de um mecanismo de subscrição, conforme apresentado no diagrama abaixo. Para obter mais informações, veja o artigo [Introdução ao SDK do Gateway do IoT][lnk-gw-getstarted].
> 
> 

### <a name="protocol-ingestion-module"></a>Módulo de ingestão de protocolo
Este módulo é o ponto de partida para obter dados a partir de dispositivos, através do gateway, para a cloud. Na amostra, o módulo executa quatro tarefas:

1. Cria dados de temperatura simulada. Tenha em atenção que se utilizar dispositivos reais, o módulo lê dados a partir desses dispositivos físicos.
2. Coloca os dados de temperatura simulada nos conteúdos de uma mensagem.
3. Adiciona uma propriedade com um endereço MAC falso à mensagem que contém os dados de temperatura simulada.
4. Disponibiliza a mensagem ao módulo seguinte na cadeia.

> [!NOTE]
> O módulo com o nome **Ingestão do Protocolo X** no diagrama acima denomina-se **Dispositivo simulado ** no código de fonte.
> 
> 

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; Módulo de ID do Hub IoT
Este módulo analisa as mensagens que incluem uma propriedade que contém o endereço MAC, adicionado pelo módulo de ingestão de protocolo, da aplicação de dispositivo simulado. Se o módulo encontrar uma propriedade desse tipo, adiciona outra propriedade com uma chave de dispositivo Hub IoT à mensagem e, em seguida, disponibiliza a mensagem ao módulo seguinte na cadeia. É assim que a amostra associa identidades de dispositivos Hub IoT a dispositivos simulados. O programador configura o mapeamento entre os endereços MAC e as identidades do Hub IoT manualmente como parte da configuração do módulo. 

> [!NOTE]
> Esta amostra utiliza um endereço MAC como identificador de dispositivo exclusivo e correlaciona-o com uma identidade de dispositivo Hub IoT. No entanto, pode escrever o seu próprio módulo que utiliza um identificador exclusivo diferente. Por exemplo, pode ter dispositivos com números de série exclusivos ou dados telemétricos que tenha um nome do dispositivo exclusivo incorporado que possa utilizar para determinar a identidade de dispositivo Hub IoT.
> 
> 

### <a name="iot-hub-communication-module"></a>Módulo de comunicação do Hub IoT
Este módulo envia o conteúdo das mensagens que têm uma identidade de dispositivo Hub IoT atribuída pelo módulo anterior para o Hub IoT através de HTTP. O HTTP é um dos três protocolos que o Hub IoT compreende.

Em vez de abrir uma ligação para o Hub IoT para cada aplicação de dispositivo simulado, este módulo abre uma ligação HTTP exclusiva a partir do gateway para o Hub IoT e combina ligações por multiplexação de todos os dispositivos simulados através dessa ligação. Isto permite que um único gateway ligue muitos mais dispositivos, simulados ou outros, do que seria possível se abrisse uma ligação exclusiva para cada dispositivo.

![][2]

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[amostra do Carregamento para a Cloud de Dispositivo Simulado]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md

<!--HONumber=Feb17_HO1-->


