---
title: Criar VM a partir de um disco especializado no Azure | Microsoft Docs
description: "Crie uma nova VM ao anexar um disco não gerido especializado, no modelo de implementação Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: 974d89aa96cba94fedfd1acbaf4f1d30ac8e6257
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Criar uma VM a partir de um VHD numa conta de armazenamento especializado

Crie uma nova VM ao anexar um disco não gerido especializado como disco do SO através do Powershell. Um disco especializado é uma cópia do VHD de uma VM existente que mantém as contas de utilizador, aplicações e outros dados de estado de original VM. 

Tem duas opções:
* [Carregar um VHD](create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Copie o VHD de uma VM do Azure existente](create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>Antes de começar
Se utilizar o PowerShell, certifique-se de que tem a versão mais recente do módulo do AzureRM.Compute PowerShell. Execute o seguinte comando para instalá-lo.

```powershell
Install-Module AzureRM.Compute 
```
Para obter mais informações, consulte [controlo de versões do Azure PowerShell](/powershell/azure/overview).


## <a name="option-1-upload-a-specialized-vhd"></a>Opção 1: Carregar um VHD especializado

Pode carregar o VHD de uma VM especializada criado com uma ferramenta de virtualização no local, como o Hyper-V ou numa VM exportada de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Pode carregar um VHD especializado que foi criado com uma VM no local ou um VHD exportado a partir da outra nuvem. Um VHD especializado mantém as contas de utilizador, aplicações e outros dados de estado de original VM. Se pretender utilizar o VHD como-para criar uma nova VM, certifique-se de que os seguintes passos são concluídos. 
  
  * [Preparar um VHD do Windows para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalize a VM com o Sysprep.
  * Remova quaisquer agentes que estão instalados na VM (ou seja, as ferramentas do VMware) e ferramentas de Virtualização do convidado.
  * Certifique-se de que a VM está configurada para solicitar o respetivo endereço IP e as definições de DNS através do DHCP. Isto garante que o servidor obtém um endereço IP dentro da VNet durante o arranque. 


### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Necessita de uma conta de armazenamento no Azure para armazenar a imagem VM carregada. Pode utilizar uma conta de armazenamento existente ou crie um novo. 

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

    Para criar um grupo de recursos denominado **myResourceGroup** no **EUA oeste** região, escreva:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Criar uma conta de armazenamento com o nome **mystorageaccount** neste grupo de recursos utilizando o [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD à sua conta de armazenamento
Utilize o [adicionar AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet para carregar a imagem para um contentor na sua conta de armazenamento. Neste exemplo carrega o ficheiro **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` para uma conta de armazenamento com o nome **mystorageaccount** no **myResourceGroup** grupo de recursos. O ficheiro será colocado no contentor com o nome **mycontainer** e o novo nome de ficheiro será **myUploadedVHD.vhd**.

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

Dependendo da sua ligação de rede e o tamanho do ficheiro VHD, este comando pode demorar algum tempo para concluir.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opção 2: Copie o VHD da VM do Azure existente

Pode copiar um VHD para outra conta de armazenamento a utilizar quando criar uma VM nova, duplicada.

### <a name="before-you-begin"></a>Antes de começar
Certifique-se de que tem:

* Ter informações sobre o **contas de armazenamento de origem e destino**. Para a VM de origem, tem de ter os nomes de conta e contentor de armazenamento. Normalmente, o nome de contentor será **vhds**. Terá também de ter uma conta de armazenamento de destino. Se ainda não tiver uma, pode criar uma com o portal ou (**mais serviços** > contas de armazenamento > Adicionar) ou utilizando o [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet. 
* Transferiu e instalou o [AzCopy ferramenta](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Desalocar a VM
Desalocar a VM, o que liberta o VHD seja copiado. 

* **Portal**: clique em **máquinas virtuais** > **myVM** > parar
* **PowerShell**: Utilize [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) a parar (desalocar) VM com o nome **myVM** no grupo de recursos **myResourceGroup**.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

O **estado** para a VM do Azure portal é alterado de **parado** para **parado (desalocado)**.

### <a name="get-the-storage-account-urls"></a>Obter os URLs de conta de armazenamento
Terá dos URLs das contas de armazenamento de origem e de destino. O aspeto de URLs, como: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se já conhece o nome de conta e contentor de armazenamento, apenas pode substituir as informações entre Retos para criar o seu URL. 

Pode utilizar o portal do Azure ou o Azure Powershell para obter o URL:

* **Portal**: clique no  **>**  para **mais serviços** > **contas de armazenamento** > *conta de armazenamento* > **Blobs** e o ficheiro de VHD de origem está a ser provavelmente o **vhds** contentor. Clique em **propriedades** para o contentor e copie o texto da etiqueta **URL**. Terá dos URLs de contentores de origem e de destino. 
* **PowerShell**: Utilize [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) para obter as informações de VM com o nome **myVM** no grupo de recursos **myResourceGroup**. Nos resultados, procure no **perfil de armazenamento** secção para o **Uri de Vhd**. A primeira parte do Uri é o URL para o contentor e a última parte é o nome do VHD do SO para a VM.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Obter as chaves de acesso de armazenamento
Localize as chaves de acesso para a origem e destino contas de armazenamento. Para mais informações sobre chaves de acesso, consulte [contas do storage do Azure sobre](../../storage/common/storage-create-storage-account.md).

* **Portal**: clique em **mais serviços** > **contas do Storage** > *conta de armazenamento* > **chaves de acesso**. Copie a chave identificada como **chave1**.
* **PowerShell**: Utilize [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) para obter a chave de armazenamento para a conta de armazenamento **mystorageaccount** no grupo de recursos **myResourceGroup**. Copie a chave de etiqueta **chave1**.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Copie o VHD
Pode copiar ficheiros entre contas de armazenamento utilizando o AzCopy. Para o contentor de destino, se o contentor especificado não existir, será criado para si. 

Para utilizar o AzCopy, abra uma linha de comandos no seu computador local e navegue para a pasta onde o AzCopy é instalado. Será semelhante *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos os ficheiros dentro de um contentor, utilize o **/S** mudar. Isto pode ser utilizado para copiar o VHD de SO e todos os discos de dados, se estiverem no mesmo contentor. Este exemplo mostra como copiar todos os ficheiros no contentor **mysourcecontainer** na conta do storage **mysourcestorageaccount** ao contentor **mydestinationcontainer** no **mydestinationstorageaccount** conta de armazenamento. Substitua os nomes das contas de armazenamento e de contentores com os seus próprios. Substitua `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` com as suas próprias chaves.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se apenas pretender copiar um VHD específico num contentor com vários ficheiros, também pode especificar o nome de ficheiro com o comutador /Pattern. Neste exemplo, apenas o ficheiro denominado **myFileName.vhd** serão copiados.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Quando estiver concluído, irá receber uma mensagem que aspeto semelhante ao seguinte:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Resolução de problemas
* Quando utilizar o AZCopy, se vir o erro "Falha ao autenticar o pedido Server", certifique-se de que o valor do cabeçalho de autorização está formado corretamente, incluindo a assinatura. Se estiver a utilizar a chave 2 ou a chave de armazenamento secundário, tente utilizar a chave de armazenamento primário ou 1.

## <a name="create-the-new-vm"></a>Criar nova VM 

Terá de criar redes e outros recursos VM a serem utilizadas pela nova VM.

### <a name="create-the-subnet-and-vnet"></a>Criar a sub-rede e a vNet

Criar a vNet e sub-rede do [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede designada **mySubNet**, no grupo de recursos **myResourceGroup**e define o prefixo de endereço de sub-rede **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie a vNet. Neste exemplo define o nome de rede virtual para ser **myVnetName**, a localização para **EUA oeste**e o prefixo de endereço para a rede virtual para **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e NIC
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome de endereço IP público está definido como **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Criar o NIC. Neste exemplo, o nome NIC está definido como **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para conseguir iniciar sessão na sua VM através de RDP, tem de ter uma regra de segurança que permite o acesso RDP na porta 3389. Porque foi criado o VHD para a nova VM existente de um VM especializada, depois da VM é criada, pode utilizar uma conta existente da máquina virtual de origem que tinha permissão para iniciar sessão com RDP.
Neste exemplo define o nome NSG para **myNsg** e o nome da regra RDP para **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre os pontos finais e as regras do NSG, consulte [a abertura de portas para uma VM no Azure utilizando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="set-the-vm-name-and-size"></a>Definir o nome da VM e o tamanho

Neste exemplo define o nome da VM para "myVM" e o tamanho da VM "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicionar o NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Configurar o disco do SO

1. Defina o URI do VHD que é carregado ou copiados. Neste exemplo, o ficheiro VHD denominado **myOsDisk.vhd** é mantida uma conta de armazenamento com o nome **myStorageAccount** num contentor com o nome **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adicione o disco de SO. Neste exemplo, quando o disco do SO é criado, o termo "osDisk" é appened para o nome da VM para criar o nome do disco de SO. Este exemplo também especifica que este VHD baseado no Windows deve ser ligado à VM como disco do SO.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: Se tiver discos de dados que precisem de ser ligado à VM, adicione os discos de dados utilizando os URLs de dados VHDs e o número de unidade lógica (Lun) adequados.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Quando utilizar uma conta de armazenamento, os URLs de disco do sistema operativo e dados algo semelhante ao seguinte: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Pode encontrar isto no portal de navegar para o contentor de armazenamento de destino, clicando o sistema operativo ou os dados VHD que foi copiada e, em seguida, copiar o conteúdo do URL.


### <a name="complete-the-vm"></a>Conclua a VM 

Crie a VM com as configurações que acabamos de criar.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se este comando foi concluída com êxito, verá o resultado como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada
Deverá ver a VM recém-criado está no [portal do Azure](https://portal.azure.com), em **procurar** > **máquinas virtuais**, ou utilizando os seguintes comandos do PowerShell:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Para iniciar sessão sua nova máquina virtual, navegue para a VM com o [portal](https://portal.azure.com), clique em **Connect**e abra o ficheiro RDP de ambiente de trabalho remoto. Utilize as credenciais da conta da máquina virtual original para iniciar sessão sua nova máquina virtual. Para obter mais informações, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure com o Windows](connect-logon.md).

