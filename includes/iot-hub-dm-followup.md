## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar e expandir o dispositivo ações de gestão

As soluções de IoT podem expandir o conjunto definido de padrões de gestão de dispositivos ou ativar padrões personalizadas utilizando o dispositivo duplo e primitivos do método de nuvem para o dispositivo. Outros exemplos de ações de gestão de dispositivos incluem a reposição de fábrica, atualização de firmware, a atualização de software, gestão de energia, gestão de rede e a conectividade e encriptação de dados.

## <a name="device-maintenance-windows"></a>Janelas de manutenção do dispositivo

Normalmente, pode configurar dispositivos para efetuar ações de cada vez que minimiza a interrupções planeadas e período de indisponibilidade. Janelas de manutenção do dispositivo são um padrão frequentemente utilizado para definir a hora quando um dispositivo deve ser atualizado a respetiva configuração. As soluções de back-end podem utilizar as propriedades do dispositivo duplo pretendidas para definir e ativar uma política no seu dispositivo que permite uma janela de manutenção. Quando um dispositivo recebe a política de janela de manutenção, pode utilizar a propriedade do dispositivo duplo comunicada para comunicar o estado da política. A aplicação de back-end, em seguida, pode utilizar consultas do dispositivo duplo atestem a conformidade de dispositivos e cada política.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou um método direto para acionar um reinício remoto num dispositivo. Utilizar as propriedades que relatados para reportar a última de reinício do dispositivo e consultar o dispositivo duplo para detetar a última de reinício do dispositivo da nuvem.

Para continuar a introdução ao IoT Hub e padrões de gestão de dispositivos como remota através da atualização de firmware ondas eletromagnéticas, consulte:

[Tutorial: Como efetuar uma atualização de firmware][lnk-fwupdate]

Para saber como expandir o seu IoT chama o método de solução e agenda em vários dispositivos, consulte o [agenda e as tarefas de difusão] [ lnk-tutorial-jobs] tutorial.

Para continuar a introdução ao IoT Hub, consulte [introdução ao IoT Edge][lnk-iot-edge].

[lnk-fwupdate]: ../articles/iot-hub/iot-hub-node-node-firmware-update.md
[lnk-tutorial-jobs]: ../articles/iot-hub/iot-hub-node-node-schedule-jobs.md
[lnk-iot-edge]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md