As ligações de Site a Site para uma rede no local requerem um dispositivo VPN. Enquanto não fornecemos os passos de configuração para todos os dispositivos VPN, as informações das seguintes ligações úteis ser-lhe-ão úteis:

- Para obter mais informações sobre os dispositivos VPN compatíveis, consulte [VPN Devices (Dispositivos VPN)](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). 
- Para ver as ligações para as definições de configuração de dispositivo, consulte [Validated VPN Devices (Dispositivos VPN Validados)](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable). As ligações para a configuração do dispositivo são fornecidas numa base de melhor esforço. É sempre melhor verificar com o fabricante do dispositivo para obter as mais recentes informações de configuração.
- Para obter informações sobre a edição de amostras de configuração do dispositivo, consulte [Editing samples (Editar amostras)](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing).
- Para os parâmetros de IPsec/IKE, consulte [Parameters (Parâmetros)](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec).
- Antes de configurar o dispositivo VPN, verifique a existência de [Problemas de compatibilidade de dispositivos conhecidos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) para o dispositivo VPN que pretende utilizar.

Quando configurar o dispositivo VPN, irá precisar dos seguintes itens:

- O endereço IP Público do gateway de rede virtual.

    -  Para encontrar o endereço IP Público através do portal do Azure, navegue para **Gateways de rede virtual** e, em seguida, clique no nome do gateway. 
    - Para encontrar o endereço IP Público do gateway de rede virtual com o PowerShell, utilize o seguinte exemplo, substituindo os valores pelos seus próprios.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- Uma chave partilhada. Esta é a mesma chave partilhada que especifica ao criar a ligação VPN de Site a Site. Nos nossos exemplos, iremos utilizar uma chave partilhada muito básica. Deve gerar uma chave mais complexa para utilizar.




