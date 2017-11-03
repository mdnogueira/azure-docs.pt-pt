## <a name="create-an-iot-hub"></a>Criar um hub IoT
Crie um hub IoT para ligação da aplicação do dispositivo simulado. Os passos que se seguem mostram como concluir esta tarefa com o Portal do Azure.

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Agora que criou um hub IoT, localize as informações importantes que utilizar para ligar dispositivos e aplicações para o seu IoT hub. 

1. Quando o hub IoT tiver sido criado com êxito, clique no novo mosaico do mesmo no portal do Azure para abrir a janela das propriedades do hub IoT novo. Anote o **Nome de anfitrião** e, em seguida, clique em **Políticas de acesso partilhado**.
   
    ![Janela do hub IoT novo][4]
1. Em **Políticas de acesso partilhado**, clique na política **iothubowner** e copie e anote a cadeia de ligação do Hub IoT na janela **iothubowner**. Para obter mais informações, veja [Controlo de acesso][lnk-access-control] no “Guia do programador do Hub IoT.”
   
    ![Políticas de acesso partilhado][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
