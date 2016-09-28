### Para verificar a sua ligação com o PowerShell

Pode verificar se a sua ligação foi concluída com êxito com o cmdlet`Get-AzureRmVirtualNetworkGatewayConnection`, com ou sem `-Debug`. 

1. Utilize o seguinte exemplo de cmdlet, configurando os valores para corresponder aos seus. Se lhe for pedido, selecione "A" para executar "Todos". No exemplo, `-Name` refere-se para o nome da ligação que criou e quer testar.

        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. Quando o cmdlet terminar, veja os valores. No exemplo abaixo, o estado da ligação é apresentado como "Ligado" e pode ver bytes de entrada e de saída.

        Body:
        {
          "name": "MyGWConnection",
          "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
          "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
            "virtualNetworkGateway1": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
        teways/vnetgw1"
            },
            "localNetworkGateway2": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
        ways/LocalSite"
            },
            "connectionType": "IPsec",
            "routingWeight": 10,
            "sharedKey": "abc123",
            "connectionStatus": "Connected",
            "ingressBytesTransferred": 33509044,
            "egressBytesTransferred": 4142431
          }

### Para verificar a ligação com o Portal do Azure

No portal do Azure, pode ver o estado da ligação ao navegar pela mesma. Existem múltiplas formas de efetuar este procedimento. Abaixo encontra-se uma forma para navegar para a ligação.

1. No [portal do Azure,](http://portal.azure.com), navegue para **Gateways de rede virtual**. Clique no nome do seu gateway.
2. No painel, em **Definições**, clique em **Ligações**. Pode ver o estado de cada ligação.
3. Para obter mais informações sobre a ligação, clique no nome da ligação. Na página Informações Básicas da sua ligação, observe com atenção o **Estado da Ligação**. Quando efetuar uma ligação com êxito o estado será alterado para "Com Êxito" e "Ligado". Pode verificar o movimento dos dados ao consultar os campos **Entrada de dados** e **Saída de dados**.

    No exemplo abaixo, o **Estado da Ligação** é "Não ligado". 

    ![Verificar ligação](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)

<!--HONumber=Sep16_HO3-->


