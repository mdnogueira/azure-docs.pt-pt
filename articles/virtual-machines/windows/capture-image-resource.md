---
title: Criar uma imagem gerida no Azure | Microsoft Docs
description: "Crie uma imagem gerida de uma VM ou VHD generalizado no Azure. Imagens podem ser utilizadas para criar várias VMs que utilizam discos geridos."
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
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: d6409ac490f530d49f82c93b07b0fd22adbec4de
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Criar uma imagem gerida de uma VM generalizada no Azure

Um recurso de imagem gerido pode ser criado a partir de uma VM generalizada que é armazenada como um disco gerido ou um disco não gerido numa conta do storage. A imagem, em seguida, pode ser utilizada para criar várias VMs. 


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


## <a name="create-a-managed-image-in-the-portal"></a>Criar uma imagem gerida no portal 

1. Abra o [portal](https://portal.azure.com).
2. No menu à esquerda, clique em máquinas virtuais e, em seguida, selecione a VM na lista.
3. Na página para a VM, no menu superior, clique em **capturar**.
3. No **nome**, escreva o nome que pretende utilizar para a imagem.
4. No **grupo de recursos** selecione **criar nova** e escreva um nome, ou selecione **utilizar existente** e selecione um grupo de recursos para utilizar na lista pendente.
5. Se pretender eliminar a VM de origem depois da imagem ter sido criada, selecione **eliminar automaticamente esta máquina virtual depois de criar a imagem**.
6. Quando tiver terminado, clique em **criar**.
16. Depois de criada a imagem, irá vê-los como um **imagem** recursos na lista de recursos no grupo de recursos.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Criar uma imagem de uma VM com o Powershell

Criar uma imagem diretamente a partir da VM garante que a imagem inclui todos os discos associados a VM, incluindo o disco do SO e discos de dados. Este exemplo mostra como criar uma imagem gerida a partir de uma VM que utiliza discos geridos pelo.


Antes de começar, certifique-se de que tem a versão mais recente do módulo do AzureRM.Compute PowerShell. Execute o seguinte comando para instalá-lo.

```azurepowershell-interactive
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para obter mais informações, consulte [controlo de versões do Azure PowerShell](/powershell/azure/overview).


1. Crie algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Certifique-se que a VM foi desalocada.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Definir o estado da máquina virtual para **generalizado**. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Coloque a máquina virtual. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Crie a configuração de imagem.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Crie a imagem.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Criar uma imagem a partir de um disco gerido com o PowerShell

Se apenas pretender criar uma imagem do disco de SO, também pode de crate uma imagem, especificando o ID de disco gerido como disco do SO.

    
1. Crie algumas variáveis. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Obter a VM.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name myVM -ResourceGroupName $rgName
   ```

3. Obter o ID do disco gerido.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Crie a configuração de imagem.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Crie a imagem.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Criar uma imagem a partir de um instantâneo com o Powershell

Pode criar uma imagem gerida a partir de um instantâneo de uma VM generalizado.

    
1. Crie algumas variáveis. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Obter o instantâneo.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Crie a configuração de imagem.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Crie a imagem.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>Criar imagem a partir de um VHD numa conta do storage

Crie uma imagem gerida a partir de um VHD de SO generalizado numa conta do storage. É necessário o URI do VHD na conta de armazenamento, o que está a ser https:// o formato*mystorageaccount*.blob.core.windows.net/*contentor*/*vhd_filename.vhd*. Neste exemplo, o VHD que estamos a utilizar está a ser *mystorageaccount* num contentor com o nome *vhdcontainer* e é o nome de ficheiro VHD *osdisk.vhd*.


1.  Em primeiro lugar, defina os parâmetros comuns:

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Step\deallocate a VM.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marcar a VM como generalizado.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Crie a imagem utilizando o VHD de SO generalizado.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Passos seguintes
- Agora pode [criar uma VM a partir da imagem gerida generalizada](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

