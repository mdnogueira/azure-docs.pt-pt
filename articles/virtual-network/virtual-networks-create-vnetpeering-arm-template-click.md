<properties
   pageTitle="Criar VNet Peering com Modelos do Resource Manager | Microsoft Azure"
   description="Saiba como criar peering de uma rede virtual com os modelos no Resource Manager."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos"/>

# Criar VNet Peering com Modelos do Resource Manager

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet peering com os Modelos do Resource Manager, siga os passos abaixo:

1. Se nunca tiver utilizado o Azure PowerShell, veja [How to Install and Configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../powershell-install-configure.md) e siga as instruções até ao fim para iniciar sessão no Azure e selecionar a sua subscrição.

Nota: o cmdlet do PowerShell para gerir o VNet peering vem incluído no [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. A secção abaixo mostra a definição da ligação de VNet peering da VNet1 para a VNet2, com base no cenário acima. Copie o seu conteúdo para um ficheiro e guarde-o no ficheiro VNetPeeringVNet1.json

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
    

3. A secção abaixo mostra a definição da ligação de VNet peering da VNet2 para a VNet1, com base no cenário acima.  Copie o seu conteúdo para um ficheiro e guarde-o no ficheiro VNetPeeringVNet2.json

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

Como se viu nos modelos acima, existem algumas propriedades configuráveis para VNet peering:

|Opção|Descrição|Predefinição|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Se o espaço de endereços da VNet em modo de peering vai ser incluído como parte da Etiqueta Virtual_network|Sim|
|AllowForwardedTraffic|Permite que o tráfego que não provém da VNet em modo de peering seja aceite ou removido|Não|
|AllowGatewayTransit|Permite que a VNet em modo de peering utilize o seu gateway de VNet|Não|
|UseRemoteGateways|Utilize o gateway da VNet em modo de peering. A VNet em modo de peering tem de ter um gateway configurado e AllowGatewayTransit deve estar selecionado. Não pode utilizar esta opção se tiver um gateway configurado|Não|

Cada ligação no VNet peering tem um conjunto das propriedades acima indicadas. Por exemplo, pode definir AllowVirtualNetworkAccess para Verdadeiro para a ligação VNet peering da VNet1 para a VNet2 e defini-lo como Falso para a ligação VNet peering da outra direção. 


4. Para implementar o ficheiro de modelo, pode executar o cmdlet New-AzureRmResourceGroupDeployment para criar ou atualizar a implementação. Para obter mais informações sobre como utilizar o modelo do Resource Manager, consulte este [artigo](../resource-group-template-deploy.md)

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

> [AZURE.NOTE] Substitua o nome do grupo de recursos e o ficheiro de modelo conforme apropriado. 

Abaixo é apresentado um exemplo com base no cenário acima:

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

Uma vez estabelecido o peering neste cenário, deverá conseguir iniciar as ligações a partir de qualquer máquina virtual para qualquer outra máquina virtual de ambas as VNets. Por predefinição, AllowVirtualNetworkAccess é Verdadeiro e VNet Peering irá aprovisionar as ACLs adequadas para permitir a comunicação entre VNets. No entanto, continua a poder aplicar as regras NSG para bloquear a conectividade, por exemplo, entre sub-redes ou máquinas virtuais específicas, para obter um controlo mais refinado do acesso entre duas redes virtuais.  Para obter mais informações sobre a criação de regras NSG, consulte este [artigo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar VNet peering entre subscrições, siga os passos abaixo:

1. Inicie sessão no Azure com o User-A que tem privilégios para a Subscription-A e execute o cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

Isto não é um requisito. O peering pode ser estabelecido mesmo se os utilizadores emitirem individualmente pedidos de peering para as respetivas Vnets, desde que os pedidos correspondam. A adição do utilizador com privilégios da outra VNet como utilizador na VNet local torna mais fácil a configuração. 

2. Inicie sessão no Azure com o User-B que tem privilégios para a Subscription-B e execute o cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Em seguida, no início de sessão do User-A, execute o cmdlet

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
   
4. Em seguida, no início de sessão do User-B, execute o cmdlet

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

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Neste cenário, pode implementar o modelo de exemplo abaixo para estabelecer o VNet peering e precisa, particularmente, de definir a propriedade AllowForwardedTraffic como Verdadeiro, de modo a permitir ao dispositivo de Rede Virtual na VNet em modo de peering enviar e receber tráfego. Eis o modelo para criar VNet peering da HubVNet para a VNet1. Tenha em atenção que AllowForwardedTraffic é definido como falso

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

2. Eis o modelo para criar VNet peering da VNet1 para a HubVnet. Tenha em atenção que AllowForwardedTraffic é definido como verdadeiro. 

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


3. Uma vez estabelecido o peering, pode consultar este [artigo](virtual-network-create-udr-arm-ps.md) e definir a Rota Definida pelo Utilizador (UDR) para redirecionar o tráfego da VNet1 através de uma aplicação virtual, de modo a utilizar as respetivas capacidades. Quando especificar o endereço do Próximo Salto na rota, pode configurá-lo para o endereço IP da aplicação virtual na VNet HubVNet em modo de peering


<!--HONumber=Aug16_HO1-->


