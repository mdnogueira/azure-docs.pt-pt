---
title: Carregar um disco do Linux personalizado com o Azure CLI 2.0 | Microsoft Docs
description: "Criar e carregar um disco rígido virtual (VHD) para o Azure utilizando o modelo de implementação Resource Manager e o 2.0 CLI do Azure"
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
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 9159960af396e89f373da711e0cc46fdd996ab83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Carregar e criar uma VM com Linux a partir do disco personalizado com o 2.0 CLI do Azure
Este artigo mostra como carregar um disco rígido virtual (VHD) para uma conta de armazenamento do Azure com o 2.0 CLI do Azure e criar VMs com Linux a partir deste disco personalizado. Também pode efetuar estes passos com a [CLI 1.0 do Azure](upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Esta funcionalidade permite-lhe instalar e configurar um distro Linux os seus requisitos e, em seguida, utilizar essa VHD para criar rapidamente máquinas de virtuais (VMs) do Azure.

Este tópico utiliza contas de armazenamento para os VHDs finais, mas também pode fazer estes passos através de [discos geridos pelo](upload-vhd.md). 

## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, os seguintes detalhes de secção na base de comandos para carregar um VHD para o Azure. Mais informações e o contexto para cada passo é possível encontrar o resto do documento [Iniciar aqui](#requirements).

Certifique-se de que a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes de parâmetros de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `mydisks`.

Em primeiro lugar, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado `myResourceGroup` no `WestUs` localização:

```azurecli
az group create --name myResourceGroup --location westus
```

Criar uma conta de armazenamento para armazenar os discos virtuais com [criar conta de armazenamento az](/cli/azure/storage/account#create). O exemplo seguinte cria uma conta de armazenamento com o nome `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Listar as chaves de acesso para a sua conta de armazenamento com [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys#list). Tome nota do `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Criar um contentor na sua conta de armazenamento utilizando a chave de armazenamento obtida com [criar contentor de armazenamento az](/cli/azure/storage/container#create). O exemplo seguinte cria um contentor com o nome `mydisks` utilizar o valor de chave de armazenamento do `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Por fim, carregar o VHD para o contentor que criou com [carregamento de blob de armazenamento az](/cli/azure/storage/blob#upload). Especifique o caminho local para o VHD em `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Especifique o URI para o seu disco (`--image`) com [az vm criar](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada `myVM` através do disco virtual carregada anteriormente:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A conta de armazenamento de destino tem de ser o mesmo que em que o seu disco virtual para que carregou. Também tem de especificar ou resposta pede ao utilizador, todos os parâmetros adicionais de que o **az vm criar** comando tais como a rede virtual, o endereço IP público, o nome de utilizador e chaves SSH. Pode ler mais sobre o [parâmetros disponíveis de Gestor de recursos do CLI](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para concluir os passos seguintes, tem de:

* **Sistema de operativo Linux instalado num ficheiro. vhd** -instalar um [distribuição Linux aprovadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consulte [informações para distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) para um disco virtual no VHD formato. Existem várias ferramentas para criar uma VM e os VHD:
  * Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), tendo cuidado para utilizar o VHD como o formato de imagem. Se necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) utilizando `qemu-img convert`.
  * Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O formato VHDX mais recente não é suportado no Azure. Quando cria uma VM, especifique o VHD como o formato. Se for necessário, pode converter discos VHDX VHD utilizando [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, o Azure não suporta o carregamento de VHDs dinâmicos, por isso terá de converter essas discos VHDs estáticos antes de carregar. Pode utilizar ferramentas como [utilitários de VHD do Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) converter discos dinâmicos durante o processo de carregamento para o Azure.
> 
> 

* VMs criadas a partir do seu disco personalizado tem de residir na mesma conta de armazenamento como disco de si próprio
  * Criar uma conta de armazenamento e um contentor para conter o disco personalizado e VMs criadas
  * Depois de criar as suas VMs, pode eliminar em segurança o disco

Certifique-se de que a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes de parâmetros de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Preparar o disco para ser carregado
Azure suporta várias distribuições em Linux (consulte [distribuições aprovadas pelo](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Os artigos seguintes descrevem como preparar as diversas distribuições de Linux que são suportadas no Azure:

* **[Distribuições baseado em centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Outras - distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Consulte também o  **[Linux instalação notas](create-upload-generic.md#general-linux-installation-notes)**  para dicas mais gerais sobre preparar imagens de Linux para o Azure.

> [!NOTE]
> O [plataforma Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se para VMs do Linux em execução apenas quando um das distribuições endorsed é utilizado com os detalhes de configuração conforme especificado em versões suportadas em [Linux em Azure-Endorsed distribuições](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Grupos de recursos logicamente reunir todos os recursos do Azure para suportar as suas máquinas virtuais, tais como redes virtuais e armazenamento. Para grupos de recursos mais informações, consulte [descrição geral de grupos de recursos](../../azure-resource-manager/resource-group-overview.md). Antes de carregar o seu disco personalizado e a criação de VMs, terá primeiro de criar um grupo de recursos com [criar grupo az](/cli/azure/group#create).

O exemplo seguinte cria um grupo de recursos denominado `myResourceGroup` no `westus` localização:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma conta de armazenamento para o seu disco personalizado e VMs com [criar conta de armazenamento az](/cli/azure/storage/account#create). Quaisquer VMs com discos não geridos que criou a partir da sua necessidade de disco personalizado deve estar na mesma conta de armazenamento como esse disco. 

O exemplo seguinte cria uma conta de armazenamento com o nome `mystorageaccount` no grupo de recursos que criou anteriormente:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Lista de chaves de conta de armazenamento
O Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Estas chaves de acesso são utilizadas durante a autenticação, tais como a conta de armazenamento para realizar operações de escrita. Leia mais sobre [gerir o acesso a armazenamento aqui](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Ver as chaves de acesso com [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys#list).

Ver as chaves de acesso para a conta de armazenamento que criou:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
Tome nota do `key1` como irá utilizá-lo para interagir com a sua conta de armazenamento nos passos.

## <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
Da mesma forma que criar diretórios diferentes para organizar logicamente o sistema de ficheiros local, pode criar contentores dentro de uma conta de armazenamento para organizar os discos. Uma conta do storage pode conter qualquer número de contentores. Criar um contentor com [criar contentor de armazenamento az](/cli/azure/storage/container#create).

O exemplo seguinte cria um contentor com o nome `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Carregar o VHD
Agora carregue o seu disco personalizado com [carregamento de blob de armazenamento az](/cli/azure/storage/blob#upload). Carregar e armazenar o disco personalizado como um blob de página.

Especifique a chave de acesso, o contentor que criou no passo anterior e, em seguida, o caminho para o disco personalizado no seu computador local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Crie a VM
Para criar uma VM com discos não geridos, especifique o URI para o seu disco (`--image`) com [az vm criar](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada `myVM` através do disco virtual carregada anteriormente:

Especificar o `--image` parâmetro com [az vm criar](/cli/azure/vm#create) para apontar para o seu disco personalizado. Certifique-se de que `--storage-account` corresponde à conta de armazenamento onde está armazenado o seu disco personalizado. Não é necessário utilizar o mesmo contentor que o disco personalizado para armazenar as suas VMs. Certifique-se criar quaisquer contentores adicionais da mesma forma como os passos anteriores antes de carregar o seu disco personalizado.

O exemplo seguinte cria uma VM chamada `myVM` do seu disco personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Terá de especificar ou resposta pede ao utilizador, todos os parâmetros adicionais de que o **az vm criar** comando como o nome de utilizador e as chaves SSH.


## <a name="resource-manager-template"></a>Modelo do Resource Manager
Modelos Azure Resource Manager são ficheiros JavaScript Object Notation (JSON) que definem o ambiente que pretende criar. Os modelos são divididos para fornecedores de recursos diferente, tais como a computação ou rede. Pode utilizar os modelos existentes ou escrever os seus próprios. Leia mais sobre [utilizando o Gestor de recursos e modelos](../../azure-resource-manager/resource-group-overview.md).

Dentro do `Microsoft.Compute/virtualMachines` fornecedor do seu modelo, tiver um `storageProfile` nó que contém os detalhes de configuração para a VM. São os dois parâmetros principais para editar o `image` e `vhd` URIs que apontam para o disco personalizado e disco virtual a nova VM. O seguinte mostra um exemplo de JSON para utilizar um disco personalizado:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Pode utilizar [este modelo existente para criar uma VM a partir de uma imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou leia sobre [criar os seus próprios modelos Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Assim que tiver um modelo configurado, utilize [criar a implementação do grupo az](/cli/azure/group/deployment#create) para criar as suas VMs. Especifique o URI do seu modelo JSON com o `--template-uri` parâmetro:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Se tiver um ficheiro JSON armazenado localmente no seu computador, pode utilizar o `--template-file` parâmetro em vez disso:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Passos seguintes
Depois de ter preparado e carregado o disco virtual personalizado, pode ler mais sobre [utilizando o Gestor de recursos e modelos](../../azure-resource-manager/resource-group-overview.md). Pode também querer [adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para as VMs de novo. Se tiver aplicações em execução nas suas VMs que precisem de aceder, não se esqueça [abrir portas e os pontos finais](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

