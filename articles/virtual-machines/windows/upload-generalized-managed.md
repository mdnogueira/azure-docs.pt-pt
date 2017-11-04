---
title: Criar uma VM do Azure gerida a partir de um VHD generalizado no local | Microsoft Docs
description: "Carregar um VHD generalizado para o Azure e utilizá-la para criar novas VMs, no modelo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
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
ms.date: 05/19/2017
ms.author: cynthn
ms.openlocfilehash: d802ba16ecb4e32e2adb7be3a8e99c72a1625841
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carregar um VHD generalizado e utilizá-la para criar novas VMs no Azure

Este tópico explica como utilizar o PowerShell para carregar um VHD de uma VM generalizado para o Azure, criar uma imagem do VHD e crie uma nova VM a partir dessa imagem. Pode carregar um VHD exportado a partir de uma ferramenta de virtualização no local ou de outra nuvem. Utilizar [discos geridos](managed-disks-overview.md) para a nova VM simplifica a gestão de VM e fornece uma melhor disponibilidade quando a VM está colocada num conjunto de disponibilidade. 

Se pretender utilizar um script de exemplo, consulte [script para carregar um VHD para o Azure e criar uma nova VM de exemplo](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Antes de começar

- Antes de carregar qualquer VHD para o Azure, deve seguir [preparar um VHD do Windows ou o VHDX para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Reveja [planear a migração para discos geridos](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) antes de iniciar a migração para [discos geridos](managed-disks-overview.md).
- Certifique-se de que tem a versão mais recente do módulo do AzureRM.Compute PowerShell. Execute o seguinte comando para instalá-lo.

    ```powershell
    Install-Module AzureRM.Compute -RequiredVersion 2.6.0
    ```
    Para obter mais informações, consulte [controlo de versões do Azure PowerShell](/powershell/azure/overview).


## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalize a VM do Windows com o Sysprep

Sysprep remove todas as informações pessoais da conta, entre outras coisas e prepara a máquina para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [como Sysprep de utilização: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Certifique-se as funções de servidor em execução na máquina são suportadas pelo Sysprep. Para obter mais informações, consulte [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se estiver a executar o Sysprep antes de carregar o VHD para o Azure pela primeira vez, certifique-se de que tem [preparado VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

1. Iniciar sessão para a máquina virtual do Windows.
2. Abra a janela de linha de comandos como administrador. Altere o diretório para **%windir%\system32\sysprep**e, em seguida, execute `sysprep.exe`.
3. No **ferramenta de preparação de sistema** caixa de diálogo, selecione **introduza sistema Out-of-Box experiência (OOBE)**e certifique-se de que o **Generalize** caixa de verificação está selecionada.
4. No **as opções de encerramento**, selecione **encerramento**.
5. Clique em **OK**.
   
    ![Iniciar o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando tiver concluído o Sysprep, encerrar a máquina virtual. Não reinicie a VM.



## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Se ainda não tiver o PowerShell na versão 1.4 ou superior instalado, leia [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

1. Abra o Azure PowerShell e inicie sessão na sua conta do Azure. Será apresentada uma janela de pop-up para introduzir as credenciais da conta do Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Obter a IDs de subscrição para as subscrições disponíveis.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Definir a subscrição correta com o ID da subscrição. Substitua  *<subscriptionID>*  com o ID da subscrição correta.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Necessita de uma conta de armazenamento no Azure para armazenar a imagem VM carregada. Pode utilizar uma conta de armazenamento existente ou crie um novo. 

Se planear utilizar o VHD para criar um disco gerido para uma VM, a localização da conta de armazenamento tem de ser igual a localização onde irá criar a VM.

Para mostrar as contas de armazenamento disponíveis, escreva:

```powershell
Get-AzureRmStorageAccount
```

Se pretender utilizar uma conta de armazenamento existente, avance para o [carregar a imagem VM](#upload-the-vm-vhd-to-your-storage-account) secção.

Se precisar de criar uma conta de armazenamento, siga estes passos:

1. Tem o nome do grupo de recursos onde a conta de armazenamento deve ser criada. Para obter todos os grupos de recursos que estão na sua subscrição, escreva:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Para criar um grupo de recursos denominado **myResourceGroup** no **EUA Leste** região, escreva:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "East US"
    ```

2. Criar uma conta de armazenamento com o nome **mystorageaccount** neste grupo de recursos utilizando o [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "East US"`
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Os valores válidos para - SkuName são:
   
   * **Standard_LRS** -armazenamento localmente redundante. 
   * **Standard_ZRS** -armazenamento com redundância de zona.
   * **Standard_GRS** -armazenamento com redundância de Georreplicação. 
   * **Standard_RAGRS** -armazenamento com redundância de georreplicação de acesso de leitura. 
   * **Premium_LRS** -armazenamento localmente redundante Premium. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD à sua conta de armazenamento

Utilize o [adicionar AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) cmdlet para carregar o VHD para um contentor na sua conta de armazenamento. Neste exemplo carrega o ficheiro *myVHD.vhd* de *"discos de rígido C:\Users\Public\Documents\Virtual\"*  para uma conta de armazenamento com o nome *mystorageaccount* no o *myResourceGroup* grupo de recursos. O ficheiro será colocado no contentor com o nome *mycontainer* e o novo nome de ficheiro será *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se tiver êxito, receberá uma resposta semelhante a isto:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo da sua ligação de rede e o tamanho do ficheiro VHD, este comando pode demorar algum tempo para concluir

Guardar o **URI de destino** caminho a utilizar mais tarde, se pretender criar um disco gerido ou uma nova VM utilizando o VHD foi carregado.

### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
 
 
Também pode carregar um VHD para a conta de armazenamento com um dos seguintes:

- [AZCopy](http://aka.ms/downloadazcopy)
- [Blob de cópia de armazenamento do Azure API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Blobs de carregamento de Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
- [Referência de API de REST de serviço de importação/exportação de armazenamento](https://msdn.microsoft.com/library/dn529096.aspx)
-   Recomendamos que utilize o serviço de importação/exportação se estimado tempo de carregamento tem mais de 7 dias. Pode utilizar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo de unidade de tamanho e a transferência de dados. 
    Importar/exportar podem ser utilizado para copiar para uma conta de armazenamento standard. Terá de copiar a partir de armazenamento standard para a conta do premium storage utilizando uma ferramenta como o AzCopy.


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Criar uma imagem gerida do VHD foi carregado 

Crie uma imagem gerida utilizando o VHD de SO generalizado. Substitua os valores pelas suas informações.


1.  Em primeiro lugar, defina os parâmetros comuns:

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    $vmSize = "Standard_DS1_v2"
    $location = "East US" 
    $imageName = "yourImageName"
    ```

4.  Crie a imagem utilizando o VHD de SO generalizado.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $urlOfUploadedImageVhd
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Criar a vNet e sub-rede do [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede designada *mySubnet* com o prefixo de endereço de *10.0.0.0/24*.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Criar a rede virtual. Este exemplo cria uma rede virtual denominada *myVnet* com o prefixo de endereço de *10.0.0.0/16*.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Criar uma interface de rede e o endereço IP pública

Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público com o nome *myPip*. 
   
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

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP

Para conseguir iniciar sessão na sua VM através de RDP, tem de ter uma regra de segurança de rede (NSG) que permite o acesso RDP na porta 3389. 

Este exemplo cria um NSG denominado *myNsg* que contenha uma regra denominada *myRdpRule* que permite tráfego RDP de através da porta 3389. Para obter mais informações sobre NSGs, consulte [a abertura de portas para uma VM no Azure utilizando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual

Crie uma variável para a rede virtual foi concluída. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>Obter as credenciais para a VM

O cmdlet seguinte irá abrir uma janela onde vai introduzir um novo nome de utilizador e palavra-passe para utilizar como conta de administrador local para aceder remotamente a VM. 

```powershell
$cred = Get-Credential
```

## <a name="add-the-vm-name-and-size-to-the-vm-configuration"></a>Adicione o nome da VM e o tamanho para a configuração de VM.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Defina a imagem VM como imagem de origem para a nova VM

Defina a imagem de origem com o ID da imagem VM gerido.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Definir a configuração do SO e adicione o NIC.

Introduza o tipo de armazenamento (PremiumLRS ou StandardLRS) e o tamanho do disco de SO. Neste exemplo define o tipo de conta para *PremiumLRS*, o tamanho do disco para *128 GB* e colocação em cache do disco para *ReadWrite*.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Crie a VM

Criar a nova VM com a configuração armazenada no **$vm** variável.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada
Quando terminar, deverá ver a VM criada recentemente o [portal do Azure](https://portal.azure.com) em **procurar** > **máquinas virtuais**, ou utilizando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos seguintes

Para iniciar sessão sua nova máquina virtual, navegue para a VM com o [portal](https://portal.azure.com), clique em **Connect**e abra o ficheiro RDP de ambiente de trabalho remoto. Utilize as credenciais da conta da máquina virtual original para iniciar sessão sua nova máquina virtual. Para obter mais informações, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure com o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

