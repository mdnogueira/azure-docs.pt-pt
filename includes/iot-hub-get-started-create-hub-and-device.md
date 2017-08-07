## <a name="create-an-iot-hub"></a>Criar um hub IoT

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo** > **Internet das Coisas** > **Hub IoT**.

   ![Criar um hub IoT no portal do Azure](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
2. No painel **Hub IoT**, introduza as seguintes informações para o hub IoT:

     **Nome**: introduza o nome do hub IoT. Se o nome introduzido for válido, aparece uma marca de verificação verde.

     **Escalão de preços e dimensionamento**: selecione o escalão **F1 - Gratuito**. Esta opção é suficiente para esta demonstração. Para obter mais informações, veja o [escalão de preços e dimensionamento](https://azure.microsoft.com/pricing/details/iot-hub/).

     **Grupo de recursos**: crie um grupo de recursos para utilizar o hub IoT ou utilize um já existente. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../articles/azure-resource-manager/resource-group-portal.md).

     **Localização**: selecione a localização mais próxima da origem de criação do hub IoT.

     **Afixar ao dashboard**: selecione esta opção para facilitar o acesso ao hub IoT a partir do dashboard.

   ![Introduzir as informações para criar o hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

3. Clique em **Criar**. A criação do hub IoT pode demorar alguns minutos. Pode ver o progresso no painel **Notificações**.

   ![Ver as notificações de progresso do hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

4. Depois de criar o hub IoT, clique no mesmo no dashboard. Anote o **Nome de anfitrião** e, em seguida, clique em **Políticas de acesso partilhado**.

   ![Obter o nome do anfitrião do hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

5. No painel **Políticas de acesso partilhado**, clique na política **iothubowner** e copie e anote a **Cadeia de ligação** do hub IoT. Para obter mais informações, veja [Controlar o acesso ao Hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

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
