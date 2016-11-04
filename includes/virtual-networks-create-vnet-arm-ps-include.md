## Como criar uma VNet com o PowerShell
Para criar uma VNet com o PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, veja [How to Install and Configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../articles/powershell-install-configure.md) e siga as instruções até ao fim para iniciar sessão no Azure e selecionar a sua subscrição.
2. Se necessário, crie um novo grupo de recursos, conforme mostrado abaixo. Para o nosso cenário, crie um grupo de recursos com o nome *TestRG*. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/resource-group-overview.md).
   
        New-AzureRmResourceGroup -Name TestRG -Location centralus
   
    Resultado esperado:
   
        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
   
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG   
3. Crie uma nova VNet com o nome *TestVNet*, como mostrado abaixo.
   
        New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
            -AddressPrefix 192.168.0.0/16 -Location centralus   
   
    Resultado esperado:
   
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : []
4. Armazene o objeto da rede virtual numa variável, como mostrado abaixo.
   
        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
   
   > [!TIP]
   > Pode combinar os passos 3 e 4 executando **$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**.
   > 
   > 
5. Adicione uma sub-rede à nova variável da VNet, como mostrado abaixo.
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
   
    Resultado esperado:
   
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": null,
                                "Id": null,
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": null,
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": null
                              }
                            ]
6. Repita o passo 5 acima para cada sub-rede que pretende criar. O comando abaixo cria a sub-rede do *Back-end* para o nosso cenário.
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24
7. Embora crie sub-redes, elas atualmente só existem na variável local utilizada para obter a VNet que criou no passo 4 acima. Para guardar as alterações no Azure, execute o cmdlet **Set-AzureRmVirtualNetwork**, como mostrado abaixo.
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 
   
    Resultado esperado:
   
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": []
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

<!--HONumber=Jun16_HO2-->


