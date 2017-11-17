---
title: Criar uma rede virtual - CLI do Azure | Microsoft Docs
description: Saiba como criar uma rede virtual com a CLI do Azure.
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 659a791124eab002290ac0b7f0898cf1c06c2951
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Criar uma rede virtual com a CLI do Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

O Azure tem dois modelos de implementação: a implementação do Azure Resource Manager e a implementação clássica. A Microsoft recomenda a criação de recursos com o modelo de implementação Resource Manager. Para saber mais sobre as diferenças entre os dois modelos, veja o artigo [Understand Azure deployment models (Compreender os modelos de implementação do Azure)](../azure-resource-manager/resource-manager-deployment-model.md).

Também pode criar uma rede virtual através do Resource Manager utilizando outras ferramentas ou criar uma rede virtual através do modelo de implementação clássica, selecionando uma opção diferente na lista seguinte:

> [!div class="op_single_selector"]
> * [Portal](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [Modelo](virtual-networks-create-vnet-arm-template-click.md)
> * [Portal (Clássico)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (Clássico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [CLI (Clássica)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Para criar uma rede virtual com a CLI do Azure, execute os seguintes passos:

1. Instalar e configurar a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) e início de sessão para um Azure conta através de [início de sessão az](/cli/azure/#login).

2. Criar um grupo de recursos para a sua VNet utilizando o [criar grupo az](/cli/azure/group#create) comando com o `--name` e `--location` argumentos:

    ```azurecli
    az group create --name TestRG --location centralus
    ```

3. Crie uma VNet e uma sub-rede:

    ```azurecli
    az network vnet create \
    --name TestVNet \
    --resource-group TestRG \
    --location centralus \
    --address-prefix 192.168.0.0/16 \
    --subnet-name FrontEnd \
    --subnet-prefix 192.168.1.0/24
    ```

    Resultado esperado:
    
    ```json
    {
        "newVNet": {
            "addressSpace": {
            "addressPrefixes": [
            "192.168.0.0/16"
            ]
            },
            "dhcpOptions": {
            "dnsServers": []
            },
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>",
            "subnets": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                "name": "FrontEnd",
                "properties": {
                "addressPrefix": "192.168.1.0/24",
                "provisioningState": "Succeeded"
                },
                "resourceGroup": "TestRG"
            }
            ]
            }
    }
    ```

    Parâmetros utilizados:

    - `--name TestVNet`: Nome da VNet a ser criado.
    - `--resource-group TestRG`: O nome do grupo de recursos que controla o recurso de #. 
    - `--location centralus`: A localização na qual pretende implementar.
    - `--address-prefix 192.168.0.0/16`: O prefixo de endereço e a bloquear.  
    - `--subnet-name FrontEnd`: O nome da sub-rede.
    - `--subnet-prefix 192.168.1.0/24`: O prefixo de endereço e a bloquear.

    Para listar as informações básicas a utilizar no comando seguinte, pode consultar a VNet utilizando um [filtro de consulta](/cli/azure/query-az-cli2):

    ```azurecli
    az network vnet list --query '[?name==`TestVNet`].{Where:location,Name:name,Group:resourceGroup}' -o table
    ```

    Que produz a seguinte saída:

        Where      Name      Group

        centralus  TestVNet  TestRG

4. Crie uma sub-rede:

    ```azurecli
    az network vnet subnet create \
    --address-prefix 192.168.2.0/24 \
    --name BackEnd \
    --resource-group TestRG \
    --vnet-name TestVNet
    ```

    Resultado esperado:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid> \"",
    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "TestRG",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

    Parâmetros utilizados:

    - `--address-prefix 192.168.2.0/24`: Bloco CIDR da sub-rede.
    - `--name BackEnd`: Nome da nova sub-rede.
    - `--resource-group TestRG`: O grupo de recursos.
    - `--vnet-name TestVNet`: O nome da VNet proprietário.

5. Consulta as propriedades da nova VNet:

    ```azurecli
    az network vnet show \
    -g TestRG \
    -n TestVNet \
    --query '{Name:name,Where:location,Group:resourceGroup,Status:provisioningState,SubnetCount:subnets | length(@)}' \
    -o table
    ```

    Resultado esperado:

        Name      Where      Group    Status       SubnetCount

        TestVNet  centralus  TestRG   Succeeded              2

6. Consulta as propriedades das sub-redes:

    ```azurecli
    az network vnet subnet list \
    -g TestRG \
    --vnet-name testvnet \
    --query '[].{Name:name,CIDR:addressPrefix,Status:provisioningState}' \
    -o table
    ```

    Resultado esperado:

        Name      CIDR            Status

        FrontEnd  192.168.1.0/24  Succeeded
        BackEnd   192.168.2.0/24  Succeeded

## <a name="next-steps"></a>Passos seguintes

Saiba como ligar:

- Uma máquina virtual (VM) a uma rede virtual ao ler o [criar uma VM com Linux](../virtual-machines/linux/quick-create-cli.md) artigo. Em vez de criar um VNet e uma sub-rede nos passos dos artigos, pode selecionar uma VNet e uma sub-rede existentes às quais ligar uma VM.
- A rede virtual a outras redes virtuais, no artigo [Ligar VNets](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- A rede virtual a uma rede no local através de uma rede privada virtual (VPN) site a site ou de um circuito do ExpressRoute. Saiba como ao ler o [ligar uma VNet a uma rede no local através de uma VPN de site para site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) e [ligar uma VNet a um circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).