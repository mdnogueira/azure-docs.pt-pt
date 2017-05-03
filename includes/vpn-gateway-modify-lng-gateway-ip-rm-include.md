Para modificar o endereço IP do gateway, utilize o cmdlet "New-AzureRmVirtualNetworkGatewayConnection". Atualmente, o cmdlet "Set" não suporta a modificação do endereço IP do gateway.

### <a name="gwipnoconnection"></a>Modificar o endereço IP do gateway sem uma ligação de gateway
Para modificar o endereço IP do gateway de rede local ainda sem uma ligação, utilize o exemplo abaixo. Também pode modificar os prefixos de endereços em simultâneo. Certifique-se de que utiliza o nome existente do seu gateway de rede local, para substituir as definições atuais. Se não o fizer, irá criar um novo gateway de rede local, em vez de substituir o existente.

Utilize o exemplo seguinte, substituindo os valores pelos seus próprios:

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>Modificar o endereço IP do gateway com uma ligação de gateway existente
Se já existir uma ligação de gateway, tem primeiro de remover a ligação. Depois de a ligação ser removida, pode modificar o endereço IP do gateway e recriar uma nova ligação. Também pode modificar os prefixos de endereços em simultâneo. Este procedimento resulta num período de indisponibilidade da ligação VPN.

> [!IMPORTANT]
> Não elimine o gateway de VPN. Se o fizer, tem de retroceder os passos para recriá-lo. Além disso, tem de atualizar o seu dispositivo VPN no local com o novo endereço IP do gateway do VPN.
> 
> 

1. Remova a ligação. Pode encontrar o nome da ligação com o cmdlet "Get-AzureRmVirtualNetworkGatewayConnection".

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Modifique o valor "GatewayIpAddress". Também pode modificar os prefixos de endereços em simultâneo. Certifique-se de que utiliza o nome existente do seu gateway de rede local, para substituir as definições atuais. Se não o fizer, irá criar um novo gateway de rede local, em vez de substituir o existente.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Crie a ligação. Neste exemplo, configuramos uma ligação do tipo IPsec. Ao recriar a ligação, utilize o tipo de ligação especificado para a sua configuração. Para tipos de ligação adicionais, veja a página [cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).  Para obter o nome do VirtualNetworkGateway, pode executar o cmdlet "Get-AzureRmVirtualNetworkGateway".
   
    Defina as variáveis.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Crie a ligação.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```