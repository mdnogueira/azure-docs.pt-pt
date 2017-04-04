As ligações de Site a Site para uma rede no local requerem um dispositivo VPN. Existem vários dispositivos VPN diferentes que funcionam com o Azure. Para obter informações sobre dispositivos VPN e definições de configuração, consulte [VPN Devices (Dispositivos VPN)](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). Antes de configurar o dispositivo VPN, verifique a existência de [Problemas de compatibilidade de dispositivos conhecidos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) para o dispositivo VPN que pretende utilizar. Para obter informações de configuração do dispositivo VPN, contacte o fabricante do dispositivo.

Quando configurar o dispositivo VPN, irá precisar dos seguintes itens:

- **O endereço IP Público** do gateway de rede virtual.

    -  Para encontrar o endereço IP Público através do portal do Azure, navegue para **Gateways de rede virtual** e, em seguida, clique no nome do gateway. 

    - Para encontrar o endereço IP Público do gateway de rede virtual com o PowerShell, utilize o seguinte exemplo, substituindo os valores pelos seus próprios.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **Uma chave partilhada**. Esta é a mesma chave partilhada que irá especificar ao criar a ligação VPN de Site a Site. Nos nossos exemplos, iremos utilizar uma chave partilhada muito básica. Deve gerar uma chave mais complexa para utilizar.




