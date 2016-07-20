### Adicionar ou remover prefixos se ainda não criou uma ligação de gateway de VPN

- **Para adicionar** prefixos de endereços adicionais a um gateway de rede local que criou, mas que ainda não tem uma ligação de gateway de VPN, utilize o exemplo abaixo.

        $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para remover** um prefixo de endereço de um gateway de rede local que não tem uma ligação de VPN, utilize o exemplo abaixo. Não inclua os prefixos que já não são necessários. Neste exemplo, já não precisamos do prefixo 20.0.0.0/24 (do exemplo anterior), pelo que iremos atualizar o gateway de rede local e excluir esse prefixo.

        $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Adicionar ou remover prefixos se já tiver criado uma ligação de gateway de VPN

Se tiver criado a ligação de VPN e pretender adicionar ou remover os prefixos de endereço IP contidos no gateway de rede local, terá de realizar os seguintes passos por ordem. Este procedimento resultará num período de indisponibilidade da ligação VPN.

>[AZURE.IMPORTANT] Não elimine o gateway de VPN. Se o fizer, terá de voltar a repetir os passos para o recriar e reconfigurar o router no local com as novas definições.
 
1. Remova a ligação IPsec. 
2. Modifique os prefixos do gateway de rede local. 
3. Crie uma nova ligação IPsec. 

Pode utilizar o exemplo seguinte como orientação.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

    Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
    Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
    
    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



<!--HONumber=Jun16_HO2-->


