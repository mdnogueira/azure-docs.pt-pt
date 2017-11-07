## <a name="view-the-solution-dashboard"></a>Ver o dashboard da solução

O dashboard de solução permite-lhe gerir a solução implementada. Por exemplo, pode ver a telemetria, adicionar dispositivos e invocar métodos.

1. Quando o aprovisionamento estiver concluído e o mosaico da sua solução pré-configurada indicar **Pronto**, escolha **Iniciar** para abrir o seu portal de solução de monitorização remota num novo separador.

    ![Iniciar a solução pré-configurada][img-launch-solution]

1. Por predefinição, o portal de solução mostra o *dashboard*. Pode navegar para outras áreas do portal de solução com o menu no lado esquerdo da página.

    ![Dashboard da solução pré-configurada de monitorização remota][img-menu]

## <a name="add-a-device"></a>Adicionar um dispositivo

Para que um dispositivo ligue à solução pré-configurada, este tem de se identificar no Hub IoT utilizando credenciais válidas. Pode obter as credenciais do dispositivo a partir do dashboard da solução. Vai incluir as credenciais do dispositivo na sua aplicação cliente mais à frente neste tutorial.

Se ainda não o fez, adicione um dispositivo personalizado à sua solução de monitorização remota. Conclua os passos seguintes no dashboard de solução:

1. No canto inferior esquerdo do dashboard, clique em **Adicionar um dispositivo**.

   ![Adicionar um dispositivo][1]

1. No painel **Dispositivo Personalizado**, clique em **Adicionar novo**.

   ![Adicionar um dispositivo personalizado][2]

1. Escolha **Definir o meu próprio ID do Dispositivo**. Introduza um ID de dispositivo como **rasppi**, clique em **verifique ID** para verificar que já não tiver utilizado o nome na sua solução e, em seguida, clique em **criar** para aprovisionar o dispositivo.

   ![Adicionar ID do dispositivo][3]

1. Anote as credenciais de dispositivo (**ID de dispositivo**, **o nome de anfitrião do IoT Hub**, e **chave do dispositivo**). A aplicação de cliente no Raspberry Pi tem estes valores para ligar a solução de monitorização remota. Em seguida, clique em **Guardar**.

    ![Ver as credenciais do dispositivo][4]

1. Selecione o seu dispositivo na lista de dispositivos no dashboard da solução. Em seguida, no painel **Detalhes do Dispositivo**, clique em **Ativar Dispositivo**. O estado do seu dispositivo é agora **Em execução**. A solução de monitorização remota pode agora receber telemetria do seu dispositivo e invocar métodos no dispositivo.

[img-launch-solution]: media/iot-suite-v1-raspberry-pi-kit-view-solution/launch.png
[img-menu]: media/iot-suite-v1-raspberry-pi-kit-view-solution/menu.png
[1]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite0.png
[2]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite1.png
[3]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite2.png
[4]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite3.png