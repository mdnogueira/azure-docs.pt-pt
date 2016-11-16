Para alterar o endereço IP do gateway, utilize o cmdlet `New-AzureRmVirtualNetworkGatewayConnection`. Desde que mantenha o nome do gateway de rede local exatamente igual ao existente, as definições serão substituídas. Neste momento, o cmdlet "Set" não suporta a modificação do endereço IP do gateway.

### <a name="a-namegwipnoconnectionahow-to-modify-the-gateway-ip-address-no-gateway-connection"></a><a name="gwipnoconnection"></a>Como modificar o endereço IP do gateway sem uma ligação de gateway
Para atualizar o endereço IP do gateway de rede local ainda sem uma ligação, utilize o exemplo abaixo. Também pode atualizar os prefixos de endereços em simultâneo. As definições que especificar irão substituir as definições existentes. Certifique-se de que utiliza o nome existente do seu gateway de rede local. Se não o fizer, será criado um novo gateway de rede local, sem substituir o existente.

Utilize o exemplo seguinte, substituindo os valores pelos seus próprios.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="a-namegwipwithconnectionahow-to-modify-the-gateway-ip-address-existing-gateway-connection"></a><a name="gwipwithconnection"></a>Como modificar o endereço IP do gateway com uma ligação de gateway existente
Se já existir uma ligação de gateway, terá primeiro de remover a ligação. Em seguida, pode modificar o endereço IP do gateway e recriar uma nova ligação. Este procedimento resultará num período de indisponibilidade da ligação VPN.

> [!IMPORTANT]
> Não elimine o gateway de VPN. Se o fizer, terá de repetir os passos para o recriar e reconfigurar o router no local com o endereço IP que será atribuído ao gateway recentemente criado.
> 
> 

1. Remova a ligação. Pode encontrar o nome da sua ligação, ao utilizar o cmdlet `Get-AzureRmVirtualNetworkGatewayConnection`.
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName
2. Modifique o valor GatewayIpAddress. Nesta altura, se necessário, também pode modificar os prefixos de endereços. Tenha em atenção que isto irá substituir as definições do gateway de rede local existentes. Utilize o nome existente do seu gateway de rede local ao efetuar a modificação, para que as definições sejam substituídas. Se não o fizer, será criado um novo gateway de rede local, sem modificar o existente.
   
        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
3. Crie a ligação. Neste exemplo, estamos a configurar uma ligação do tipo IPsec. Ao recriar a ligação, utilize o tipo de ligação especificado para a sua configuração. Para tipos de ligação adicionais, veja a página [cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).  Para obter o nome do VirtualNetworkGateway, pode executar o cmdlet `Get-AzureRmVirtualNetworkGateway`.
   
    Defina as variáveis:
   
        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
   
    Crie a ligação:
   
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



<!--HONumber=Nov16_HO2-->


