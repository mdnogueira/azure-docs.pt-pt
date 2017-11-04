> [!div class="op_single_selector"]
> * [C em Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C em Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (genérico)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Node.js no Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [C no Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-c.md)

Neste tutorial, implementa um **Chiller** dispositivo que envia a telemetria seguinte para a monitorização remota [solução pré-configurada](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md):

* Temperatura
* pressão
* Humidade

De simplicidade, o código gera os valores de telemetria de exemplo para o **Chiller**. Podia expandir o exemplo ao ligar sensores reais para o seu dispositivo e envia a telemetria real.

O dispositivo de exemplo também:

* Envia os metadados para a solução para descrever as respetivas capacidades.
* Responde a ações acionadas a partir de **dispositivos** página na solução.
* Responder a alterações de configuração enviar a partir de **dispositivos** página na solução.

Para concluir este tutorial, precisa de uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Antes de começar

Antes de escrever qualquer código para o seu dispositivo, terá de aprovisionar a sua solução pré-configurada de monitorização remota e aprovisionar um novo dispositivo personalizado nessa solução.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Aprovisionar a solução pré-configurada de monitorização remota

O **Chiller** dispositivo que criou neste tutorial envia dados para uma instância do [monitorização remota](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) solução pré-configurada. Se já que ainda não aprovisionou a solução pré-configurada de monitorização remota na sua conta do Azure, consulte o artigo [implementar a solução pré-configurada de monitorização remota](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

Quando o processo de aprovisionamento para a solução de monitorização remota terminar, clique em **Iniciar** para abrir o dashboard da solução no seu browser.

![O dashboard de solução](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Aprovisionar o dispositivo na solução de monitorização remota

> [!NOTE]
> Se já tiver aprovisionado um dispositivo na sua solução, pode ignorar este passo. Terá das credenciais de dispositivo ao criar a aplicação de cliente.

Para que um dispositivo ligue à solução pré-configurada, este tem de se identificar no Hub IoT utilizando credenciais válidas. Pode obter as credenciais de dispositivo da solução **dispositivos** página. Vai incluir as credenciais do dispositivo na sua aplicação cliente mais à frente neste tutorial.

Para adicionar um dispositivo à sua solução de monitorização remota, conclua os seguintes passos no **dispositivos** página na solução:

1. Escolha **aprovisionar**e, em seguida, escolha **físico** como o **tipo de dispositivo**:

    ![Aprovisionar um dispositivo físico](media/iot-suite-selector-connecting/devicesprovision.png)

1. Introduza **físico chiller** como o ID de dispositivo. Escolha o **chave simétrica** e **automática gerar chaves** opções:

    ![Escolha as opções de dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

Para localizar as credenciais do que seu dispositivo tem de utilizar para ligar a solução pré-configurada, navegue até ao portal do Azure no seu browser. Inicie sessão sua subscrição.

1. Localize o grupo de recursos que contém os serviços do Azure que utiliza a sua solução de monitorização remota. O grupo de recursos tem o mesmo nome que a solução de monitorização remota que aprovisionou.

1. Navegue para o IoT hub neste grupo de recursos. Em seguida, escolha **Explorador de dispositivo**:

    ![Explorador de dispositivo](media/iot-suite-selector-connecting/deviceexplorer.png)

1. Escolha o **ID de dispositivo** que criou no **dispositivos** página na solução de monitorização remota.

1. Anote o **ID de dispositivo** e **chave primária** valores. Utilize estes valores quando adicionar código para ligar o seu dispositivo à solução.

Tem de ter agora aprovisionado um dispositivo físico no monitorização remota solução pré-configurada. Nas secções seguintes, implementar a aplicação cliente que utiliza as credenciais de dispositivo para ligar à sua solução.

A aplicação cliente implementa incorporada **Chiller** modelo do dispositivo. Um modelo de dispositivo da solução pré-configurada Especifica o seguinte sobre um dispositivo:

* As propriedades de que relatórios de dispositivo para a solução. Por exemplo, um **Chiller** dispositivo comunica informações sobre o respetivo firmware e localização.
* Os tipos de telemetria que o dispositivo envia para a solução. Por exemplo, um **Chiller** dispositivo envia temperatura, humidade e valores de pressão.
* Os métodos que pode agendar da solução para execução no dispositivo. Por exemplo, um **Chiller** dispositivo tem de implementar **reiniciar**, **FirmwareUpdate**, **EmergencyValveRelease**, e  **IncreasePressuree** métodos.