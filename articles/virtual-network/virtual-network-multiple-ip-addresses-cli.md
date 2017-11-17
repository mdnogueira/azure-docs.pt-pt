---
title: "VM com o IP de vários endereços utilizando a CLI do Azure | Microsoft Docs"
description: "Saiba como atribuir vários endereços IP a uma máquina virtual utilizando a interface de linha de comandos do Azure (CLI)."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: jimdial
ms.openlocfilehash: aa0f84299dcb4800cd332d8276785f6b08152060
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Atribuir vários endereços IP para máquinas virtuais utilizando a CLI do Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) através do modelo de implementação Azure Resource Manager utilizando a CLI do Azure. Vários endereços IP não não possível atribuir recursos criados através do modelo de implementação clássica. Para saber mais sobre modelos de implementação do Azure, leia o [compreender os modelos de implementação](../resource-manager-deployment-model.md) artigo.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Os passos que se seguem explicam como criar uma máquina virtual de exemplo com vários endereços IP, conforme descrito no cenário. Alterar valores das variáveis no "" e tipos de endereços IP, conforme necessário, para a implementação. 

1. Instalar o [Azure CLI 2.0](/cli/azure/install-az-cli2) se ainda não o tiver instalado.
2. Criar um par de chaves público e privado SSH para VMs com Linux, efetuando os passos a [criar um par de chaves público e privado SSH para VMs com Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. A partir de uma shell de comandos, inicie sessão com o comando `az login` e selecione a subscrição que está a utilizar.
4. Crie a VM ao executar o script que se segue num computador Linux ou Mac. O script cria um grupo de recursos, uma rede virtual (VNet), um NIC com três configurações de IP e uma VM com dois NICs anexados ao mesmo. O NIC, o endereço IP público, a rede virtual e a recursos da VM têm de existir na mesma localização e subscrição. Apesar dos recursos não tem existir no mesmo grupo de recursos, o script seguinte fazem.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP adresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

azure network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes rticle. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location estcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the utput returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Para além de criar uma VM com um NIC com 3 configurações de IP, o script cria:

- Um único premium gerida disco por predefinição, mas tiver outras opções para o tipo de disco, que pode criar. Leia o [criar uma VM com Linux utilizando o 2.0 CLI do Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo para obter detalhes.
- Uma rede virtual com uma sub-rede e dois endereços IP públicos. Em alternativa, pode utilizar *existente* rede virtual, sub-rede, NIC ou recursos do endereço IP público. Para saber como utilizar os recursos de rede existentes em vez de criação dos recursos adicional, introduza `az vm create -h`.

Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereço IP, leia o [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Não há um limite ao número de endereços IP públicos que podem ser utilizadas numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

Depois de criada a VM, introduza o `az network nic show --name MyNic1 --resource-group myResourceGroup` comando para ver a configuração de NIC. Introduza o `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` para ver uma lista das configurações de IP associados para o NIC.

Adicionar os endereços IP privados para o sistema operativo VM, efetuando os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo.

## <a name="add"></a>Adicionar endereços IP para uma VM

Pode adicionar endereços IP privados e públicos adicionais para uma NIC existente, efetuando os passos que se seguem. Os exemplos são criados após o [cenário](#Scenario) descrito neste artigo.

1. Abra uma shell de comandos e conclua os restantes passos nesta secção dentro de uma única sessão. Se ainda não tiver a CLI do Azure, instalado e configurado, concluir os passos a [instalação Azure CLI 2.0](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo e início de sessão para o seu Azure conta com o `az-login` comando.

2. Conclua os passos de uma das seguintes secções, com base nos seus requisitos:

    **Adicionar um endereço IP privado**
    
    Para adicionar um endereço IP privado a um NIC, tem de criar uma configuração de IP utilizando o comando que se segue. O endereço IP estático tem de ser um endereço não utilizado para a sub-rede.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Crie configurações de tantos conforme for necessário, utilizando nomes de configuração exclusivo e endereços IP privados (para as configurações com endereços IP estáticos).

    **Adicionar um endereço IP público**
    
    Um endereço IP público é adicionado ao associá-la para uma nova configuração de IP ou uma configuração de IP existente. Conclua os passos de uma das seguintes secções que se seguem, forem necessárias.

    Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereço IP, leia o [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Não há um limite ao número de endereços IP públicos que podem ser utilizadas numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

    - **Associe o recurso para uma nova configuração de IP**
    
        Sempre que adicionar um endereço IP público uma nova configuração de IP, também tem de adicionar um endereço IP privado, porque todas as configurações de IP tem de ter um endereço IP privado. Pode adicionar um recurso de endereço IP público existente ou crie um novo. Para criar uma nova, introduza o seguinte comando:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Para criar uma nova configuração de IP com um endereço IP privado estático e associada *myPublicIP3* IP público endereços de recursos, introduza o seguinte comando:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Associe o recurso a uma configuração de IP existente** só pode ser associado a uma configuração de IP que já não tem um associado a um recurso de endereço IP público. Pode determinar se uma configuração de IP tem um endereço IP público associado ao introduzir o seguinte comando:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Devolveu saída:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Uma vez que o **PublicIpAddressId** coluna *IpConfig 3* está em branco no resultado, nenhum recurso de endereço IP público do está atualmente associado a ele. Pode adicionar um recurso de endereço IP público existente para IpConfig 3, ou introduza o seguinte comando para criar um:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Introduza o seguinte comando para associar o recurso de endereço IP público à configuração de IP existente com o nome *IPConfig 3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Ver os endereços IP privados e o recurso de endereço IP público Ids atribuídas a NIC, introduzindo o seguinte comando:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Devolveu saída: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Adicionar os endereços IP privados adicionadas para o NIC para o sistema de operativo VM ao seguir as instruções no [endereços IP de adicionar a um sistema de operativo VM](#os-config) secção deste artigo. Adicione os endereços IP públicos para o sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
