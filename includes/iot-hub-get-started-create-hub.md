## <a name="create-an-iot-hub"></a>Criar um hub IoT
Crie um hub IoT para ligação da aplicação do dispositivo simulado. Os passos que se seguem mostram como concluir esta tarefa com o Portal do Azure.

1. Inicie sessão no [Portal do Azure][lnk-portal].
1. Na Barra de atalhos, clique em **Novo** > **Internet das Coisas** > **IoT Hub**.
   
    ![Barra de atalhos do portal do Azure][1]
1. No painel **Hub IoT**, escolha a configuração do seu hub IoT.
   
    ![Painel do hub IoT][2]
   
   1. Na caixa **Nome**, introduza um nome para o hub IoT. Se o **Nome** for válido e estiver disponível, aparece uma marca de verificação verde na caixa **Nome**.
    [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]
   
   1. Selecione um [escalão de preços e dimensionamento][lnk-pricing]. Este tutorial não requer um escalão específico. Para este tutorial, utilize o escalão F1 gratuito.
   1. Em **Grupo de recursos**, crie um grupo de recursos ou selecione um existente. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure][lnk-resource-groups].
   1. Em **Localização**, selecione a localização para alojar o hub IoT. Para este tutorial, escolha a localização mais próxima.
1. Quando tiver escolhido as opções de configuração do hub IoT, clique em **Criar**.  O Azure poderá demorar alguns minutos a criar o hub IoT. Para verificar o estado, pode monitorizar o progresso no Startboard ou no painel Notificações.
   
    ![Estado do novo hub IoT][3]
1. Quando o hub IoT tiver sido criado com êxito, clique no novo mosaico do hub IoT no portal do Azure para abrir o painel do novo hub IoT. Anote o **Nome de anfitrião** e, em seguida, clique em **Políticas de acesso partilhado**.
   
    ![Novo painel do hub IoT][4]
1. No painel **Políticas de acesso partilhado**, clique na política **iothubowner** e copie e anote a cadeia de ligação do Hub IoT no painel **iothubowner**. Para obter mais informações, veja [Controlo de acesso][lnk-access-control] no “Guia do programador do Hub IoT.”
   
    ![Painel das políticas de acesso partilhado][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
