Para verificar uma ligação VPN no portal do Azure, aceda a **Gateways de rede virtual** **>** ***clique no nome do gateway*** **>** **Definições** **>** **Ligações**. Pode ver mais informações sobre a ligação selecionando o respetivo nome. No exemplo abaixo, a ligação não está ligada e não existem dados a circular.


![Verificar ligação](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### Para verificar a ligação com o PowerShell

Também pode verificar se a ligação teve êxito com `Get-AzureRmVirtualNetworkGatewayConnection –Debug`. Pode utilizar o seguinte exemplo de cmdlet, configurando os valores para corresponder aos seus. Quando lhe for pedido, selecione “A” para executar Todos.

    Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Após concluir o cmdlet, veja os valores. No exemplo abaixo, o estado da ligação é apresentado como *Ligado* e pode ver bytes de entrada e de saída.

    Body:
    {
      "name": "localtovon",
      "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
    ltovon",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
        "virtualNetworkGateway1": {
          "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
    teways/vnetgw1"
        },
        "localNetworkGateway2": {
          "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
    ways/LocalSite"
        },
        "connectionType": "IPsec",
        "routingWeight": 10,
        "sharedKey": "abc123",
        "connectionStatus": "Connected",
        "ingressBytesTransferred": 33509044,
        "egressBytesTransferred": 4142431
      }


<!--HONumber=Jun16_HO2-->


