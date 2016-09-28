## Implementar o modelo ARM com a CLI do Azure

Para implementar o modelo ARM que transferiu com a CLI do Azure, siga os passos abaixo:

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../articles/xplat-cli-install.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **`azure config mode`** para mudar para o modo Resource Manager, como mostrado abaixo.

        azure config mode arm

    O resultado esperado para o comando acima é o seguinte:

        info:    New mode is arm

3. Se necessário, execute o comando **`azure group create`** para criar um novo grupo de recursos, como mostrado abaixo. Repare no resultado do comando. A lista apresentada depois do resultado explica os parâmetros utilizados. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Azure Resource Manager](../articles/resource-group-overview.md).

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

    - **-n (ou --name)**. Nome do novo grupo de recursos. Para o nosso cenário *TestRG*.
    - **-l (ou --location)**. Região do Azure onde será criado o novo grupo de recursos. Para o nosso cenário *centralus*.

4. Execute o cmdlet **`azure group deployment create`** para implementar a nova VNet com o modelo e os ficheiros de parâmetros que transferiu e alterou acima. A lista apresentada depois do resultado explica os parâmetros utilizados.

        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

    O resultado esperado para o comando acima é o seguinte:

        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK

    - **-g (ou --resource-group)**. Nome do grupo de recursos em que a nova VNet vai ser criada.
    - **-f (ou --template-file)**. Caminho para o ficheiro do modelo ARM.
    - **-e (ou --parameters-file)**. Caminho para o ficheiro de parâmetros ARM.

5. Execute o comando **`azure network vnet show`** para ver as propriedades da nova VNet, como mostrado abaixo.

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


<!--HONumber=Sep16_HO3-->


