## <a name="view-the-telemetry"></a>Ver a telemetria

Raspberry Pi agora está a enviar telemetria para a solução de monitorização remota. Pode ver a telemetria no dashboard da solução. Também pode enviar mensagens para a sua Raspberry Pi do dashboard da solução.

- Navegue para o dashboard de solução.
- Selecione o seu dispositivo no **dispositivo para a vista** pendente.
- Apresenta a telemetria de Raspberry Pi no dashboard.

![Apresentar a telemetria de Raspberry Pi][img-telemetry-display]

## <a name="act-on-the-device"></a>Agir sobre o dispositivo

A partir do dashboard de solução, pode invocar métodos no seu Raspberry Pi. Quando o Raspberry Pi liga-se a solução de monitorização remota, envia informações sobre os métodos que suporta.

- No dashboard de solução, clique em **dispositivos** para visitar o **dispositivos** página. Selecione o seu Raspberry Pi no **lista de dispositivos**. Em seguida, escolha **métodos**:

    ![Lista de dispositivos no dashboard][img-list-devices]

- No **invocar método** página, escolha **LightBlink** no **método** pendente.

- Escolha **InvokeMethod**. O LED ligado para os flashes Raspberry Pi várias vezes. A aplicação no Raspberry Pi envie uma confirmação novamente para o dashboard de solução:

    ![Mostrar histórico de método][img-method-history]

- Pode alternar o LED e desativar a utilizar o **ChangeLightStatus** método com um **LightStatusValue** definido como **1** no ou **0** para desativado.

> [!WARNING]
> Se deixar a solução de monitorização remota em execução na sua conta do Azure, é-lhe faturado durante o período de tempo que é executada. Para obter mais informações sobre como reduzir o consumo de enquanto executa a solução de monitorização remota, consulte [soluções para fins de demonstração de pré-configuradas de configurar o Azure IoT Suite][lnk-demo-config]. Elimine a solução pré-configurada da sua conta do Azure quando tiver concluído a utilizá-la.


[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md