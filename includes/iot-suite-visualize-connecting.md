## <a name="view-device-telemetry"></a>Ver a telemetria do dispositivo

Pode ver a telemetria enviada do seu dispositivo **dispositivos** página na solução.

1. Selecione o dispositivo aprovisionadas na lista de dispositivos no **dispositivos** página. Um painel mostra informações sobre o seu dispositivo, incluindo um desenho de telemetria do dispositivo:

    ![Ver detalhes do dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Escolha **pressão** para alterar a apresentação de telemetria:

    ![Telemetria de pressão de vista](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para ver informações de diagnóstico sobre o seu dispositivo, desloque para baixo até **diagnóstico**:

    ![Diagnóstico de dispositivos de vista](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Atuar no seu dispositivo

Para invocar métodos nos seus dispositivos, utilize o **dispositivos** página na solução de monitorização remota. Por exemplo, numa solução de monitorização remota **Chiller** dispositivos implementam um **reiniciar** método.

1. Escolha **dispositivos** para navegar para o **dispositivos** página na solução.

1. Selecione o dispositivo aprovisionadas na lista de dispositivos no **dispositivos** página:

    ![Selecione o dispositivo físico](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para apresentar uma lista dos métodos pode chamar no seu dispositivo, escolha **agenda**. Para agendar um método para ser executado em vários dispositivos, pode selecionar vários dispositivos na lista. O **agenda** painel mostra os tipos de método comuns a todos os dispositivos selecionados.

1. Escolha **reiniciar**, defina o nome da tarefa **RebootPhysicalChiller**e escolha **aplicar**:

    ![Agendar o reinício](media/iot-suite-visualize-connecting/deviceschedule.png)

1. É apresentada uma mensagem na consola do seu código de dispositivo a executar quando o dispositivo processa o método.

> [!NOTE]
> Para controlar o estado da tarefa na solução, escolha **vista**.

## <a name="next-steps"></a>Passos seguintes

O artigo [personalizar a solução pré-configurada de monitorização remota](../articles/iot-suite/iot-suite-remote-monitoring-customize.md) descreve algumas formas de personalizar a solução pré-configurada.