---
title: Carregar uma imagem personalizada do Linux com a CLI do Azure 1.0 | Microsoft Docs
description: "Criar e carregar um disco rígido virtual (VHD) para o Azure com uma imagem personalizada do Linux, utilizando o modelo de implementação Resource Manager e a CLI do Azure 1.0."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: ca4c6cb9296028275b2b032af0c94baabeec1223
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Carregar e criar uma VM com Linux a partir de imagem de disco personalizado utilizando a CLI do Azure 1.0
Este artigo mostra como carregar um disco rígido virtual (VHD) para o Azure utilizando o modelo de implementação Resource Manager e criar VMs com Linux a partir desta imagem personalizada. Esta funcionalidade permite-lhe instalar e configurar um distro Linux os seus requisitos e, em seguida, utilizar essa VHD para criar rapidamente máquinas de virtuais (VMs) do Azure.


## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#quick-commands) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos


## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, os seguintes detalhes de secção na base de comandos para carregar uma VM no Azure. Mais informações e o contexto para cada passo é possível encontrar o resto do documento [Iniciar aqui](#requirements).

Certifique-se de que tem [a CLI do Azure 1.0](../../cli-install-nodejs.md) a sessão e utilizar o modo Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes de parâmetros de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `myimages`.

Em primeiro lugar, crie um grupo de recursos. O exemplo seguinte cria um grupo de recursos denominado `myResourceGroup` no `WestUs` localização:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

Crie uma conta de armazenamento para armazenar os discos virtuais. O exemplo seguinte cria uma conta de armazenamento com o nome `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Liste as chaves de acesso para a sua conta de armazenamento. Tome nota do `key1`:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Crie um contentor na sua conta de armazenamento utilizando a chave de armazenamento que obteve. O exemplo seguinte cria um contentor com o nome `myimages` utilizar o valor de chave de armazenamento do `key1`:

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Por fim, carregue o VHD para o contentor que criou. Especifique o caminho local para o VHD em `/path/to/disk/mydisk.vhd`:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Agora, pode criar uma VM a partir do disco virtual carregado [com um modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Também pode utilizar a CLI especificando o URI para o seu disco (`--image-urn`). O exemplo seguinte cria uma VM chamada `myVM` através do disco virtual carregada anteriormente:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

A conta de armazenamento de destino tem de ser o mesmo que em que o seu disco virtual para que carregou. Também tem de especificar ou resposta pede ao utilizador, todos os parâmetros adicionais de que o `azure vm create` comando tais como a rede virtual, o endereço IP público, o nome de utilizador e chaves SSH. Pode ler mais sobre o [parâmetros disponíveis de Gestor de recursos do CLI](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para concluir os passos seguintes, tem de:

* **Sistema de operativo Linux instalado num ficheiro. vhd** -instalar um [distribuição Linux aprovadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consulte [informações para distribuições não aprovadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) para um disco virtual no VHD formato. Existem várias ferramentas para criar uma VM e os VHD:
  * Instalar e configurar [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), tendo cuidado para utilizar o VHD como o formato de imagem. Se necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) utilizando `qemu-img convert`.
  * Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O formato VHDX mais recente não é suportado no Azure. Quando cria uma VM, especifique o VHD como o formato. Se for necessário, pode converter discos VHDX VHD utilizando [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) cmdlet do PowerShell. Além disso, o Azure não suporta o carregamento de VHDs dinâmicos, por isso terá de converter essas discos VHDs estáticos antes de carregar. Pode utilizar ferramentas como [utilitários de VHD do Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go) converter discos dinâmicos durante o processo de carregamento para o Azure.
> 
> 

* VMs criadas a partir da sua imagem personalizada tem de residir na mesma conta de armazenamento como a imagem de si próprio
  * Criar uma conta de armazenamento e um contentor para manter a sua imagem personalizada e a VMs criadas
  * Depois de criar as suas VMs, pode eliminar em segurança a sua imagem

Certifique-se de que tem [a CLI do Azure 1.0](../../cli-install-nodejs.md) a sessão e utilizar o modo Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes de parâmetros de exemplo incluídos `myResourceGroup`, `mystorageaccount`, e `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>Preparar a imagem a ser carregado
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


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Grupos de recursos logicamente reunir todos os recursos do Azure para suportar as suas máquinas virtuais, tais como redes virtuais e armazenamento. Leia mais sobre [grupos de recursos do Azure aqui](../../azure-resource-manager/resource-group-overview.md). Antes de carregar a imagem de disco personalizados e a criação de VMs, terá primeiro de criar um grupo de recursos. 

O exemplo seguinte cria um grupo de recursos denominado `myResourceGroup` no `WestUS` localização:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
VMs são armazenadas como blobs de páginas dentro de uma conta de armazenamento. Leia mais sobre [blob storage do Azure aqui](../../storage/common/storage-introduction.md#blob-storage). Criar uma conta de armazenamento para a imagem de disco personalizado e VMs. As VMs que criar a partir da imagem de disco personalizado tem de ser na mesma conta de armazenamento como dessa imagem.

O exemplo seguinte cria uma conta de armazenamento com o nome `mystorageaccount` no grupo de recursos que criou anteriormente:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Lista de chaves de conta de armazenamento
O Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Estas chaves de acesso são utilizadas durante a autenticação, tais como a conta de armazenamento para realizar operações de escrita. Leia mais sobre [gerir o acesso a armazenamento aqui](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Pode ver as chaves de acesso com o `azure storage account keys list` comando.

Ver as chaves de acesso para a conta de armazenamento que criou:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
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
Da mesma forma que criar diretórios diferentes para organizar logicamente o sistema de ficheiros local, pode criar contentores dentro de uma conta de armazenamento para organizar os discos virtuais e imagens. Uma conta do storage pode conter qualquer número de contentores. 

O exemplo seguinte cria um contentor com o nome `myimages`, especificar a chave de acesso obtido no passo anterior (`key1`):

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Carregar o VHD
Agora, na verdade, pode carregar a imagem de disco personalizado. Como com todos os discos virtuais utilizados por VMs, pode carrega e armazenar a imagem de disco personalizadas como um blob de página.

Especifique a chave de acesso, o contentor que criou no passo anterior e, em seguida, o caminho para a imagem de disco personalizado no seu computador local:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Criar a VM de imagem personalizada
Quando criar as VMs da imagem de disco personalizada, especifique o URI para a imagem de disco. Certifique-se de que as correspondências de conta de armazenamento de destino onde está armazenada a imagem de disco personalizado. Pode criar a VM utilizando o modelo do Azure CLI ou do Resource Manager JSON.

### <a name="create-a-vm-using-the-azure-cli"></a>Criar uma VM utilizando a CLI do Azure
Especificar o `--image-urn` parâmetro com o `azure vm create` comando para apontar para a imagem de disco personalizado. Certifique-se de que `--storage-account-name` corresponde à conta de armazenamento onde está armazenada a imagem de disco personalizado. Não é necessário utilizar o mesmo contentor como a imagem de disco personalizado para armazenar as suas VMs. Certifique-se criar quaisquer contentores adicionais da mesma forma como os passos anteriores antes de carregar as imagens de disco personalizado.

O exemplo seguinte cria uma VM chamada `myVM` da imagem de disco personalizado:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Terá de especificar ou resposta pede ao utilizador, todos os parâmetros adicionais de que o `azure vm create` comando tais como a rede virtual, o endereço IP público, o nome de utilizador e chaves SSH. Leia mais sobre o [parâmetros disponíveis de Gestor de recursos do CLI](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Criar uma VM utilizando um modelo JSON
Modelos Azure Resource Manager são ficheiros JavaScript Object Notation (JSON) que definem o ambiente que pretende criar. Os modelos são divididos para fornecedores de recursos diferente, tais como a computação ou rede. Pode utilizar os modelos existentes ou escrever os seus próprios. Leia mais sobre [utilizando o Gestor de recursos e modelos](../../azure-resource-manager/resource-group-overview.md).

Dentro do `Microsoft.Compute/virtualMachines` fornecedor do seu modelo, tiver um `storageProfile` nó que contém os detalhes de configuração para a VM. São os dois parâmetros principais para editar o `image` e `vhd` URIs que apontam para a imagem de disco personalizado e disco virtual a nova VM. O seguinte mostra um exemplo de JSON para utilizar uma imagem de disco personalizado:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Pode utilizar [este modelo existente para criar uma VM a partir de uma imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou leia sobre [criar os seus próprios modelos Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

Depois de ter um modelo configurado, criar as suas VMs utilizando o `azure group deployment create` comando. Especifique o URI do seu modelo JSON com o `--template-uri` parâmetro:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Se tiver um ficheiro JSON armazenado localmente no seu computador, pode utilizar o `--template-file` parâmetro em vez disso:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Passos seguintes
Depois de ter preparado e carregado o disco virtual personalizado, pode ler mais sobre [utilizando o Gestor de recursos e modelos](../../azure-resource-manager/resource-group-overview.md). Pode também querer [adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para as VMs de novo. Se tiver aplicações em execução nas suas VMs que precisem de aceder, não se esqueça [abrir portas e os pontos finais](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

