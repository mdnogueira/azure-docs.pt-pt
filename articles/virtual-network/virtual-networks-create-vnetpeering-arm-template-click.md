---
title: Criar VNet Peering com modelos do Resource Manager | Microsoft Docs
description: Saiba como criar peering de uma rede virtual com os modelos no Resource Manager.
services: virtual-network
documentationcenter: ''
author: narayanannamalai
manager: jefco
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayanannamalai;annahar

---
# Criar VNet Peering com modelos do Resource Manager
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet peering com os modelos do Resource Manager, siga os passos abaixo:

1. Se nunca tiver utilizado o Azure PowerShell, veja [How to Install and Configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../powershell-install-configure.md) e siga as instruções até ao fim para iniciar sessão no Azure e selecionar a sua subscrição.
   
   > [!NOTE]
   > O cmdlet PowerShell para gerir o VNet Peering é fornecido com o [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
   > 
   > 
2. O texto abaixo mostra a definição da ligação de VNet peering da VNet1 para a VNet2, com base no cenário acima. Copie o conteúdo abaixo e guarde-o num ficheiro chamado VNetPeeringVNet1.json.
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }
3. A secção abaixo mostra a definição de uma ligação de VNet peering da VNet2 para a VNet1, com base no cenário acima.  Copie o conteúdo abaixo e guarde-o num ficheiro chamado VNetPeeringVNet2.json.
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }
   
    Como se viu no modelo acima, existem algumas propriedades configuráveis para VNet peering:
   
   | Opção | Descrição | Predefinição |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Se o espaço de endereços da VNet em modo de peering vai ser, ou não, incluído como parte da etiqueta virtual_network. |Sim |
   | AllowForwardedTraffic |Se o tráfego que não provém de uma VNet em modo de peering seja aceite ou removido. |Não |
   | AllowGatewayTransit |Permite que a VNet em modo de peering utilize o seu gateway de VNet. |Não |
   | UseRemoteGateways |Utilize o gateway da VNet em modo de peering. A VNet em modo de peering tem de ter um gateway configurado e AllowGatewayTransit selecionado. Não pode utilizar esta opção se tiver um gateway configurado. |Não |
   
    Cada ligação no VNet peering tem o conjunto das propriedades acima indicadas. Por exemplo, pode definir AllowVirtualNetworkAccess para Verdadeiro para a ligação VNet peering da VNet1 para a VNet2 e defini-lo como Falso para a ligação VNet peering na outra direção.
4. Para implementar o ficheiro de modelo, pode executar o cmdlet New-AzureRmResourceGroupDeployment para criar ou atualizar a implementação. Para obter mais informações sobre como utilizar os modelos do Resource Manager, consulte este [artigo](../resource-group-template-deploy.md).
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all
   
   > [!NOTE]
   > Substitua o nome do grupo de recursos e o ficheiro de modelo conforme apropriado.
   > 
   > 
   
    Abaixo é apresentado um exemplo com base no cenário anterior:
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all
   
    O resultado indica:
   
        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all
   
    O resultado indica:
   
        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
5. Após a conclusão da implementação, pode executar o cmdlet abaixo para ver o estado do peering:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2
   
    O resultado indica:
   
        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Uma vez estabelecido o peering neste cenário, deverá conseguir iniciar as ligações a partir de qualquer máquina virtual para qualquer outra máquina virtual em ambas as VNets. Por predefinição, AllowVirtualNetworkAccess é Verdadeiro e o VNet peering irá aprovisionar as ACLs adequadas para permitir a comunicação entre VNets. Continua a poder aplicar as regras do grupo de segurança de rede (NSG) para bloquear a conetividade entre sub-redes ou máquinas virtuais específicas, para obter um controlo mais refinado do acesso entre duas redes virtuais.  Para obter mais informações sobre a criação de regras NSG, consulte este [artigo](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar um VNet peering entre subscrições, siga os passos abaixo:

1. Inicie sessão no Azure com a conta do User-A privilegiada na Subscription-A e execute o seguinte cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5
   
    Isto não é um requisito. O peering pode ser estabelecido mesmo se os utilizadores emitirem individualmente pedidos de peering para as respetivas Vnets, desde que os pedidos correspondam. A adição de um utilizador com privilégios da outra VNet como utilizador na VNet local torna mais fácil a configuração.
2. Inicie sessão no Azure com a conta do User-B privilegiada para a Subscription-B e execute o seguinte cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3
3. No início de sessão do User-A, execute este cmdlet:
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all
   
    Eis como o ficheiro JSON está definido.  
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }
4. No início de sessão do User-B, execute o cmdlet seguinte:
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
   
    Eis como o ficheiro JSON está definido:
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }
   
    Uma vez estabelecido o peering neste cenário, deverá conseguir iniciar as ligações a partir de qualquer máquina virtual para qualquer outra máquina virtual de ambas as VNets em subscrições diferentes.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Neste cenário, pode implementar o modelo de exemplo abaixo para estabelecer o VNet peering.  Terá de definir a propriedade AllowForwardedTraffic como Verdadeiro, o que permite que a aplicação virtual de rede na VNet em modo de peering envie e receba tráfego.
   
    Eis o modelo para criar um VNet peering da HubVNet para a VNet1. Tenha em atenção que AllowForwardedTraffic está definido como falso.
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }
2. Eis o modelo para criar um VNet peering da VNet1 para a HubVnet. Tenha em atenção que AllowForwardedTraffic está definido como verdadeiro.
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }
3. Uma vez estabelecido o peering, pode consultar este [artigo](virtual-network-create-udr-arm-ps.md) para definir rotas definidas pelo utilizador (UDR) para redirecionar o tráfego da VNet1 através de uma aplicação virtual, de modo a utilizar as respetivas capacidades. Quando especificar o endereço do próximo salto na rota, pode configurá-lo para o endereço IP da aplicação virtual na VNet HubVNet em modo de peering.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para criar um peering entre redes virtuais a partir de modelos de implementação diferentes, siga os passos abaixo:

1. O texto abaixo mostra a definição de uma ligação VNet Peering da VNET1 para a VNET2 neste cenário. Só precisa de uma ligação para agrupar uma rede virtual clássica a uma rede virtual do Azure Resource Manager.
   
    Confirme que coloca o seu ID de subscrição com o local onde se encontra a rede virtual clássica ou VNET2 e altere MyResouceGroup para o nome do grupo de recursos adequado.
   
    {  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  "contentVersion": "1.0.0.0",  "parameters": {  },  "variables": {  },  "resources": [      {      "apiVersion": "2016-06-01",      "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",      "name": "VNET1/LinkToVNET2",      "location": "[resourceGroup().location]",      "properties": {      "allowVirtualNetworkAccess": true,      "allowForwardedTraffic": false,      "allowGatewayTransit": false,      "useRemoteGateways": false,          "remoteVirtualNetwork": {          "id": "[resourceId('Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"  }      }      }  ]  }
2. Para implementar o ficheiro de modelo, execute o cmdlet seguinte para criar ou atualizar a implementação.
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all
   
        Output shows:
   
        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
3. Assim que a implementação for concluída com êxito, pode executar o cmdlet abaixo para ver o estado do peering:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2
   
        Output shows:
   
        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Depois de ter estabelecido o peering entre uma VNet clássica e uma VNet do Resource Manage, deverá poder iniciar as ligações a partir de qualquer máquina virtual na VNET1 para qualquer máquina virtual na VNET2 e vice-versa.

<!--HONumber=Sep16_HO3-->


