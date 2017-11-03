---
title: "Padrões de redes para o Azure Service Fabric | Microsoft Docs"
description: "Descreve os padrões comuns da rede para recursos de infraestrutura de serviço e como criar um cluster utilizando as funcionalidades de redes do Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: 535ea21a2c08be5f676ee24269b323a415b92607
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-networking-patterns"></a>Padrões de rede do Service Fabric
Pode integrar o seu cluster do Azure Service Fabric com outras funcionalidades de rede do Azure. Neste artigo, vamos mostrar-lhe como criar clusters que utilizam as seguintes funcionalidades:

- [Rede virtual existente ou a sub-rede](#existingvnet)
- [Endereço IP público estático](#staticpublicip)
- [Balanceador de carga interno só](#internallb)
- [Balanceador de carga internos e externos](#internalexternallb)

Recursos de infraestrutura de serviço é executado num conjunto de dimensionamento padrão máquina virtual. Qualquer funcionalidade que pode utilizar um conjunto de dimensionamento de máquina virtual, pode utilizar com um cluster do Service Fabric. As secções dos modelos Azure Resource Manager para conjuntos de dimensionamento de máquina virtual e de recursos de infraestrutura do serviço de rede são idênticas. Depois de implementar uma rede virtual existente, é fácil incorporar outras funcionalidades de rede, como o Azure ExpressRoute, Gateway de VPN do Azure, um grupo de segurança de rede e peering de rede virtual.

Recursos de infraestrutura de serviço é exclusivo de outras funcionalidades de rede, um aspeto. O [portal do Azure](https://portal.azure.com) internamente utiliza o fornecedor de recursos do Service Fabric para a chamada para um cluster para obter informações sobre nós e aplicações. O fornecedor de recursos do Service Fabric necessita de acesso de entrada acessível publicamente para a porta de gateway HTTP (porta 19080, por predefinição) no ponto final de gestão. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) utiliza o ponto final de gestão para gerir o cluster. O fornecedor de recursos do Service Fabric também utiliza esta porta para consulta obter informações sobre o cluster, para apresentar no portal do Azure. 

Se a porta 19080 não está acessível a partir do fornecedor de recursos do Service Fabric, uma mensagem como *não encontrados nós* aparece no portal, e a sua lista de nós e aplicação aparece em branco. Se pretender ver o seu cluster no portal do Azure, o Balanceador de carga tem de expor um endereço IP público e o grupo de segurança de rede têm de permitir tráfego de porta 19080 recebido. Se a configuração não cumpra estes requisitos, o portal do Azure não apresentar o estado do cluster.

## <a name="templates"></a>Modelos

Todos os modelos de Service Fabric, estão em [um transferir ficheiro](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip). Deverá conseguir implementar os modelos como-utilizando os seguintes comandos do PowerShell. Se estiver a implementar o modelo de rede Virtual do Azure existente ou o modelo de IP público estático, ler primeiro o [inicial configuração](#initialsetup) secção deste artigo.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configuração inicial

### <a name="existing-virtual-network"></a>Rede virtual existente

No exemplo seguinte, iremos começar com uma rede virtual existente com o nome ExistingRG vnet, no **ExistingRG** grupo de recursos. A sub-rede é o nome predefinido. Estes recursos predefinidos são criados ao utilizar o portal do Azure para criar uma máquina de virtual (VM) padrão. Pode criar a rede virtual e uma sub-rede sem criar a VM, mas é o objetivo principal da adição de um cluster para uma rede virtual existente para fornecer conectividade de rede para as outras VMs. Criar a VM proporciona um bom exemplo de como uma rede virtual existente, normalmente, é utilizada. Se o cluster do Service Fabric utiliza apenas um balanceador de carga interno, sem um endereço IP público, pode utilizar a VM e o IP público como segura *ir caixa*.

### <a name="static-public-ip-address"></a>Endereço IP público estático

Um endereço IP público estático é, geralmente, um recurso dedicado gerido separadamente do VM ou VMs que está atribuído à. É aprovisionada num grupo de recursos de rede dedicado (como opposed no recurso de cluster do Service Fabric agrupar próprio). Crie um endereço IP público estático com o nome staticIP1 no mesmo ExistingRG grupo de recursos, no portal do Azure ou através do PowerShell:

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Modelo de Service Fabric

Os exemplos neste artigo, utilizamos o Template de Service Fabric. Pode utilizar o Assistente de portal padrão para transferir o modelo do portal, antes de criar um cluster. Também pode utilizar um dos modelos no [Galeria de modelo](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric), como o [cluster do Service Fabric cinco nós](https://azure.microsoft.com/en-us/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Rede virtual existente ou a sub-rede

1. Altere o parâmetro de sub-rede para o nome da sub-rede existente e, em seguida, adicione os dois parâmetros novos para referenciar a rede virtual existente:

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```


2. Alterar o `vnetID` variável para apontar para a rede virtual existente:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Remover `Microsoft.Network/virtualNetworks` dos seus recursos Azure não crie uma nova rede virtual:

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

4. Comente da rede virtual do `dependsOn` atributo `Microsoft.Compute/virtualMachineScaleSets`, pelo que não dependem por criar uma nova rede virtual:

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. Implemente o modelo:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Após a implementação, a rede virtual deve incluir o novo conjunto de dimensionamento de VMs. O tipo de nó de conjunto de dimensionamento de máquina virtual deve mostrar a rede virtual existente e sub-rede. Também pode utilizar protocolo RDP (Remote Desktop Protocol) para aceder a VM que já estava na rede virtual e enviar um ping a escala novo conjunto de VMs:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Para obter outro exemplo, consulte [um que não é específico do Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Endereço IP público estático

1. Adicione parâmetros para o nome do grupo de recursos do IP estático existente, o nome e o nome de domínio completamente qualificado (FQDN):

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Remova o `dnsName` parâmetro. (O endereço IP estático já tem um.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Adicione uma variável para referenciar o endereço IP estático existente:

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Remover `Microsoft.Network/publicIPAddresses` dos seus recursos Azure não crie um novo endereço IP:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Comente o endereços IP a partir de `dependsOn` atributo `Microsoft.Network/loadBalancers`, pelo que não dependem por criar um novo endereço IP:

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. No `Microsoft.Network/loadBalancers` recurso, altere o `publicIPAddress` elemento do `frontendIPConfigurations` para referenciar o endereço IP estático existente em vez de um criado recentemente:

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. No `Microsoft.ServiceFabric/clusters` recurso, alteração `managementEndpoint` ao FQDN de DNS do endereço IP estático. Se estiver a utilizar um cluster seguro, certifique-se de que altera *http://* para *https://*. (Tenha em atenção que este passo apenas se aplica a clusters do Service Fabric. Se estiver a utilizar um conjunto de dimensionamento de máquina virtual, ignore este passo.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Implemente o modelo:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Após a implementação, pode ver que o Balanceador de carga está vinculado ao endereço IP estático público do outro grupo de recursos. Ponto final de ligação do cliente do Service Fabric e [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ponto de endpoint para o FQDN DNS do endereço IP estático.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Balanceador de carga interno só

Este cenário substitui o Balanceador de carga externo no modelo de Service Fabric predefinido com um balanceador de carga meramente internos. Para as implicações para o portal do Azure e para o fornecedor de recursos do Service Fabric, consulte a secção anterior.

1. Remova o `dnsName` parâmetro. (Que não é necessária.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcionalmente, se utilizar um método de alocação estático, pode adicionar um parâmetro de endereço IP estático. Se utilizar um método de alocação dinâmica, não é necessário efetuar este passo.

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Remover `Microsoft.Network/publicIPAddresses` dos seus recursos Azure não crie um novo endereço IP:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Remova o endereço IP `dependsOn` atributo `Microsoft.Network/loadBalancers`, pelo que não dependem por criar um novo endereço IP. Adicione a rede virtual `dependsOn` atributo porque o Balanceador de carga depende agora a sub-rede da rede virtual:

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Alterar o Balanceador de carga `frontendIPConfigurations` definição de utilizar um `publicIPAddress`, a utilização de uma sub-rede e `privateIPAddress`. `privateIPAddress`utiliza um predefinidas endereço IP estático interno. Para utilizar um endereço IP dinâmico, remova o `privateIPAddress` elemento e, em seguida, altere `privateIPAllocationMethod` para **dinâmica**.

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. No `Microsoft.ServiceFabric/clusters` recurso, alteração `managementEndpoint` para apontar para o endereço de Balanceador de carga interno. Se utilizar um cluster seguro, certifique-se de que altera *http://* para *https://*. (Tenha em atenção que este passo apenas se aplica a clusters do Service Fabric. Se estiver a utilizar um conjunto de dimensionamento de máquina virtual, ignore este passo.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Implemente o modelo:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Após a implementação, o seu Balanceador de carga utiliza o endereço IP privado estático 10.0.0.250. Se tiver outra máquina na mesma rede virtual, pode aceder à interna [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ponto final. Tenha em atenção que este se liga a um de nós por trás do Balanceador de carga.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Balanceador de carga internos e externos

Neste cenário, começa com o Balanceador de carga externo do tipo de nó único existente e adicionar um balanceador de carga interno para o mesmo tipo de nó. Uma porta de back-end ligada a um conjunto de endereços de back-end pode ser atribuída apenas a um balanceador de carga único. Escolha o Balanceador de carga terá as portas de aplicação e o Balanceador de carga terá dos pontos finais de gestão (as portas 19000 e 19080). Se o put os pontos finais de gestão no balanceador de carga interno, tenha em atenção o recurso de Service Fabric restrições de fornecedor abordadas anteriormente no artigo. No exemplo utilizamos, a gestão de pontos finais permanecem no balanceador de carga externo. Pode também adicionar uma porta de aplicação 80 de porta e coloque-a no balanceador de carga interno.

Num cluster de tipo de nó de dois, é um tipo de nó no balanceador de carga externo. O tipo de nó é no balanceador de carga interno. Para utilizar um cluster de tipo de nó de dois no modelo de tipo de nó de dois criados pelo portal (o que vem com balanceadores de carga dois), mude o segundo Balanceador de carga para um balanceador de carga interno. Para obter mais informações, consulte o [Balanceador de carga interno só](#internallb) secção.

1. Adicione o parâmetro de endereço IP de Balanceador de carga interno estático. (Para obter notas relacionadas com a utilização de um endereço IP dinâmico, consulte as secções anteriores deste artigo.)

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Adicione um parâmetro de porta 80 de aplicação.

3. Para adicionar versões internas do existente redes variáveis, copiem e colagem-os e adicione "-Int" para o nome:

    ```
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Se começar com o modelo gerado pelo portal que utiliza a porta 80 da aplicação, o modelo de portal predefinido adiciona AppPort1 (porta 80) no balanceador de carga externo. Neste caso, remova AppPort1 do Balanceador de carga externo `loadBalancingRules` e sondas, pelo que pode adicioná-lo para o Balanceador de carga interno:

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Adicionar uma segunda `Microsoft.Network/loadBalancers` recursos. Aspeto semelhante ao balanceador de carga interno criado no [Balanceador de carga interno só](#internallb) secção, mas utiliza o "-Int" variáveis de Balanceador de carga e implementa apenas a porta de aplicação 80. Esta ação também remove `inboundNatPools`, para manter os pontos finais RDP no balanceador de carga público. Se quiser RDP no balanceador de carga interno, mover `inboundNatPools` do externo o Balanceador de carga para este Balanceador de carga interno:

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. No `networkProfile` para o `Microsoft.Compute/virtualMachineScaleSets` recursos, adicione o conjunto de endereços de back-end interno:

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Implemente o modelo:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Após a implementação, pode ver dois balanceadores de carga no grupo de recursos. Se procurar os balanceadores de carga, pode ver os IP endereço e a gestão de pontos finais públicos (as portas 19000 e 19080) atribuídos para o endereço IP público. Também pode ver o estático interno endereço e a aplicação de ponto final de IP (porta 80) atribuído ao balanceador de carga interno. Ambos os balanceadores de carga usam o conjunto de back-end de conjunto de dimensionamento do mesmo máquina virtual.

## <a name="next-steps"></a>Passos seguintes
[Criar um cluster](service-fabric-cluster-creation-via-arm.md)
