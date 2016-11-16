## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Como criar uma VNet com a CLI do Azure
Pode utilizar a CLI do Azure para gerir os seus recursos do Azure na linha de comandos a partir de qualquer computador com Windows, Linux ou OSX. Para criar uma VNet com a CLI do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../articles/xplat-cli-install.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **azure config mode** para mudar para o modo Resource Manager, como mostrado abaixo.
   
        azure config mode arm
   
    O resultado esperado para o comando acima é o seguinte:
   
        info:    New mode is arm
3. Se necessário, execute **azure group create** para criar um novo grupo de recursos, como mostrado abaixo. Repare no resultado do comando. A lista apresentada depois do resultado explica os parâmetros utilizados. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
        azure group create -n TestRG -l centralus
   
    O resultado esperado para o comando acima é o seguinte:
   
        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
   
   * **-n (ou --name)**. Nome do novo grupo de recursos. Para o nosso cenário *TestRG*.
   * **-l (ou --location)**. Região do Azure onde será criado o novo grupo de recursos. Para o nosso cenário *centralus*.
4. Execute o comando **azure network vnet create** para criar uma VNet e uma sub-rede, como mostrado abaixo. 
   
        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus
   
    O resultado esperado para o comando acima é o seguinte:
   
        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK
   
   * **-g (ou --resource-group)**. Nome do grupo de recursos em que a VNet será criada. Para o nosso cenário *TestRG*.
   * **-n (ou --name)**. Nome da VNet a ser criada. Para o nosso cenário *TestVNet*.
   * **-a (ou --address-prefixes)**. Lista de blocos CIDR utilizados para o espaço de endereços VNet. Para o nosso cenário, *192.168.0.0/16*
   * **-l (ou --location)**. Região do Azure onde a VNet será criada. Para o nosso cenário *centralus*.
5. Execute o comando **azure network vnet subnet create** para criar uma sub-rede, como mostrado abaixo. Repare no resultado do comando. A lista apresentada depois do resultado explica os parâmetros utilizados.
   
        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
   
    O resultado esperado para o comando acima é o seguinte:
   
        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK
   
   * **-e (ou --vnet-name**. Nome da VNet onde a sub-rede será criada. Para o nosso cenário *TestVNet*.
   * **-n (ou --name)**. Nome da nova sub-rede. Para o nosso cenário *FrontEnd*.
   * **-a (or --address-prefix)**. Bloco CIDR da sub-rede. Para o nosso cenário, *192.168.1.0/24*.
6. Repita o passo 5 acima para criar outras sub-redes, se necessário. Para o nosso cenário, execute o comando abaixo para criar a sub-rede do *Back-end*.
   
        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24
7. Execute o comando **azure network vnet show** para ver as propriedades da nova VNet, como mostrado abaixo.
   
        azure network vnet show -g TestRG -n TestVNet
   
    O resultado esperado para o comando acima é o seguinte:
   
        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK



<!--HONumber=Nov16_HO2-->


