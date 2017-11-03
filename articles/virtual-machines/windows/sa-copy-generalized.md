---
title: "Criar uma imagem de uma VM generalizada não gerida no Azure | Microsoft Docs"
description: "Crie uma imagem de unmanged de uma VM do Windows generalizado a utilizar para criar várias cópias de uma VM no Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: d7f4a9558175835eba9096e6845726f21c7459d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Como criar uma imagem VM não gerida a partir de uma VM do Azure

Este artigo abrange a utilização de contas de armazenamento. Recomendamos que utilize discos geridos e imagens geridas em vez de uma conta de armazenamento. Para obter mais informações, consulte [capturar uma imagem gerida de uma VM no Azure generalizada](capture-image-resource.md).

Este artigo mostra-lhe como utilizar o Azure PowerShell para criar uma imagem de uma VM do Azure generalizado com uma conta de armazenamento. Em seguida, pode utilizar a imagem para criar outra VM. Inclui a imagem de disco do SO e os discos de dados que estão anexados à máquina virtual. A imagem não inclui os recursos de rede virtual, por isso terá de configurar esses recursos, ao criar a nova VM. 

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o Azure PowerShell versão 1.0.x ou mais recente instalada. Se já não tiver instalado o PowerShell, leia [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter os passos de instalação.

## <a name="generalize-the-vm"></a>Generalize a VM 
Esta secção mostra como generalize a máquina virtual do Windows para utilização como uma imagem. Generalizar uma VM remove todas as informações pessoais da conta, entre outras coisas e prepara a máquina para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [como Sysprep de utilização: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Certifique-se as funções de servidor em execução na máquina são suportadas pelo Sysprep. Para obter mais informações, consulte [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se estiver a carregar o VHD para o Azure pela primeira vez, certifique-se de que tem [preparado VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

Também pode generalizar uma VM com Linux utilizando `sudo waagent -deprovision+user` e, em seguida, utilizar o PowerShell para capturar a VM. Para obter informações sobre como utilizar a CLI para capturar uma VM, consulte [como generalize e capturar uma máquina virtual de Linux utilizando a CLI do Azure ](../linux/capture-image.md).


1. Iniciar sessão para a máquina virtual do Windows.
2. Abra a janela de linha de comandos como administrador. Altere o diretório para **%windir%\system32\sysprep**e, em seguida, execute `sysprep.exe`.
3. No **ferramenta de preparação de sistema** caixa de diálogo, selecione **introduza sistema Out-of-Box experiência (OOBE)**e certifique-se de que o **Generalize** caixa de verificação está selecionada.
4. No **as opções de encerramento**, selecione **encerramento**.
5. Clique em **OK**.
   
    ![Iniciar o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando tiver concluído o Sysprep, encerrar a máquina virtual. 

> [!IMPORTANT]
> Não reinicie a VM até terminar carregar o VHD para o Azure ou criar uma imagem da VM. Se, acidentalmente, a VM obtém reiniciada, execute o Sysprep para generalizá-lo novamente.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Inicie sessão no Azure PowerShell
1. Abra o Azure PowerShell e inicie sessão na sua conta do Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Será apresentada uma janela de pop-up para introduzir as credenciais da conta do Azure.
2. Obter a IDs de subscrição para as subscrições disponíveis.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Definir a subscrição correta com o ID da subscrição.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Desalocar a VM e definir o estado de generalizado
1. Desalocar os recursos da VM.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    O *estado* para a VM do Azure portal é alterado de **parado** para **parado (desalocado)**.
2. Definir o estado da máquina virtual para **generalizado**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Verifique o estado da VM. O **OSState/generalizado** secção para a VM deve ter o **DisplayStatus** definido como **VM generalizado**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem

Crie uma imagem de máquinas de virtuais não geridos no contentor de armazenamento de destino utilizando este comando. A imagem é criada na mesma conta do storage que a máquina virtual original. O `-Path` parâmetro guarda uma cópia do modelo JSON para a VM de origem para o seu computador local. O `-DestinationContainerName` parâmetro é o nome do contentor que pretende armazenar as imagens. Se o contentor não existe, é criado para si.
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Pode obter o URL da imagem a partir do modelo de ficheiro JSON. Vá para o **recursos** > **storageProfile** > **osDisk** > **imagem** > **uri** secção para o caminho completo da imagem. O URL da imagem do aspeto: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Também pode verificar o URI no portal. A imagem é copiada para um contentor com o nome **sistema** na sua conta de armazenamento. 

## <a name="create-a-vm-from-the-image"></a>Criar uma VM a partir da imagem

Agora pode criar uma ou mais VMs a partir da imagem não gerida.

### <a name="set-the-uri-of-the-vhd"></a>Definir o URI do VHD

O URI para o VHD utilizar está no formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**. vhd. Neste exemplo, o VHD com o nome **myVHD** é na conta de armazenamento **mystorageaccount** no contentor **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Criar a vNet e sub-rede do [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. O exemplo seguinte cria uma sub-rede designada **mySubnet** no grupo de recursos **myResourceGroup** com o prefixo de endereço de **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Criar a rede virtual. O exemplo seguinte cria uma rede virtual denominada **myVnet** no **EUA oeste** localização com o prefixo do endereço de **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Criar uma interface de rede e o endereço IP pública
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público com o nome **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Criar o NIC. Este exemplo cria um NIC com o nome **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para conseguir iniciar sessão na sua VM através de RDP, tem de ter uma regra de segurança que permite o acesso RDP na porta 3389. 

Este exemplo cria um NSG denominado **myNsg** que contenha uma regra denominada **myRdpRule** que permite tráfego RDP de através da porta 3389. Para obter mais informações sobre NSGs, consulte [a abertura de portas para uma VM no Azure utilizando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual
Crie uma variável para a rede virtual foi concluída. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Crie a VM
O PowerShell seguinte realiza as configurações de máquina virtual e utiliza a imagem não gerida como origem para a nova instalação.

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

### <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada
Quando terminar, deverá ver a VM criada recentemente o [portal do Azure](https://portal.azure.com) em **procurar** > **máquinas virtuais**, ou utilizando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Para gerir a sua nova máquina virtual com o Azure PowerShell, consulte [gerir máquinas virtuais utilizando o Azure Resource Manager e o PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


