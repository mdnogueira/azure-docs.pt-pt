## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta secção, utilize uma ferramenta de Node.js chamada [iothub explorer] [ iot-hub-explorer] para criar uma identidade de dispositivo para este tutorial. Os IDs dos dispositivos são sensíveis às maiúsculas e minúsculas.

1. Execute o seguinte no seu ambiente de linha de comandos:

    `npm install -g iothub-explorer@latest`

1. Em seguida, execute o seguinte comando para iniciar sessão para o seu hub. Substitute `{iot hub connection string}` com a cadeia de ligação do IoT Hub que copiou anteriormente:

    `iothub-explorer login "{iot hub connection string}"`

1. Por fim, crie uma nova identidade de dispositivo chamada `myDeviceId` com o comando:

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Tome nota da cadeia de ligação do dispositivo do resultado. Esta cadeia de ligação do dispositivo é utilizada pela aplicação de dispositivo para ligar ao seu IoT Hub como um dispositivo.

![][img-identity]

Consulte [introdução ao IoT Hub] [ lnk-getstarted] para programaticamente criar as identidades de dispositivo.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
