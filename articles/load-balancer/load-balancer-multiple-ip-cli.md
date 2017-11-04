---
title: "Várias configurações de IP utilizando a CLI do Azure de balanceamento de carga | Microsoft Docs"
description: "Saiba como atribuir vários endereços IP a uma máquina virtual utilizando a CLI do Azure | Gestor de recursos."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 1d88c53784cec302f5e67b9d50f84780bbec37db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="load-balancing-on-multiple-ip-configurations"></a>Várias configurações de IP de balanceamento de carga

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Este artigo descreve como utilizar o Balanceador de carga do Azure com vários endereços IP numa interface de rede secundária (NIC). Para este cenário, temos duas VMs com Windows, cada um com um site primário e um NIC secundário. Cada uma dos NICs secundários tem duas configurações de IP. Cada VM aloja contoso.com de Web sites e fabrikam.com. Cada Web site está vinculado a uma das configurações de IP no NIC secundário. Utilizamos o Balanceador de carga do Azure para expor dois endereços IP de front-end, um para cada Web site, distribuir o tráfego para a respetiva configuração de IP para o Web site. Este cenário utiliza o mesmo número de porta entre ambos frontends, bem como ambos os endereços IP do conjunto de back-end.

![Imagem de cenário LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Passos para carregar saldo em várias configurações de IP

Para alcançar o cenário descrito neste artigo, conclua os seguintes passos:

1. [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md) seguindo os passos no artigo ligado e inicie a sua conta do Azure.
2. [Criar um grupo de recursos](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) chamado *contosofabrikam* da seguinte forma:

    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. [Criar um conjunto de disponibilidade](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) para as duas VMs. Para este cenário, utilize o seguinte comando:

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Criar uma rede virtual](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) chamado *myVNet* e uma sub-rede denominada *mySubnet*:

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. [Criar o Balanceador de carga](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) chamado *mylb*:

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Crie dois endereços IP públicos dinâmicos para as configurações de IP de front-end de Balanceador de carga:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Criar as duas configurações de IP de front-end, *contosofe* e *fabrikamfe* respetivamente:

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Criar conjuntos de endereços - de back-end *contosopool* e *fabrikampool*, um [sonda](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*, e balanceamento regras - *HTTPc* e *HTTPf*:

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Verifique a saída para [verificar o seu Balanceador de carga](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) foi criado corretamente executando o seguinte comando:

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Criar um IP público](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp*, e [conta de armazenamento](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* para a sua primeira máquina virtual VM1 da seguinte forma:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Criar as interfaces de rede](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) para VM1 e adicionar uma segunda configuração de IP, *VM1 ipconfig2*, e [criar a VM](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms) da seguinte forma:

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Repita os passos 10 11 para a VM segundo:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Por fim, tem de configurar os registos de recursos DNS para apontar para o endereço IP do respetivo front-end do Balanceador de carga. Pode alojar o seus domínios no DNS do Azure. Para obter mais informações sobre como utilizar o DNS do Azure com o Balanceador de carga, consulte [utilizando o DNS do Azure com outros serviços do Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como combinar serviços no Azure de balanceamento de carga [utilizando os serviços de balanceamento de carga no Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Saiba como pode utilizar diferentes tipos de registos no Azure para gerir e resolver problemas de Balanceador de carga no [análise de registo para o Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
