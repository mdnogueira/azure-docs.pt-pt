Uma das principais funcionalidades do Azure IoT Edge está a ser possível implementar módulos nos seus dispositivos de limite de IoT a partir da nuvem. Um módulo de limite de IoT é um pacote executável implementado como um contentor. Nesta secção, implemente um módulo que gera telemetria para o seu dispositivo simulado. 

1. No portal do Azure, navegue até ao seu IoT hub.
1. Aceda a **IoT Edge (pré-visualização)** e selecione o seu dispositivo de limite de IoT.
1. Selecione **definir módulos**.
1. Selecione **Adicionar módulo IoT Edge**.
1. No **nome** campo, introduza `tempSensor`. 
1. No **URI de imagem** campo, introduza `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
1. Deixar as outras definições inalteradas e selecione **guardar**.

   ![Guardar o módulo de limite de IoT depois de introduzir o nome e a imagem de URI](./media/iot-edge-deploy-module/name-image.png)

1. Volta a **adicionar módulos** passo, selecione **seguinte**.
1. No **especificar rotas** passo, selecione **seguinte**.
1. No **rever modelo** passo, selecione **submeter**.
1. Regressar à página de detalhes do dispositivo e selecionar **atualizar**. Deverá ver o novo módulo tempSensor em execução ao longo do tempo de execução do limite de IoT. 

   ![Vista tempSensor na lista de módulos implementados][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png