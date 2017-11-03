---
title: Carregar ou copiar uma VM com Linux personalizada com o Azure CLI 2.0 | Microsoft Docs
description: "Carregar ou copiar uma máquina virtual personalizada utilizando o modelo de implementação Resource Manager e o 2.0 CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/06/2017
ms.author: cynthn
ms.openlocfilehash: 7c297725c26ea6c44403a10ecdcc3542f89f10b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Criar uma VM com Linux a partir do disco personalizado com o 2.0 CLI do Azure

<!-- rename to create-vm-specialized -->

Este artigo mostra como carregar um disco rígido virtual (VHD) personalizado ou copiar uma um VHD existente no Azure e criar novas Linux de máquinas virtuais (VMs) do disco personalizado. Pode instalar e configurar um distro Linux os seus requisitos e, em seguida, utilizar essa VHD para rapidamente criar uma nova máquina virtual do Azure.

Se pretender criar várias VMs a partir do seu disco personalizado, deve criar uma imagem do seu VM ou o VHD. Para obter mais informações, consulte [criar uma imagem personalizada da VM do Azure utilizando a CLI](tutorial-custom-images.md).

Tem duas opções:
* [Carregar um VHD](#option-1-upload-a-specialized-vhd)
* [Copiar uma VM do Azure existente](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Comandos rápidos

Ao criar uma nova VM utilizando [az vm criar](/cli/azure/vm#create) partir de um disco personalizado ou especializado **anexar** o disco (– anexar--disco do SO) em vez de especificar uma imagem personalizada ou marketplace (– imagem). O exemplo seguinte cria uma VM chamada *myVM* utilizando o disco gerido com o nome *myManagedDisk* criadas a partir do seu VHD personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Requisitos
Para concluir os passos seguintes, tem de:

* Uma máquina virtual de Linux que tenha sido preparada para utilização no Azure. O [preparar a VM](#prepare-the-vm) secção deste artigo abrange como localizar distro informações específicas sobre como instalar o agente Linux do Azure (waagent) que é necessária para a VM funcionar corretamente no Azure e para que possa estabelecer ligação à mesma através de SSH.
* O ficheiro VHD existente de um [distribuição Linux aprovadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consulte [informações para distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) para um disco virtual no formato VHD. Existem várias ferramentas para criar uma VM e os VHD:
  * Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), tendo cuidado para utilizar o VHD como o formato de imagem. Se necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) utilizando **qemu img converter**.
  * Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O formato VHDX mais recente não é suportado no Azure. Quando cria uma VM, especifique o VHD como o formato. Se for necessário, pode converter discos VHDX VHD utilizando [qemu img converter](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, o Azure não suporta o carregamento de VHDs dinâmicos, por isso terá de converter essas discos VHDs estáticos antes de carregar. Pode utilizar ferramentas como [utilitários de VHD do Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) converter discos dinâmicos durante o processo de carregamento para o Azure.
> 
> 


* Certifique-se de que a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes de parâmetros de exemplo incluídos *myResourceGroup*, *mystorageaccount*, e *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Preparar a VM

Azure suporta várias distribuições em Linux (consulte [distribuições aprovadas pelo](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Os artigos seguintes descrevem como preparar as diversas distribuições de Linux que são suportadas no Azure:

* [Distribuições baseado em centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Outras - distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Consulte também o [Linux instalação notas](create-upload-generic.md#general-linux-installation-notes) para dicas mais gerais sobre preparar imagens de Linux para o Azure.

> [!NOTE]
> O [plataforma Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se para VMs do Linux em execução apenas quando um das distribuições endorsed é utilizado com os detalhes de configuração conforme especificado em versões suportadas em [Linux em Azure-Endorsed distribuições](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Opção 1: Carregar um VHD

Pode carregar um VHD personalizado que tem de ser executado num computador local ou que tenha exportado a partir de outra nuvem. Para utilizar o VHD para criar uma nova VM do Azure, terá de carregar o VHD para uma conta de armazenamento e criar um disco gerido do VHD. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de carregar o seu disco personalizado e a criação de VMs, terá primeiro de criar um grupo de recursos com [criar grupo az](/cli/azure/group#create).

O exemplo seguinte cria um grupo de recursos denominado *myResourceGroup* no *eastus* localização: [descrição geral do Azure gerida discos](../windows/managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma conta de armazenamento para o seu disco personalizado e VMs com [criar conta de armazenamento az](/cli/azure/storage/account#create). 

O exemplo seguinte cria uma conta de armazenamento com o nome *mystorageaccount* no grupo de recursos que criou anteriormente:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Lista de chaves de conta de armazenamento
O Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Estas chaves de acesso são utilizadas durante a autenticação para a conta de armazenamento, como as devidas operações de escrita. Leia mais sobre [gerir o acesso a armazenamento aqui](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Ver as chaves de acesso com [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys#list).

Ver as chaves de acesso para a conta de armazenamento que criou:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

O resultado é semelhante a:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Tome nota do **chave1** como irá utilizá-lo para interagir com a sua conta de armazenamento nos passos.

### <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
Da mesma forma que criar diretórios diferentes para organizar logicamente o sistema de ficheiros local, pode criar contentores dentro de uma conta de armazenamento para organizar os discos. Uma conta do storage pode conter qualquer número de contentores. Criar um contentor com [criar contentor de armazenamento az](/cli/azure/storage/container#create).

O exemplo seguinte cria um contentor com o nome *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Carregar o VHD
Agora carregue o seu disco personalizado com [carregamento de blob de armazenamento az](/cli/azure/storage/blob#upload). Carregar e armazenar o disco personalizado como um blob de página.

Especifique a chave de acesso, o contentor que criou no passo anterior e, em seguida, o caminho para o disco personalizado no seu computador local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Carregar o VHD, poderá demorar algum tempo.

### <a name="create-a-managed-disk"></a>Criar um disco gerido


Criar um disco gerido do VHD com [criar disco de az](/cli/azure/disk#create). O exemplo seguinte cria um disco gerido com o nome *myManagedDisk* do VHD que carregou para a sua conta de armazenamento e o contentor:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Opção 2: Copiar uma VM existente

Também pode criar a VM personalizada no Azure e, em seguida, copie o disco de SO e anexe-o a uma nova VM para criar outra cópia. Isto é adequado para fins de teste, mas se pretender utilizar uma VM do Azure existente como modelo para várias novas VMs, realmente deve criar um **imagem** em vez disso. Para obter mais informações sobre como criar uma imagem de VM do Azure existente, consulte [criar uma imagem personalizada da VM do Azure utilizando a CLI](tutorial-custom-images.md)

### <a name="create-a-snapshot"></a>Criar um instantâneo

Este exemplo cria um instantâneo de uma VM chamada *myVM* no grupo de recursos *myResourceGroup* e cria um instantâneo com o nome *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Criar o disco gerido

Crie um novo disco gerido do instantâneo.

Obter o ID do instantâneo. Neste exemplo, o instantâneo é denominado *osDiskSnapshot* e está a ser o *myResourceGroup* grupo de recursos.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Crie o disco gerido. Neste exemplo, vamos criar um disco gerido com o nome *myManagedDisk* do nosso instantâneo, que é 128 GB de tamanho no armazenamento standard.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Crie a VM

Agora, crie a VM com [az vm criar](/cli/azure/vm#create) e anexe (– anexar--disco do SO) de disco gerido como disco do SO. O exemplo seguinte cria uma VM chamada *myNewVM* utilizando o disco gerido criado a partir do seu carregado VHD:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Deve ser capaz de SSH para a VM com as credenciais da origem de VM. 

## <a name="next-steps"></a>Passos seguintes
Depois de ter preparado e carregado o disco virtual personalizado, pode ler mais sobre [utilizando o Gestor de recursos e modelos](../../azure-resource-manager/resource-group-overview.md). Pode também querer [adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para as VMs de novo. Se tiver aplicações em execução nas suas VMs que precisem de aceder, não se esqueça [abrir portas e os pontos finais](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

