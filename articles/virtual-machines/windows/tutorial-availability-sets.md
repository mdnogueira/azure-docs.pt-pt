---
title: Tutorial de conjuntos de disponibilidade para as VMs do Windows no Azure | Microsoft Docs
description: Saiba mais sobre os conjuntos de disponibilidade para as VMs do Windows no Azure.
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2345b434a51b768793c2dea4587dc0a49ab35b70
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-use-availability-sets"></a>Como utilizar os conjuntos de disponibilidade

Neste tutorial, irá aprender a aumentar a disponibilidade e fiabilidade das suas soluções de Máquina Virtual no Azure através de uma capacidade chamada conjuntos de disponibilidade. Conjuntos de disponibilidade Certifique-se de que as VMs que implementa no Azure são distribuídas por vários nós de hardware isolado num cluster. Fazer isto garante que se ocorre uma falha de hardware ou software no Azure, apenas um conjunto das suas VMs secundárias são afetados e de que a solução global permanece disponível e operacional. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verifique os tamanhos VM disponíveis
> * Verifique o Advisor do Azure

Este tutorial requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).

## <a name="availability-set-overview"></a>Descrição geral do conjunto de disponibilidade

Um conjunto de disponibilidade é uma capacidade de agrupamento lógico que pode utilizar no Azure para garantir que os recursos da VM, colocar dentro da mesma estão isolados umas das outras quando estão implementadas dentro de um datacenter do Azure. Azure garante que as VMs colocar dentro de um conjunto de disponibilidade sejam executadas em vários servidores físicos, computação bastidores, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de software do Azure ou de hardware, apenas um subconjunto das suas VMs são afetados e global da sua aplicação permanece cópias de segurança e continua a estar disponíveis para os seus clientes. Conjuntos de disponibilidade são uma capacidade essencial quando pretender criar soluções de nuvem fiável.

Vejamos uma típica VM solução baseada em que poderá ter 4 servidores de web de front-end e utilize 2 VMs de back-end que aloja uma base de dados. Com o Azure, iria querer definir dois conjuntos de disponibilidade antes de implementar as suas VMs: um conjunto de disponibilidade para a camada web e um conjunto de disponibilidade para a camada de base de dados. Quando cria uma nova VM, em seguida, pode especificar a conjunto de disponibilidade como um parâmetro para a vm az criar comando e Azure automaticamente assegura que as VMs que cria no conjunto disponível são isoladas entre vários recursos de hardware físico. Se o hardware físico que o servidor Web ou VMs do servidor de base de dados está em execução no tem um problema, sabe que as outras instâncias do servidor Web e as VMs de base de dados permanecem em execução por estarem em hardware diferente.

Utilize conjuntos de disponibilidade quando pretender implementar as soluções baseadas na VM fiáveis, no Azure.

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Pode criar um conjunto através de disponibilidade [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Neste exemplo, vamos definir ambos os o número de domínios de atualização e de falhas em *2* para o conjunto nomeada de disponibilidade *myAvailabilitySet* no *myResourceGroupAvailability* grupo de recursos.

Crie um grupo de recursos.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Criar uma conjunto através de disponibilidade gerida [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) com o **- sku alinhado** parâmetro.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Criar VMs dentro de um conjunto de disponibilidade

As VMs têm de ser criadas dentro da conjunto de disponibilidade para Certifique-se que corretamente estão distribuídos o hardware. Não é possível adicionar uma VM existente para um conjunto depois de criado de disponibilidade. 

O hardware numa localização está dividido em vários domínios de atualização e domínios de falhas. Um **domínio de atualização** é um grupo de VMs e hardware físico subjacente que pode ser reiniciado ao mesmo tempo. VMs na mesma **domínio de falhas** partilhar armazenamento comuns, bem como um comutador de rede de origem e de energia comum. 

Quando cria uma configuração de VM utilizando [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) utiliza o `-AvailabilitySetId` parâmetro para especificar o ID do conjunto de disponibilidade.

Criar duas VMs com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) no conjunto de disponibilidade.

```azurepowershell-interactive
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig
    
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup `
    -Location eastus `
    -Name myNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAvailability `
    -SecurityRules $nsgRuleRDP
    
# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name mySubnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -ComputerName myVM$i `
        -Credential $cred `
        -VM $vm `
        -Windows `
        -EnableAutoUpdate `
        -ProvisionVMAgent
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

Demora alguns minutos para criar e configurar ambas as VMs. Quando terminar, tem duas máquinas virtuais, distribuídas o hardware subjacente. 

Se observar a conjunto de disponibilidade no portal, acedendo a grupos de recursos > myResourceGroupAvailability > myAvailabilitySet, deverá ver a forma como as VMs estão distribuídas a falha de 2 e domínios de atualização.

![Conjunto de disponibilidade no portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Verifique a existência de tamanhos VM disponíveis 

Pode adicionar mais VMs para o conjunto mais tarde de disponibilidade, mas é necessário saber quais os tamanhos de VM estão disponíveis no hardware. Utilize [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) para listar todos os tamanhos disponíveis no hardware de cluster para o conjunto de disponibilidade.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="check-azure-advisor"></a>Verifique o Advisor do Azure 

Também pode utilizar o Advisor do Azure para obter mais informações sobre formas de melhorar a disponibilidade das suas VMs. Azure Advisor ajuda-o a seguir as melhores práticas para otimizar as implementações do Azure. Este analisa a telemetria de utilização e configuração do recurso e, em seguida, recomenda soluções que podem ajudar a melhorar a eficácia de custo, desempenho, elevada disponibilidade e segurança dos seus recursos Azure.

Iniciar sessão para o [portal do Azure](https://portal.azure.com), selecione **mais serviços**e escreva **Advisor**. O dashboard do Advisor apresenta personalizadas recomendações para a subscrição selecionada. Para obter mais informações, consulte [introdução ao Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verifique os tamanhos VM disponíveis
> * Verifique o Advisor do Azure

Avançar para o próximo tutorial para saber mais sobre os conjuntos de dimensionamento de máquina virtual.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento de VM](tutorial-create-vmss.md)


