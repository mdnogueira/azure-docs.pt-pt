---
title: Controlar os dispositivos de encaminhamento e virtuais utilizando a CLI do Azure 1.0 | Microsoft Docs
description: Saiba como controlar os dispositivos de encaminhamento e virtuais utilizando a CLI do Azure 1.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2017
ms.author: jdial
ms.openlocfilehash: 5f21bc7a4fcd9507ea9d6b2b752a2328a7b834f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-10"></a>Criar rotas definidas pelo utilizador (UDR) utilizando a CLI do Azure 1.0

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [CLI do Azure](virtual-network-create-udr-arm-cli.md)
> * [Modelo](virtual-network-create-udr-arm-template.md)
> * [PowerShell (Clássico)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Clássica)](virtual-network-create-udr-classic-cli.md)

Crie aplicações virtuais utilizando a CLI do Azure e de encaminhamento personalizado.

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa 

Pode concluir a tarefa utilizando uma das seguintes versões CLI: 

- [Azure CLI 1.0](#Create-the-UDR-for-the-front-end-subnet) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](virtual-network-create-udr-arm-cli.md) - CLI de próxima geração para o modelo de implementação de gestão de recursos 


[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os comandos da CLI do Azure de exemplo abaixo esperam num ambiente simple já criado com base no cenário acima. Se pretender executar os comandos, como são apresentados neste documento, criar primeiro o ambiente de teste ao implementar [este modelo](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), clique em **implementar no Azure**, substitua os valores de parâmetro predefinidos se necessário e siga as instruções no portal.


## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar UDR para a sub-rede do front-end
Para criar a tabela de rota e a rota necessários para a sub-rede do front-end com base no cenário acima, siga os passos abaixo.

1. Execute o seguinte comando para criar uma tabela de rota para a sub-rede do front-end:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest
    ```
   
    Saída:
   
        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK
   
    Parâmetros:
   
   * **-g (ou --resource-group)**. Nome do grupo de recursos onde será criado o UDR. Para o nosso cenário *TestRG*.
   * **-l (ou --location)**. Região do Azure onde será criada a nova UDR. Para o nosso cenário *westus*.
   * **-n (ou --name)**. Nome para o novo UDR. Para o nosso cenário *UDR-front-end*.
2. Execute o seguinte comando para criar uma rota na tabela de rota para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para o **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4
    ```
   
    Saída:
   
        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK
   
    Parâmetros:
   
   * **-r (ou – nome da tabela de rota)**. Nome da tabela de rotas onde será adicionada a rota. Para o nosso cenário *UDR-front-end*.
   * **-a (or --address-prefix)**. Prefixo de endereço para a sub-rede onde os pacotes são destinados à. Para o nosso cenário *192.168.2.0/24*.
   * **-y (ou --tipo de salto seguinte)**. Tipo de objeto tráfego será enviado para. Os valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*, ou *nenhum*.
   * **-p (– seguinte-hop-endereço ip ou -**). Endereço IP de próximo salto. Para o nosso cenário *192.168.0.4*.
3. Execute o seguinte comando para associar a tabela de rotas criada acima com o **front-end** sub-rede:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Saída:
   
        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK
   
    Parâmetros:
   
   * **-e (ou -- vnet-name)**. Nome da VNet onde a sub-rede está localizada. Para o nosso cenário *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar UDR para a sub-rede de back-end
Para criar a tabela de rota e a rota necessários para a sub-rede de back-end com base no cenário acima, conclua os seguintes passos:

1. Execute o seguinte comando para criar uma tabela de rota para a sub-rede de back-end:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-BackEnd -l westus
    ```

2. Execute o seguinte comando para criar uma rota na tabela de rota para enviar todo o tráfego destinado à sub-rede front-end (192.168.1.0/24) para o **FW1** VM (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4
    ```

3. Execute o seguinte comando para associar a tabela de rotas com o **back-end** sub-rede:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Ativar o reencaminhamento IP no FW1
Para ativar o reencaminhamento IP no NIC utilizada pelo **FW1**, conclua os seguintes passos:

1. Execute o comando que se segue e tenha em atenção o valor para **reencaminhamento IP ativar**. Deve ser definido como *falso*.

    ```azurecli
    azure network nic show -g TestRG -n NICFW1
    ```

    Saída:
   
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK
2. Execute o seguinte comando para ativar o reencaminhamento IP:

    ```azurecli
    azure network nic set -g TestRG -n NICFW1 -f true
    ```
   
    Saída:
   
        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK
   
    Parâmetros:
   
   * **-f (ou - reencaminhamento de ip de ativação)**. *Verdadeiro* ou *falso*.

