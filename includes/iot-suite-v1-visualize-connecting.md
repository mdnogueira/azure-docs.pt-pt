## <a name="view-device-telemetry-in-the-dashboard"></a>Ver telemetria do dispositivo no dashboard
O dashboard na solução de monitorização remota permite-lhe ver a telemetria que os seus dispositivos enviam para o Hub IoT.

1. No browser, regresse ao dashboard da solução de monitorização remota e clique em **Dispositivos** no painel à esquerda para navegar para a **Lista de dispositivos**.
2. Na **Lista de dispositivos**, deverá ver que o estado do seu dispositivo é **Em execução**. Se não for, clique em **Ativar Dispositivo** no painel **Detalhes do Dispositivo**.
   
    ![Ver o estado do dispositivo][18]
3. Clique em **Dashboard** para voltar ao dashboard e selecione o seu dispositivo na lista pendente **Dispositivo a Visualizar** para ver a respetiva telemetria. A telemetria da aplicação de amostra é 50 unidades para temperatura interna, 55 unidades para temperatura externa e 50 unidades para humidade.
   
    ![Ver a telemetria do dispositivo][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>Invocar um método no seu dispositivo
O dashboard na solução de monitorização remota permite-lhe invocar os métodos nos seus dispositivos através do Hub IoT. Por exemplo, na solução de monitorização remota pode invocar um método para simular o reinício de um dispositivo.

1. No dashboard da solução de monitorização remota, clique em **Dispositivos** no painel à esquerda para navegar para a **Lista de dispositivos**.
2. Clique em **ID do Dispositivo** para o seu dispositivo na **Lista de dispositivos**.
3. No painel **Detalhes do dispositivo**, clique em **Métodos**.
   
    ![Métodos do dispositivo][13]
4. Na lista pendente de **Método**, selecione **InitiateFirmwareUpdate** e, em seguida, em **FWPACKAGEURI**, introduza um URL fictício. Clique em **Invocar Método** para chamar o método no dispositivo.
   
    ![Invocar um método de dispositivo][14]
   

5. Verá uma mensagem na consola que está a executar o código do dispositivo quando o dispositivo processar o método. Os resultados do método são adicionados ao histórico no portal da solução:

    ![Ver o histórico do método][img-method-history]

## <a name="next-steps"></a>Passos seguintes
O artigo [Personalizar soluções pré-configuradas][lnk-customize] descreve algumas formas através das quais pode expandir este exemplo. As extensões possíveis incluem a utilização de sensores reais e a implementação de comandos adicionais.

Pode saber mais sobre as [permissões no site azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-v1-visualize-connecting/suite4.png
[14]: ./media/iot-suite-v1-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-v1-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-v1-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-v1-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
