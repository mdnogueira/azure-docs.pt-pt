---
title: "Criar e gerir VMs do Windows no Azure que utilizar vários NICs | Microsoft Docs"
description: "Saiba como criar e gerir uma VM do Windows que tenha vários NICs anexados ao mesmo utilizando os modelos do Azure PowerShell ou Gestor de recursos."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 941791ba398a3abbaa5137c36391fd23789cd3b1
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Criar e gerir a máquina virtual do Windows que tenha vários NICs
Máquinas virtuais (VMs) no Azure pode ter vários virtual interface as placas de rede (NICs) ligadas aos mesmos. Um cenário comum é ter diferentes sub-redes para a conectividade de front-end e back-end ou uma rede dedicada para uma solução de monitorização ou cópia de segurança. Este artigo fornece detalhes sobre como criar uma VM que tenha vários NICs anexados ao mesmo. Também irá aprender a adicionar ou remover NICs de VM existente. Diferentes [tamanhos de VM](sizes.md) suportar um número de NICs variando, por isso, tamanho da VM em conformidade.

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que tem o [a versão mais recente do Azure PowerShell instalada e configurada](/powershell/azure/overview).

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem *myResourceGroup*, *myVnet*, e *myVM*.


## <a name="create-a-vm-with-multiple-nics"></a>Criar uma VM com vários NICs
Em primeiro lugar, crie um grupo de recursos. O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *EastUs* localização:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Criar rede virtual e sub-redes
Um cenário comum é para uma rede virtual para ter duas ou mais sub-redes. Pode ser uma sub-rede para o tráfego de front-end, outra para o tráfego de back-end. Para ligar a ambas as sub-redes, é, em seguida, utilizar vários NICs na VM.

1. Definir duas sub-redes da rede virtual com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). O exemplo seguinte define as sub-redes para *mySubnetFrontEnd* e *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Criar a rede virtual e a sub-redes com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O exemplo seguinte cria uma rede virtual denominada *myVnet*:

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Criar vários NICs
Criar dois NICs com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Anexe um NIC para a sub-rede do front-end e um NIC para a sub-rede de back-end. O exemplo seguinte cria NICs com o nome *myNic1* e *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Normalmente, também cria um [grupo de segurança de rede](../../virtual-network/virtual-networks-nsg.md) para filtrar o tráfego de rede para a VM e um [Balanceador de carga](../../load-balancer/load-balancer-overview.md) para distribuir o tráfego entre várias VMs.

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual
Agora começar a criar a configuração de VM. Cada tamanho da VM tem um limite do número total de NICs que pode adicionar a uma VM. Para obter mais informações, consulte [tamanhos de Windows VM](sizes.md).

1. Definir as suas credenciais VM o `$cred` variável da seguinte forma:

    ```powershell
    $cred = Get-Credential
    ```

2. Definir a VM com [novo AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). O exemplo seguinte define uma VM chamada *myVM* e utiliza um tamanho VM que suporte a mais do que dois NICs (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Criar o resto da sua configuração de VM com [conjunto AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) e [conjunto AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). O exemplo seguinte cria uma VM do Windows Server 2016:

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Ligar os dois NICs que criou anteriormente com [adicionar AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Por fim, crie a VM com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

## <a name="add-a-nic-to-an-existing-vm"></a>Adicionar uma NIC para uma VM existente
Para adicionar uma NIC virtual a uma VM existente, a Desalocação da VM, adicione o NIC virtual, em seguida, iniciar a VM. Diferentes [tamanhos de VM](sizes.md) suportar um número de NICs variando, por isso, tamanho da VM em conformidade. Se necessário, pode [redimensionar uma VM](resize-vm.md).

1. Desalocar a VM com [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). O exemplo seguinte deallocates VM com o nome *myVM* no *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obter a configuração existente da VM com [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). O exemplo seguinte obtém as informações para a VM com o nome *myVM* no *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. O exemplo seguinte cria uma NIC virtual com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) denominado *myNic3* que está ligado a *mySubnetBackEnd*. O virtual NIC, em seguida, está ligado à VM com o nome *myVM* no *myResourceGroup* com [adicionar AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>NICs virtuais principais
    Um dos NICs numa VM vários NICs tem de ser principal. Se um do NICs virtual existente na VM já está definido como principal, pode ignorar este passo. O exemplo seguinte assume que estão agora presentes numa VM dois NICs virtuais e que pretende adicionar o primeiro NIC (`[0]`) como principal:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Iniciar a VM com [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

## <a name="remove-a-nic-from-an-existing-vm"></a>Remover um NIC de VM existente
Para remover uma NIC virtual de VM existente, Desalocação da VM, remova o virtual NIC e iniciar a VM.

1. Desalocar a VM com [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). O exemplo seguinte deallocates VM com o nome *myVM* no *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obter a configuração existente da VM com [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). O exemplo seguinte obtém as informações para a VM com o nome *myVM* no *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Obter informações sobre a remoção NIC com [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). O exemplo seguinte obtém as informações *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Remova a NIC com [remover AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) e, em seguida, atualize a VM com [Update-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm). O exemplo a seguir remove *myNic3* tal como obtidas pelo `$nicId` no passo anterior:

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. Iniciar a VM com [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Criar vários NICs com modelos
Modelos Azure Resource Manager fornecem uma forma de criar várias instâncias de um recurso durante a implementação, tais como criar vários NICs. Modelos do Resource Manager utilizam declarativos ficheiros JSON para definir o seu ambiente. Para obter mais informações, consulte [descrição geral do Gestor de recursos do Azure](../../azure-resource-manager/resource-group-overview.md). Pode utilizar *cópia* para especificar o número de instâncias para criar:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Para obter mais informações, consulte [criar várias instâncias utilizando *cópia*](../../resource-group-create-multiple.md). 

Também pode utilizar `copyIndex()` acrescentar um número para um nome de recurso. Em seguida, pode criar *myNic1*, *MyNic2* e assim sucessivamente. O código seguinte mostra um exemplo de acrescentar o valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pode ler um exemplo completo de [criar vários NICs utilizando modelos do Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Passos seguintes
Reveja [tamanhos de Windows VM](sizes.md) quando está a tentar criar uma VM que tenha vários NICs. Preste atenção para o número máximo de NICs que suporte a cada tamanho da VM. 


