### <a name="noconnection"></a>Como adicionar ou remover prefixos sem uma ligação de gateway

- **Para adicionar** prefixos de endereços adicionais a um gateway de rede local que criou, mas que ainda não tem uma ligação de gateway, utilize o exemplo abaixo. Certifique-se de que altera os valores para os seus próprios.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Para remover** um prefixo de endereço de um gateway de rede local que não tem uma ligação de VPN, utilize o exemplo abaixo. Não inclua os prefixos que já não são necessários. Neste exemplo, já não precisamos do prefixo 20.0.0.0/24 (do exemplo anterior), pelo que iremos atualizar o gateway de rede local e excluir esse prefixo.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Como adicionar ou remover prefixos com uma ligação de gateway existente

Se tiver criado a ligação de gateway e pretender adicionar ou remover os prefixos de endereço IP contidos no gateway de rede local, terá de realizar os seguintes passos por ordem. Este procedimento resultará num período de indisponibilidade da ligação VPN. Ao atualizar os prefixos, terá primeiro de remover a ligação, modificar os prefixos e, em seguida, criar uma nova ligação. Nos exemplos abaixo, certifique-se de que altera os valores para os seus próprios.

>[AZURE.IMPORTANT] Não elimine o gateway de VPN. Se o fizer, terá de voltar a repetir os passos para o recriar e reconfigurar o router no local com as novas definições.
 
1. Remova a ligação.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Modifique os prefixos de endereços IP do gateway da sua rede local.

    Defina a variável para o LocalNetworkGateway.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    Modifique os prefixos.

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Crie a ligação. Neste exemplo, estamos a configurar uma ligação do tipo IPsec. Ao recriar a ligação, utilize o tipo de ligação especificado para a sua configuração. Para tipos de ligação adicionais, veja a página [cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).

    Defina a variável para o VirtualNetworkGateway.

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    Crie a ligação. Tenha em atenção que este exemplo utiliza a variável $local definida no passo anterior.


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'



<!--HONumber=ago16_HO4-->


