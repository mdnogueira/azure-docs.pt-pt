## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associar uma conta de armazenamento do Azure ao IoT Hub

Porque a aplicação de dispositivo simulado carrega um ficheiro para um blob, tem de ter um [Storage do Azure](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) conta associada ao IoT Hub. Quando associa uma conta de armazenamento do Azure com um IoT hub, o IoT hub gera um URI de SAS. Um dispositivo pode utilizar este URI de SAS em segurança carregar um ficheiro para um contentor do blob. O serviço de IoT Hub e os SDKs do dispositivo coordenam o processo que gera o URI de SAS e torna a mesma disponível para um dispositivo para utilizar para carregar um ficheiro.

Siga as instruções em [carregamentos de ficheiros de configuração no portal do Azure](../articles/iot-hub/iot-hub-configure-file-upload.md) para associar uma conta de armazenamento do Azure ao seu IoT hub. Certifique-se de que um contentor de blob está associado ao seu IoT hub e que as notificações de ficheiros estão ativadas.

![Ativar notificações de ficheiros no portal](media/iot-hub-associate-storage/enable-file-notifications.png)