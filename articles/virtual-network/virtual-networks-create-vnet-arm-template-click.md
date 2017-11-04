---
title: Criar uma rede virtual | Modelo Azure Resource Manager | Microsoft Docs
description: Saiba como criar uma rede virtual com um modelo Azure Resource Manager.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81602766848a91331c8d811ea1c8ec3ffae44b96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Criar uma rede virtual com um modelo Azure Resource Manager

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

O Azure tem dois modelos de implementação: a implementação do Azure Resource Manager e a implementação clássica. A Microsoft recomenda a criação de recursos com o modelo de implementação Resource Manager. Para saber mais sobre as diferenças entre os dois modelos, veja o artigo [Understand Azure deployment models (Compreender os modelos de implementação do Azure)](../azure-resource-manager/resource-manager-deployment-model.md).
 
Este artigo explica como criar uma VNet através do modelo de implementação do Resource Manager através de um modelo Azure Resource Manager. Também pode criar uma VNet através do Resource Manager com outras ferramentas ou criá-la através do modelo de implementação clássica, ao selecionar outra opção na lista seguinte:

> [!div class="op_single_selector"]
- [Portal](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [Modelo](virtual-networks-create-vnet-arm-template-click.md)
- [Portal (Clássico)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (Clássico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (Clássica)](virtual-networks-create-vnet-classic-cli.md)

Irá aprender a transferir e modificar o modelo ARM existente a partir do GitHub, e a implementar o modelo a partir do GitHub, PowerShell e da CLI do Azure.

Se estiver a implementar o modelo ARM diretamente a partir do GitHub, sem quaisquer alterações, avance para [implementar um modelo a partir do github](#deploy-the-arm-template-by-using-click-to-deploy).

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Transferir e compreender o modelo Azure Resource Manager
Pode transferir o modelo existente para criar uma VNet e duas sub-redes a partir do GitHub, efetuar quaisquer alterações que pretender e reutilizá-lo. Para tal, execute os seguintes passos:

1. Navegue até à [página do modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Clique em **azuredeploy.json** e em **RAW**.
3. Guarde o ficheiro numa pasta local do computador.
4. Se estiver familiarizado com modelos, avance para o passo 7.
5. Abra o ficheiro que guardou e observe o conteúdo em **parâmetros**, na linha 5. Os parâmetros do modelo ARM fornecem um marcador de posição para valores que podem ser preenchidos durante a implementação.
   
   | Parâmetro | Descrição |
   | --- | --- |
   | **localização** |Região do Azure onde a VNet vai ser criada |
   | **vnetName** |Nome para a nova VNet |
   | **addressPrefix** |Espaço de endereços da VNet, no formato CIDR |
   | **subnet1Name** |Nome para a primeira VNet |
   | **subnet1Prefix** |Bloco CIDR para a primeira sub-rede |
   | **subnet2Name** |Nome para a segunda VNet |
   | **subnet2Prefix** |Bloco CIDR para a segunda sub-rede |
   
   > [!IMPORTANT]
   > Os modelos Azure Resource Manager mantidos no GitHub podem ser alterados ao longo do tempo. Verifique o modelo antes de o utilizar.
   > 
   > 
6. Verifique o conteúdo em **recursos** e tenha em atenção o seguinte:
   
   * **tipo**. Tipo de recurso a ser criado pelo modelo. Neste caso, **Microsoft.Network/virtualNetworks**, que representa uma VNet.
   * **nome**. Nome do recurso. Tenha em atenção a utilização de **[parameters('vnetName')]**, que significa que o nome será fornecido como uma entrada pelo utilizador ou por um ficheiro de parâmetros durante a implementação.
   * **propriedades**. Lista de propriedades do recurso. Este modelo utiliza as propriedades do espaço de endereços e da sub-rede durante a criação da VNet.
7. Regresse à [página do modelo de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Clique em **azuredeploy-paremeters.json** e em **RAW**.
9. Guarde o ficheiro numa pasta local do computador.
10. Abra o ficheiro que guardou e edite os valores dos parâmetros. Utilize os seguintes valores abaixo para implementar a VNet descrita no cenário:

    ```json
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Guarde o ficheiro.


## <a name="deploy-the-template-using-powershell"></a>Implementar o modelo com o PowerShell

Conclua os seguintes passos para implementar o modelo que transferiu com o PowerShell:

1. Instalar e configurar o Azure PowerShell, efetuando os passos a [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) artigo.
2. Execute o seguinte comando para criar um novo grupo de recursos:

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    O comando cria um grupo de recursos denominado *TestRG* no *EUA Central* região do azure. Para obter mais informações sobre os grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    Resultado esperado:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. Execute o seguinte comando para implementar a nova VNet utilizando os ficheiros de modelo e os parâmetros que transferiu e alterou acima:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Resultado esperado:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. Execute o seguinte comando para ver as propriedades da nova VNet:

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Resultado esperado:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
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
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Implementar o modelo com clique para implementar

Pode reutilizar predefinido modelos Azure Resource Manager carregados para um repositório GitHub mantido pela Microsoft e abra-a para a Comunidade. Estes modelos podem ser implementados diretamente do GitHub ou transferidos e modificados para atender às suas necessidades. Para implementar um modelo que cria uma VNet com duas sub-redes, conclua os seguintes passos:

1. Num browser, navegue para [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Desloque-se para baixo na lista de modelos e clique em **101 vnet-two subnets**. Marque o ficheiro **README.md**, como mostrado abaixo.

    ![Ficheiro READEME.md no github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Clique em **Implementar no Azure**. Se necessário, introduza as suas credenciais de início de sessão do Azure. 
4. No painel **Parâmetros**, introduza os valores que pretende utilizar para criar a sua nova VNet e clique em **OK**. A figura seguinte mostra os valores para o cenário:
   
    ![Parâmetros do modelo ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Clique em **Grupo de recursos** e selecione um grupo de recursos a qual deve adicionar a VNet ou clique em **Criar novo** para adicionar a VNet a um novo grupo de recursos. A figura seguinte mostra o recurso de definições de grupo para um novo grupo de recursos chamado **TestRG**:

    ![Grupo de recursos](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. Se necessário, altere as definições da **Subscrição** e da **Localização** para a sua VNet.
7. Se não pretender ver a VNet como um mosaico no **Startboard**, desative **Afixar ao Startboard**.
8. Clique em **termos legais**, leia os termos de licenciamento e clique em **comprar** para aceitar. 
9. Clique em **Criar** para criar a VNet.
   
    ![Mosaico Submeter implementação no portal de pré-visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. Assim que a implementação estiver concluída, no, clique em portal do Azure **mais serviços**, tipo *redes virtuais* na caixa do filtro aparece, em seguida, clique em redes virtuais para ver o painel de redes virtuais. No painel, clique em *TestVNet*. No *TestVNet* painel, clique em **sub-redes** para ver as sub-redes criadas, conforme mostrado na imagem seguinte:
    
     ![Criar a VNet no portal de pré-visualização](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como ligar:

- Uma máquina virtual (VM) a uma rede virtual, nos artigos [Criar VM do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Criar uma VM do Linux](../virtual-machines/linux/quick-create-portal.md). Em vez de criar um VNet e uma sub-rede nos passos dos artigos, pode selecionar uma VNet e uma sub-rede existentes às quais ligar uma VM.
- A rede virtual a outras redes virtuais, no artigo [Ligar VNets](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- A rede virtual a uma rede no local através de uma rede privada virtual (VPN) site a site ou de um circuito do ExpressRoute. Leia os artigos [Connect a VNet to an on-premises network using a site-to-site VPN (Ligar uma VNet a uma rede no local com uma VPN site a site)](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) e [Link a VNet to an ExpressRoute circuit (Ligar uma VNet a um circuito do ExpressRoute)](../expressroute/expressroute-howto-linkvnet-arm.md) para saber como.
