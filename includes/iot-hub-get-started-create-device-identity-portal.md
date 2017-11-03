## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Esta secção, irá utilizar o [portal do Azure] [ lnk-azure-portal] para criar uma identidade de dispositivo no registo de identidade no seu IoT hub. Não é possível ligar um dispositivo ao hub IoT, exceto se tiver uma entrada no registo de identidade. Para obter mais informações, veja a secção "Identity registry" (Registo de identidades) do [Hub IoT developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity]. O **Explorador de dispositivo** na ajuda do portal gerar um ID de dispositivo exclusivo e uma chave que o seu dispositivo pode utilizar para se identificar quando se liga ao IoT Hub. Os IDs dos dispositivos são sensíveis às maiúsculas e minúsculas.

1. Certifique-se de tem sessão iniciada para o [portal do Azure][lnk-azure-portal].

1. No Jumpbar, clique em **todos os recursos** e localizar o recurso do hub IoT.

    ![Navegue até ao seu Iot hub][img-find-iothub]

1. Quando o recurso do hub IoT é aberto, clique em de **Explorador de dispositivo** ferramenta e, em seguida, clique em **adicionar** na parte superior. Forneça o nome para o novo dispositivo, como **myDeviceId**e clique em **guardar**.

    ![Criar a identidade de dispositivo no portal][img-create-device]

   Esta ação cria uma nova identidade de dispositivo do seu hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. No **Explorador de dispositivo**da lista de dispositivos, clique no dispositivo recém-criado e anote o **cadeia de ligação---chave primária**. 

    ![Cadeia de ligação do dispositivo][img-connection-string]

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos para permitir um acesso seguro ao Hub IoT. Armazena os IDs do dispositivo e as chaves a utilizar como credenciais de segurança e um sinalizador ativado/desativado que pode utilizar para desativar o acesso de um dispositivo individual. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação.  Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

