---
title: Criar uma VM do Azure com o PowerShell | Microsoft Docs
description: Utilize o Azure PowerShell e o Azure Resource Manager para criar facilmente uma nova VM com o Windows Server.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 94c18aa0c4fe38fb74931d5ed61fece207c8b5ce
ms.openlocfilehash: 701a5515cd1d52f7ca8d3562dabcdf0e4d31183d


---
# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Criar uma VM do Windows utilizando o Resource Manager e o PowerShell
Este artigo mostra-lhe como criar rapidamente uma Máquina Virtual do Azure a executar o Windows Server e os recursos de que necessita para utilizar o [Resource Manager](../azure-resource-manager/resource-group-overview.md) e o PowerShell. 

Todos os passos neste artigo são necessários para criar uma máquina virtual e deve demorar cerca de 30 minutos para os efetuar. Substitua valores de parâmetros de exemplo nos comandos com nomes que fazem sentido no seu ambiente.

## <a name="step-1-install-azure-powershell"></a>Passo 1: instalar o Azure PowerShell
Veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azureps-cmdlets-docs) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a subscrição que quer utilizar e iniciar sessão na sua conta do Azure.

## <a name="step-2-create-a-resource-group"></a>Passo 2: criar um grupo de recursos
Todos os recursos tem de estar contidos num grupo de recursos, pelo que vamos criá-lo primeiro.  

1. Obtenha uma lista de localizações disponíveis onde os recursos podem ser criados.
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```
2. Defina a localização para os recursos. Este comando define a localização para **centralus**.
   
    ```powershell
    $location = "centralus"
    ```
3. Crie um grupo de recursos. Este comando cria o grupo de recursos com o nome **myResourceGroup** na localização que definir.
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-create-a-storage-account"></a>Passo 3: criar uma conta do Storage
É necessária uma [Conta do Storage](../storage/storage-introduction.md) para armazenar o disco rígido virtual que é utilizado pela máquina virtual que criar. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.

1. Teste o nome da conta de armazenamento quanto à exclusividade. Este comando testa o nome **myStorageAccount**.
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    Se este comando devolver **Verdadeiro**, o nome proposto é exclusivo no Azure. 
2. Agora, crie a conta de armazenamento.
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>Passo 4: criar uma rede virtual
Todas as máquinas virtuais fazem parte de uma [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Crie uma sub-rede para a rede virtual. Este comando cria uma sub-rede com o nome **mySubnet** com um prefixo do endereço de 10.0.0.0/24.
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
2. Agora, crie a rede virtual. Este comando cria uma rede virtual com o nome **myVnet** através da sub-rede que criou e um prefixo de endereço de **10.0.0.0/16**.
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Passo 5: criar um endereço IP público e uma interface de rede
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o endereço IP público. Este comando cria um endereço IP público com o nome **myPublicIp** com um método de alocação **Dinâmica**.
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. Crie a interface de rede. Este comando cria uma interface de rede com o nome **myNIC**.
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>Passo 6: criar uma máquina virtual
Agora que tem todas as peças no local, é a altura de criar a máquina virtual.

1. Execute este comando para configurar o nome de conta de administrador e a palavra-passe para a máquina virtual.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    A palavra-passe tem de ter 12-123 carateres e ter, no mínimo, uma minúscula, uma maiúscula, um número e um caráter especial. 
2. Crie o objeto de configuração para a máquina virtual. Este comando cria um objeto de configuração com o nome **myVmConfig** que define o nome e o tamanho da VM.
   
    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    Consulte o artigo [Tamanhos de máquinas virtuais no Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter uma lista de tamanhos disponíveis para uma máquina virtual.
3. Configure definições do sistema operativo para a VM. Este comando define o nome do computador, o tipo de sistema operativo e as credenciais da conta para a VM.
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```
4. Defina a imagem a utilizar para aprovisionar a VM. Este comando define a imagem do Windows Server a utilizar para a VM. 
   
    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
   
    Para obter mais informações acerca da seleção de imagens a utilizar, veja [Navigate and select Windows virtual machine images in Azure with PowerShell or the CLI (Navegar e selecionar imagens de máquinas virtuais do Windows no Azure com o PowerShell ou a CLI)](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Adicione a interface de rede que criou à configuração.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
6. Defina o nome e a localização do disco rígido da VM. O ficheiro de disco rígido virtual está armazenado num contentor. Este comando cria o disco num contentor com o nome **vhds/myOsDisk1.vhd** na conta de armazenamento que criou.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
7. Adicione as informações de disco do sistema operativo à configuração da VM. Substitua o valor de **$diskName** por um nome para o disco do sistema operativo. Crie a variável e adicione as informações do disco à configuração.
   
    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
8. Por fim, crie a máquina virtual.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Passos Seguintes
* Se tiverem ocorrido problemas com a implementação, um passo seguinte é ler [Troubleshoot common Azure deployment errors with Azure Resource Manager (Resolver erros comuns de implementação do Azure com o Azure Resource Manager)](../azure-resource-manager/resource-manager-common-deployment-errors.md)
* Saiba como gerir a máquina virtual que criar ao rever [Manage virtual machines using Azure Resource Manager and PowerShell (Gerir máquinas virtuais com o Azure Resource Manager e o PowerShell)](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Tire partido da utilização de um modelo para criar uma máquina virtual, utilizando as informações em [Criar uma máquina virtual do Windows com um modelo do Resource Manager](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO2-->


