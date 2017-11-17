---
title: "Criar uma VM com um endereço IP público estático - CLI do Azure | Microsoft Docs"
description: "Saiba como criar uma VM com um endereço IP público estático, utilizar a interface de linha de comandos do Azure (CLI)."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c50f685745a645b5fbe383a5fe4726faa0e36345
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli"></a>Criar uma VM com um endereço IP público estático, utilizar a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI do Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modelo](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (Clássico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo abrange utilizando o modelo de implementação Resource Manager, que a Microsoft recomenda-se para a maioria das implementações novas em vez do modelo de implementação clássica.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Criar a VM

Os valores existentes na "" para as variáveis nos passos que se seguem criar recursos com as definições do cenário. Altere os valores, conforme adequado, para o seu ambiente.

1. Instalar o [Azure CLI 2.0](/cli/azure/install-az-cli2) se ainda não o tiver instalado.
2. Criar um par de chaves público e privado SSH para VMs com Linux, efetuando os passos a [criar um par de chaves público e privado SSH para VMs com Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. A partir de uma shell de comandos, inicie sessão com o comando `az login`.
4. Crie a VM ao executar o script que se segue num computador Linux ou Mac. O endereço IP público do Azure, a rede virtual, a interface de rede e recursos da VM têm de existir na mesma localização. Apesar dos recursos não tem existir no mesmo grupo de recursos, o script seguinte fazem.

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
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
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

Para além de criar uma VM, cria o script:
- Um único premium gerida disco por predefinição, mas tiver outras opções para o tipo de disco, que pode criar. Leia o [criar uma VM com Linux utilizando o 2.0 CLI do Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo para obter detalhes.
- Rede virtual, uma sub-rede, NIC e recursos do endereço IP público. Em alternativa, pode utilizar *existente* rede virtual, sub-rede, NIC ou recursos do endereço IP público. Para saber como utilizar os recursos de rede existentes em vez de criação dos recursos adicional, introduza `az vm create -h`.

## <a name = "validate"></a>Validar a criação da VM e endereço IP público

1. Introduza o comando `az resource list --resouce-group IaaSStory --output table` para ver uma lista de recursos criados pelo script. Deve ser cinco recursos na saída devolvida: interface, o disco, o endereço IP público, a rede virtual e uma máquina virtual de rede.
2. Introduza o comando `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. No resultado devolvido, tenha em atenção o valor de **IpAddress** e de que o valor de **PublicIpAllocationMethod** é *estático*.
3. Antes de executar o seguinte comando, remova o <>, substitua *Username* com o nome utilizado para o **Username** variáveis no script e substituir *ipAddress* com o **ipAddress** do passo anterior. Execute o seguinte comando para ligar à VM: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Remova a VM e os recursos associados

Recomenda-se que elimina os recursos criados neste exercício, se não utilizá-los em produção. VM, endereço IP público e recursos de disco de pagar, desde que está aprovisionado. Para remover os recursos criados durante este exercício, conclua os seguintes passos:

1. Para ver os recursos no grupo de recursos, execute o `az resource list --resource-group IaaSStory` comando.
2. Confirme que existem não existem recursos no grupo de recursos, que não sejam os recursos criados pelo script neste artigo. 
3. Para eliminar todos os recursos criados neste exercício, execute o `az group delete -n IaaSStory` comando. O comando elimina o grupo de recursos e todos os recursos que nele contidos.

## <a name="next-steps"></a>Passos seguintes

Qualquer tráfego de rede possam circular para e da VM criada neste artigo. Pode definir a entrada e saídas regras dentro de um NSG limitam o tráfego que possam circular para e da interface de rede, a sub-rede ou ambos. Para saber mais sobre NSGs, leia o [descrição geral do NSG](virtual-networks-nsg.md) artigo.