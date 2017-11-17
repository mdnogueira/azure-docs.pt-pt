Crie uma identidade de dispositivo para o seu dispositivo simulado para que este consegue comunicar com o seu IoT hub. Uma vez que os dispositivos de limite de IoT comportar-se e podem ser geridos de forma diferente de dispositivos de IoT típicos, declarar esta opção para ser um dispositivo de limite de IoT a partir do início. 

1. No portal do Azure, navegue até ao seu IoT hub.
1. Selecione **IoT Edge (pré-visualização)** , em seguida, selecione **adicionar dispositivo de limite de IoT**.

   ![Adicionar dispositivo de limite de IoT](./media/iot-edge-register-device/add-device.png)

1. Dê o seu dispositivo simulado um ID de dispositivo exclusivo.
1. Selecione **guardar** para adicionar o seu dispositivo.
1. Selecione o novo dispositivo na lista de dispositivos.
1. Copie o valor para **cadeia de ligação — chave primária** e guardá-lo. Irá utilizar este valor para configurar o tempo de execução do limite de IoT na secção seguinte. 

