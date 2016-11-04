
Para configurar um dispositivo VPN, vai precisar do endereço IP público do gateway da rede virtual para configurar o dispositivo VPN no local. Contacte o fabricante do dispositivo para obter as informações de configuração específicas e configurar o dispositivo. Veja [Dispositivos VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) para obter mais informações sobre os dispositivos VPN que funcionam bem com o Azure.

Para localizar o endereço IP público do gateway de rede virtual com o PowerShell, utilize o seguinte exemplo:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Também pode ver o endereço IP público do gateway da rede virtual com Portal do Azure. Navegue até **Gateways da rede virtual** e clique no nome do seu gateway.

<!--HONumber=Sep16_HO3-->


