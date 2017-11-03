## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Agora que criou um hub IoT, localize as informações importantes que utilizar para ligar dispositivos e aplicações para o seu IoT hub. 

1. Depois de criar o hub IoT, clique no mesmo no dashboard. Anote o **Nome de anfitrião** e, em seguida, clique em **Políticas de acesso partilhado**.

   ![Obter o nome do anfitrião do hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. No painel **Políticas de acesso partilhado**, clique na política **iothubowner** e copie e anote a **Cadeia de ligação** do hub IoT. Para obter mais informações, veja [Controlar o acesso ao Hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

> [!NOTE] 
Não precisará da cadeia de ligação iothubowner para este tutorial de configuração. No entanto, poderá ser necessária para alguns dos tutoriais sobre os diferentes cenários de IoT depois de concluir esta configuração.

   ![Obter a cadeia de ligação do hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registar um dispositivo no hub IoT para o seu dispositivo

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. Clique em **Device Explorer**.
3. No painel do Device Explorer, clique em **Adicionar** para adicionar um dispositivo ao hub IoT. Em seguida, faça o seguinte:

   **ID do Dispositivo**: introduza o ID do novo dispositivo. Os IDs dos dispositivos são sensíveis às maiúsculas e minúsculas.

   **Tipo de Autenticação**: selecione **Chave Simétrica**.

   **Gerar Chaves Automaticamente**: selecione esta caixa de verificação.

   **Ligar dispositivo ao Hub IoT**: clique em **Ativar**.

   ![Adicionar um dispositivo no Device Explorer do hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Clique em **Guardar**.
5. Depois da criação do dispositivo, abra-o no painel **Device Explorer**.
6. Anote a chave primária da cadeia de ligação.

   ![Obter a cadeia de ligação do dispositivo](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
