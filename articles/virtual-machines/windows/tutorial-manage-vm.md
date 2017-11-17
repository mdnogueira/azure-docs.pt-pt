---
title: "Criar e gerir VMs do Windows com o módulo Azure PowerShell | Microsoft Docs"
description: "Tutorial - criar e gerir VMs do Windows com o módulo Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c612a251105197ab2b46bf448ae39253e5a65f36
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Criar e gerir VMs do Windows com o módulo Azure PowerShell

Máquinas virtuais do Azure fornecem um ambiente informático totalmente configurável e flexível. Este tutorial abrange itens de implementação de máquinas virtuais do Azure básica, tais como selecionar um tamanho VM, selecionar uma imagem de VM e implementar uma VM. Saiba como:

> [!div class="checklist"]
> * Criar e ligar a uma VM
> * Selecione e utilizar imagens da VM
> * Ver e utilizar específicos tamanhos de VM
> * Redimensionar uma VM
> * Visualizar e compreender o estado de VM


[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o módulo do Azure PowerShell versão 3.6 ou posterior. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzureRmAccount` para criar uma ligação com o Azure. 

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual. Neste exemplo, um grupo de recursos denominado *myResourceGroupVM* é criado no *EastUS* região. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

O grupo de recursos é especificado ao criar ou modificar uma VM, que pode ser vista ao longo deste tutorial.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Uma máquina virtual tem de estar ligada a uma rede virtual. Comunicar com a máquina virtual utilizando um endereço IP público através de uma placa de interface de rede.

### <a name="create-virtual-network"></a>Criar a rede virtual

Criar uma sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

Criar uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>Criar endereço IP público

Criar um endereço IP público com [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>Criar o cartão de interface de rede

Criar uma placa de interface de rede com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>Criar o grupo de segurança de rede

Um Azure [grupo de segurança de rede](../../virtual-network/virtual-networks-nsg.md) (NSG) controla o tráfego de entrada e saída para uma ou várias máquinas virtuais. Regras do grupo de segurança de rede permitem ou negam o tráfego de rede de uma porta específica ou um intervalo de portas. Estas regras também podem incluir um prefixo de endereço de origem para que apenas o tráfego com origem numa fonte predefinida possa comunicar com uma máquina virtual. Para aceder ao servidor IIS Web que está a instalar, tem de adicionar uma regra de entrada do NSG.

Para criar uma regra de entrada do NSG, utilize [adicionar AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig). O exemplo seguinte cria uma regra NSG designada *myNSGRule* que abre portas *3389* para a máquina virtual:

```azurepowershell-interactive
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

Criar o NSG utilizando *myNSGRule* com [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

Adicionar o NSG para sub-rede na rede virtual com [conjunto AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

Atualize a rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```azurepowershell-interactive
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Criar a máquina virtual

Ao criar uma máquina virtual, várias opções estão disponíveis, tais como a imagem do sistema operativo, as credenciais administrativas e dimensionamento do disco. Neste exemplo, é criada uma máquina virtual com um nome de *myVM* com a versão mais recente do Centro de dados do Windows Server 2016.

Definir o nome de utilizador e palavra-passe necessária para a conta de administrador na máquina virtual com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Criar a configuração inicial para a máquina virtual com [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig):

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Adicione as informações do sistema operativo à configuração da máquina virtual com [conjunto AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem):

```azurepowershell-interactive
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Adicione as informações de imagem para a configuração de máquina virtual com o [conjunto AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage):

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

Adicionar as definições de disco do sistema operativo para a configuração de máquina virtual com o [conjunto AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk):

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

Adicionar o cartão de interface de rede que criou anteriormente para a configuração de máquina virtual com o [adicionar AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```azurepowershell-interactive
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Criar a máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>Ligar à VM

Depois de a implementação estar concluída, crie uma ligação de ambiente de trabalho remoto à máquina virtual.

Execute os seguintes comandos para devolver o endereço IP público da máquina virtual. Tome nota deste endereço IP, para que se possa ligar ao mesmo com o seu browser e testar a conectividade Web num passo posterior.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

Utilize o seguinte comando na sua máquina virtual para criar uma sessão de ambiente de trabalho remoto com a máquina virtual. Substitua o endereço IP com o *publicIPAddress* da máquina virtual. Quando lhe for pedido, introduza as credenciais utilizadas ao criar a máquina virtual.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>Compreender as imagens VM

No Azure marketplace inclui várias imagens de máquina virtual que podem ser utilizadas para criar uma nova máquina virtual. Nos passos anteriores, uma máquina virtual foi criada utilizando a imagem do Datacenter do Windows Server 2016. Neste passo, o módulo do PowerShell é utilizado para procurar no marketplace para outras imagens do Windows, pode também como base para novas VMs. Este processo é composta por localizar o publicador, oferta e o nome da imagem (Sku). 

Utilize o [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) comando para devolver uma lista de editores de imagem.  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Utilize o [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) para devolver uma lista de ofertas de imagem. Com este comando, a lista devolvida é filtrada no publicador especificado. 

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

O [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) comando, em seguida, irá filtrar no nome do publicador e oferta para devolver uma lista de nomes de imagem.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Estas informações podem ser utilizadas para implementar uma VM com uma imagem específica. Neste exemplo define o nome de imagem no objeto de VM. Consulte os exemplos anteriores neste tutorial para os passos de implementação concluída.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>Compreender os tamanhos de VM

Um tamanho de máquina virtual determina a quantidade de recursos de computação, tais como CPU, memória e GPU que ficam disponíveis para a máquina virtual. Máquinas virtuais têm de ser criado com um tamanho adequado para a carga de trabalho esperado. Se a carga de trabalho aumenta, podem ser redimensionada uma máquina virtual existente.

### <a name="vm-sizes"></a>Tamanhos de VM

A tabela seguinte categoriza tamanhos em casos de utilização.  

| Tipo                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Fins gerais         |Série DSv2, Dv2, DS, D, Av2, A0 7| Equilibrada CPU para memória. Ideal para dev / teste e pequena a médias soluções de aplicações e dados.  |
| Com otimização de computação      | FS, F             | Elevada da CPU para memória. Boa para aplicações de tráfego média, os dispositivos de rede e processos de batch.        |
| Com otimização de memória       | GS, G, série DSv2, DS, Dv2, D   | Elevada memória-para-CPU. Excelente para bases de dados relacionais, caches médias e grandes e análise de memória.                 |
| Com otimização de armazenamento       | Ls                | Débito e E/S de disco elevados. Ideal para bases de dados de Macrodados, SQL e NoSQL.                                                         |
| GPU           | NV, NC            | VMs especializadas visadas para a composição de gráfico pesada e edição de vídeo.       |
| Elevado desempenho | H, A8-11          | A nossa VMs de CPU mais poderosas com interfaces de rede de alto débito opcional (RDMA). 


### <a name="find-available-vm-sizes"></a>Localizar os tamanhos VM disponíveis

Para ver uma lista de tamanhos VM disponíveis numa região específica, utilize o [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) comando.

```azurepowershell-interactive
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>Redimensionar uma VM

Depois de implementar uma VM, pode ser redimensionada para aumentar ou diminuir a alocação de recursos.

Antes de redimensionar uma VM, verifique se o tamanho pretendido está disponível no cluster de VM atual. O [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) comando devolve uma lista de tamanhos. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

Se o tamanho pretendido estiver disponível, a VM pode ser redimensionada partir de um estado ligado no, no entanto, é reiniciado durante a operação.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

Se o tamanho pretendido não está no cluster atual, a VM tem de ser desalocada antes da operação de redimensionamento pode ocorrer. Tenha em atenção que quando a VM seja novamente ativada, quaisquer dados no disco temporário são removidos e alteração de endereços de IP público, a menos que está a ser utilizado um endereço IP estático. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>Estados de energia VM

VM do Azure pode ter um dos vários Estados de energia. Este estado representa o estado atual da VM de ponto de vista do hipervisor. 

### <a name="power-states"></a>Estados de energia

| Estado de energia | Descrição
|----|----|
| A Iniciar | Indica que a máquina virtual está a ser iniciada. |
| Em Execução | Indica que a máquina virtual está em execução. |
| A Parar | Indica se a máquina virtual está a ser parada. | 
| Parada | Indica se a máquina virtual está parada. Tenha em atenção que as máquinas virtuais no estado de paragem ainda implicar custos de computação.  |
| A desalocar | Indica que a máquina virtual está a ser desalocada. |
| Desalocado | Indica que a máquina virtual é completamente removidas do hipervisor, mas continuará disponível no plane controlo. Máquinas virtuais no Estado Deallocated não implicar custos de computação. |
| - | Indica que o estado de energia da máquina virtual é desconhecido. |

### <a name="find-power-state"></a>Determinar o estado de energia

Para obter o estado de uma VM específica, utilize o [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) comando. Lembre-se de que especifique um nome válido para uma máquina virtual e o grupo de recursos. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName myResourceGroupVM `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Saída:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Tarefas de gestão

Durante o ciclo de vida de uma máquina virtual, poderá querer executar tarefas de gestão, tais como iniciar, parar ou eliminar uma máquina virtual. Além disso, pode querer criar scripts para automatizar tarefas repetitivas ou complexas. Com o Azure PowerShell, muitas tarefas comuns de gestão podem ser executadas na linha de comandos ou em scripts.

### <a name="stop-virtual-machine"></a>Parar a máquina virtual

Parar e anular a atribuição de uma máquina virtual com [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

Se pretender manter a máquina virtual num Estado aprovisionado, utilize o parâmetro - StayProvisioned.

### <a name="start-virtual-machine"></a>Iniciar a máquina virtual

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>Eliminar grupo de recursos

Também eliminar um grupo de recursos elimina todos os recursos contidos.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre a criação de VM básica e gestão tais como:

> [!div class="checklist"]
> * Criar e ligar a uma VM
> * Selecione e utilizar imagens da VM
> * Ver e utilizar específicos tamanhos de VM
> * Redimensionar uma VM
> * Visualizar e compreender o estado de VM

Avançar para o próximo tutorial para saber mais sobre os discos VM.  

> [!div class="nextstepaction"]
> [Criar e gerir VM discos](./tutorial-manage-data-disk.md)
