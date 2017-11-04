---
title: Criar imagens VM personalizadas com o Azure PowerShell | Microsoft Docs
description: Tutorial - criar uma imagem VM personalizada com o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: cee283268057a407003a38f8db5af8cac151439f
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Criar uma imagem personalizada da VM do Azure com o PowerShell

São imagens personalizadas, como imagens do marketplace, mas que são criados por si. Imagens personalizadas podem ser utilizadas para configurações de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações de SO. Neste tutorial, vai criar sua imagem personalizada de uma máquina virtual do Azure. Saiba como:

> [!div class="checklist"]
> * Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Lista todas as imagens na sua subscrição
> * Eliminar uma imagem

Este tutorial requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar ` Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Antes de começar

Como efetuar uma VM existente e ativá-la para uma imagem personalizada reutilizável que pode utilizar para criar novas instâncias VM de detalhe os passos abaixo.

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se for necessário, isto [script de exemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar uma por si. Quando trabalhar com o tutorial, substitua o grupo de recursos e VM nomes sempre que necessário.

## <a name="prepare-vm"></a>Preparar a VM

Para criar uma imagem de uma máquina virtual, terá de preparar a VM ao generalizar a VM, Desalocação e, em seguida, marcar a origem de VM como generalizado no Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalize a VM do Windows com o Sysprep

Sysprep remove todas as informações pessoais da conta, entre outras coisas e prepara a máquina para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [como Sysprep de utilização: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).


1. Ligar à máquina virtual.
2. Abra a janela de linha de comandos como administrador. Altere o diretório para *%windir%\system32\sysprep*e, em seguida, execute *sysprep.exe*.
3. No **ferramenta de preparação de sistema** caixa de diálogo, selecione *introduza sistema Out-of-Box experiência (OOBE)*e certifique-se de que o *Generalize* caixa de verificação está selecionada.
4. No **as opções de encerramento**, selecione *encerramento* e, em seguida, clique em **OK**.
5. Quando tiver concluído o Sysprep, encerrar a máquina virtual. **Não reiniciar a VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desalocar e marcar a VM como generalizado

Para criar uma imagem, a VM tem de ser anulada e marcada como generalizado no Azure.

Desalocar VM utilizando [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Definir o estado da máquina virtual para `-Generalized` utilizando [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Criar a imagem

Agora pode criar uma imagem da VM utilizando [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) e [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). O exemplo seguinte cria uma imagem com o nome *myImage* de uma VM chamada *myVM*.

Coloque a máquina virtual. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Crie a configuração de imagem.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Crie a imagem.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Criar as VMs a partir da imagem

Agora que tem uma imagem, pode criar uma ou mais VMs novas partir da imagem. Criar uma VM a partir de uma imagem personalizada é muito semelhante à criação de uma VM com uma imagem do Marketplace. Quando utiliza uma imagem do Marketplace, terá de fornecer as informações sobre a imagem, o fornecedor de imagem, oferta, SKU e versão. Com uma imagem personalizada, apenas terá de fornecer o ID do recurso de imagem personalizada. 

O script seguinte, iremos criar uma variável *$image* para armazenar informações sobre como utilizar a imagem personalizada [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) e, em seguida, utilizamos [conjunto AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) e especifique o ID utilizando o *$image* variável acabamos de criar. 

O script cria uma VM chamada *myVMfromImage* do nosso imagem personalizada num novo grupo de recursos denominado *myResourceGroupFromImage* no *EUA oeste* localização.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

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
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="image-management"></a>Gestão de imagens 

Seguem-se alguns exemplos de tarefas de imagem de gestão comuns e como conclui-los através do PowerShell.

Liste todas as imagens de por nome.

```powershell
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Elimine uma imagem. Neste exemplo elimina a imagem com o nome *myOldImage* do *myResourceGroup*.

```powershell
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, vai criar uma imagem VM personalizada. Aprendeu a:

> [!div class="checklist"]
> * Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Lista todas as imagens na sua subscrição
> * Eliminar uma imagem

Avançar para o próximo tutorial para saber mais sobre como elevadas máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar VMs de elevada disponibilidade](tutorial-availability-sets.md)



