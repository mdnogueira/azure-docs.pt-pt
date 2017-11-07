## <a name="configure-the-nodejs-simulated-device"></a>Configurar o dispositivo simulado Node.js
1. No dashboard de monitorização remota, clique em **+ adicionar um dispositivo** e, em seguida, adicione um *personalizadas do dispositivo*. Tome nota do IoT Hub do nome de anfitrião, a id de dispositivo e a chave do dispositivo. Terá de-los mais tarde no tutorial quando preparar a aplicação de cliente de dispositivo remote_monitoring.js.
2. Certifique-se de que a versão Node.js 0.12 ou posterior está instalado no computador de desenvolvimento. Executar `node --version` numa linha de comandos ou numa shell para verificar a versão. Para obter informações sobre como utilizar um Gestor de pacote para instalar o Node.js no Linux, consulte [instalar Node.js através do Gestor de pacote][node-linux].
3. Quando instalou o Node.js, clonar a versão mais recente do [azure-iot-sdk-nó] [ lnk-github-repo] repositório à sua máquina de desenvolvimento. Utilize sempre a **mestre** ramo para a versão mais recente das bibliotecas e exemplos.
4. Na sua cópia local do [azure-iot-sdk-nó] [ lnk-github-repo] repositório, copiar os seguintes dois ficheiros da pasta do nó/dispositivo/samples numa pasta vazia no computador de desenvolvimento:
   
   * Packages.JSON
   * remote_monitoring.js
5. Abra o ficheiro de remote_monitoring.js e procure a seguinte definição de variável:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Substitua **[cadeia de ligação do dispositivo IoT Hub]** com a cadeia de ligação do dispositivo. Utilize os valores para o nome de anfitrião do IoT Hub, o id de dispositivo e a chave de dispositivo que tomou nota no passo 1. Uma cadeia de ligação do dispositivo tem o seguinte formato:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Se o seu nome de anfitrião do IoT Hub é **contoso** e o id do dispositivo é **mydevice**, a cadeia de ligação se pareça com o seguinte fragmento:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Guarde o ficheiro. Execute os seguintes comandos numa linha de comandos na pasta que contém estes ficheiros para instalar os pacotes necessários e, em seguida, executar a aplicação de exemplo ou shell:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observar telemetria dinâmica em ação
O dashboard apresenta a telemetria de temperatura e humidade dos dispositivos simulados existentes:

![O dashboard predefinido][image1]

Se selecionar o dispositivo simulado de Node.js que foi executada na secção anterior, consulte temperatura, a humidade e a telemetria de temperatura externa:

![Adicionar a temperatura externa para o dashboard][image2]

A solução de monitorização remota automaticamente Deteta o tipo de telemetria de temperatura externa adicionais e adiciona-o para o gráfico no dashboard.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png