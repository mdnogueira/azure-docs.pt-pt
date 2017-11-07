## <a name="view-the-telemetry"></a>Ver a telemetria

Raspberry Pi agora está a enviar telemetria para a solução de monitorização remota. Pode ver a telemetria no dashboard da solução. Também pode enviar mensagens para a sua Raspberry Pi do dashboard da solução.

- Navegue para o dashboard de solução.
- Selecione o seu dispositivo no **dispositivo para a vista** pendente.
- Apresenta a telemetria de Raspberry Pi no dashboard.

![Apresentar a telemetria de Raspberry Pi][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>Iniciar a atualização de firmware

O processo de atualização de firmware transfere e instala uma versão atualizada da aplicação de cliente de dispositivo em Raspberry Pi. Para obter mais informações sobre o processo de atualização de firmware, consulte a descrição do padrão de atualização de firmware no [descrição geral da gestão de dispositivos do IoT hub][lnk-update-pattern].

Iniciar o processo de atualização de firmware ao invocar um método no dispositivo. Este método é assíncrono e devolve assim que iniciar o processo de atualização. O dispositivo utiliza propriedades comunicadas para notificar a solução sobre o progresso da atualização.

Invocar métodos no seu Raspberry Pi a partir do dashboard de solução. Quando o Raspberry Pi primeiro se liga a solução de monitorização remota, envia informações sobre os métodos que suporta. 

[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
