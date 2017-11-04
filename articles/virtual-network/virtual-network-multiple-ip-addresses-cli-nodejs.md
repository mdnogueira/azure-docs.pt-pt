---
title: "VM com o IP de vários endereços utilizando a CLI do Azure 1.0 | Microsoft Docs"
description: "Saiba como atribuir vários endereços IP a uma máquina virtual utilizando a CLI do Azure 1.0 | Gestor de recursos."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
ms.openlocfilehash: 9f085dfa1fe4db36d58cb976bb550a46bf241ac7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Atribuir vários endereços IP para máquinas virtuais utilizando a CLI do Azure 1.0

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) através do modelo de implementação Azure Resource Manager utilizando a CLI do Azure 1.0. Vários endereços IP não não possível atribuir recursos criados através do modelo de implementação clássica. Para saber mais sobre modelos de implementação do Azure, leia o [compreender os modelos de implementação](../resource-manager-deployment-model.md) artigo.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Pode concluir esta tarefa utilizando a CLI do Azure 1.0 (Este artigo) ou o [Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md). Os passos que se seguem explicam como criar um VM de exemplo com vários endereços IP, conforme descrito no cenário. Alterar os nomes de variáveis e tipos de endereços IP conforme necessário para a implementação.

1. Instalar e configurar a CLI do Azure 1.0, seguindo os passos a [instalar e configurar a CLI do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo e iniciar sessão no seu Azure conta com o `azure-login` comando.

2. Criar um grupo de recursos:
    
    ```azurecli
    RgName=myResourceGroup
    Location=westcentralus
    azure group create --name $RgName --location $Location
    ```
3. Criar uma rede virtual:

    ```azurecli
    azure network vnet create --resource-group $RgName --location $Location --name myVNet \
    --address-prefixes 10.0.0.0/16
    ```
4. Crie uma sub-rede na rede virtual:

    ```azurecli
    azure network vnet subnet create --name mySubnet --resource-group $RgName --vnet-name myVNet \
    --address-prefix 10.0.0.0/24
    ```
    
5. Crie uma conta de armazenamento para a VM. Antes de executar o seguinte comando, substitua *mystorageaccount* com um nome exclusivo. O nome tem de ser exclusivo em todo o Azure:

    ```azurecli
    az storage account create --resource-group $RgName --location $Location --name mystorageaccount \
    --kind Storage --sku Standard_LRS
    ```

6. Criar as configurações de IP, um NIC e atribuir as configurações de IP para o NIC. Pode adicionar, remover ou alterar as configurações conforme necessário. As seguintes configurações são descritas no cenário:

    **1 de IPConfig**

    Introduza os comandos que se seguem para criar:

    - Um recurso de endereço IP público com um endereço IP público estático
    - Uma NIC, atribuir o endereço IP público e um endereço IP privado estático para o mesmo.
    
    Substitua *mypublicdns* com um nome exclusivo na localização do Azure.

      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP1 \
      --domain-name-label mypublicdns --allocation-method Static
        
      azure network nic create --resource-group $RgName --location $Location --name myNic1 \
      --private-ip-address 10.0.0.4 --subnet-name mySubnet --subnet-vnet-name myVNet \
      --subnet-name mySubnet --public-ip-name myPublicIP1
      ```

      > [!NOTE]
      > Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereço IP, leia o [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Não há um limite ao número de endereços IP públicos que podem ser utilizadas numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

    **2 de IPConfig**

     Introduza os seguintes comandos para criar um novo recurso de endereço IP público e uma nova configuração de IP com um endereço IP público estático e um endereço IP privado estático:
    
      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP2
      --domain-name-label mypublicdns2 --allocation-method Static

      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --name IPConfig-2
      --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
      ```

    **3 de IPConfig**

    Introduza os seguintes comandos para criar uma configuração de IP com um endereço IP privado estático e nenhum endereço IP público:

      ```azurecli
      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --private-ip-address 10.0.0.6 \
      --name IPConfig-3
      ```

    >[!NOTE] 
    >Embora este artigo atribui todas as configurações de IP para um único NIC, também pode atribuir várias configurações de IP para qualquer NIC numa VM. Para saber como criar uma VM com vários NICs, leia a criar uma VM com vários NICs artigo.

7. Criar uma VM do Linux 

    ```azurecli
    az vm create --resource-group $RgName --name myVM1 --location $Location --nics myNic1 \
    --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --admin-username azureuser
    ```

    Para alterar o tamanho da VM para DS2 padrão v2, por exemplo, adicione simplesmente a seguinte propriedade `--vm-size Standard_DS3_v2` para o `azure vm create` comando no passo 6.

8. Introduza o seguinte comando para ver a NIC e as configurações de IP associadas:

    ```azurecli
    azure network nic show --resource-group $RgName --name myNic1
    ```
9. Adicionar os endereços IP privados para o sistema operativo VM, efetuando os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo.

## <a name="add"></a>Adicionar endereços IP para uma VM

Pode adicionar endereços IP privados e públicos adicionais para uma NIC existente, efetuando os passos que se seguem. Os exemplos são criados após o [cenário](#Scenario) descrito neste artigo.

1. Abra a CLI do Azure e conclua os restantes passos nesta secção dentro de uma única sessão CLI. Se ainda não tiver a CLI do Azure, instalado e configurado, concluir os passos a [instalar e configurar a CLI do Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo e iniciar sessão na sua conta do Azure.

2. Conclua os passos de uma das seguintes secções, com base nos seus requisitos:

    - **Adicionar um endereço IP privado**
    
        Para adicionar um endereço IP privado a um NIC, tem de criar uma configuração de IP utilizando o comando abaixo. O endereço estático tem de ser um endereço não utilizado para a sub-rede.

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 \
        --private-ip-address 10.0.0.7 --name IPConfig-4
        ```
        Crie configurações de tantos conforme for necessário, utilizando nomes de configuração exclusivo e endereços IP privados (para as configurações com endereços IP estáticos).

    - **Adicionar um endereço IP público**
    
        Um endereço IP público é adicionado ao associá-la para uma nova configuração de IP ou uma configuração de IP existente. Conclua os passos de uma das seguintes secções que se seguem, forem necessárias.

        > [!NOTE]
        > Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereço IP, leia o [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Não há um limite ao número de endereços IP públicos que podem ser utilizadas numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).
        >

        **Associe o recurso para uma nova configuração de IP**
    
        Sempre que adicionar um endereço IP público uma nova configuração de IP, também tem de adicionar um endereço IP privado, porque todas as configurações de IP tem de ter um endereço IP privado. Pode adicionar um recurso de endereço IP público existente ou crie um novo. Para criar uma nova, introduza o seguinte comando:

        ```azurecli
        azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 \
        --domain-name-label mypublicdns3
        ```

        Para criar uma nova configuração de IP com um endereço IP privado estático e associada *myPublicIP3* IP público endereços de recursos, introduza o seguinte comando:

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 \
        --private-ip-address 10.0.0.8 --public-ip-name myPublicIP3
        ```

        **Associe o recurso a uma configuração de IP existente**

        Só pode ser associado a uma configuração de IP que já não tem um associado um recurso de endereço IP público. Pode determinar se uma configuração de IP tem um endereço IP público associado ao introduzir o seguinte comando:

        ```azurecli
        azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
        ```

        Procure uma linha semelhante à seguinte para IPConfig 3 na saída devolvida:

        ```         
        Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet
        ```
          
        Uma vez que o **IP público** coluna para *IpConfig 3* está em branco, nenhum recurso de endereço IP público do está atualmente associado a ele. Pode adicionar um recurso de endereço IP público existente para IpConfig 3, ou introduza o seguinte comando para criar um:

        ```azurecli
        azure network public-ip create --resource-group  myResourceGroup --location westcentralus \
        --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
        ```

        Introduza o seguinte comando para associar o recurso de endereço IP público à configuração de IP existente com o nome *IPConfig 3*:
        ```azurecli
        azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 \
        --public-ip-name myPublicIP3
        ```

3. Ver os endereços IP privados e os recursos de endereço IP públicos atribuídos para o NIC introduzindo o seguinte comando:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

      O resultado devolvido é semelhante ao seguinte:
      ```
      Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        
      default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
      IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
      IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet  myPublicIP3
      ```
4. Adicionar os endereços IP privados adicionadas para o NIC para o sistema de operativo VM ao seguir as instruções no [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo. Adicione os endereços IP públicos para o sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
