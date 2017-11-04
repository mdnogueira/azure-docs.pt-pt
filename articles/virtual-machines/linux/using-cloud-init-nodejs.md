---
title: "Nuvem init a utilizar para personalizar uma VM com Linux durante a criação no Azure | Microsoft Docs"
description: "Como utilizar a cloud init para personalizar uma VM com Linux durante a criação com a CLI do Azure 1.0"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
ms.openlocfilehash: 0b6150bca333188666935b3c9aa02c4b33690db9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>Utilizar nuvem init para personalizar uma VM com Linux durante a criação com a CLI do Azure 1.0
Este artigo mostra como efetuar um script de nuvem init para definir o nome de anfitrião, os pacotes de atualização instalada e gerir contas de utilizador.  Os scripts de nuvem init são denominados durante a criação de VM a partir da CLI do Azure.  O artigo requer:

* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* a [CLI do Azure](../../cli-install-nodejs.md) com sessão iniciada com `azure login`.
* a CLI do Azure *tem de estar no* modo Azure Resource Manager`azure config mode arm`.

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#quick-commands) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="quick-commands"></a>Comandos Rápidos
Crie um script de nuvem init.txt que define o nome do anfitrião, todos os pacotes de atualizações e adiciona um utilizador de sudo ao Linux.

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Crie um grupo de recursos para iniciar as VMs em.

```azurecli
azure group create myResourceGroup westus
```

Crie uma VM com Linux utilizando nuvem init configurá-lo durante o arranque.

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas
### <a name="introduction"></a>Introdução
Quando iniciar uma nova VM do Linux, está a obter um padrão de VM com Linux com nada personalizado ou pronto para as suas necessidades. [Nuvem init](https://cloudinit.readthedocs.org) é um padrão para injetar um definições de configuração ou de script para essa VM com Linux como arrancar a para a primeira vez.

No Azure, existem um três formas diferentes para efetuar alterações para uma VM com Linux como está a ser implementado ou reiniciado.

* Inserir scripts com init de nuvem.
* Inserir scripts com o Azure [extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Um modelo do Azure com a nuvem init.
* Um modelo do Azure utilizando [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para inserir scripts em qualquer altura depois de arranque:

* SSH para executar comandos diretamente
* Inserir scripts com o Azure [extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), imperatively ou num modelo do Azure
* Ferramentas de gestão de configuração como Ansible, Salt, Chef e Puppet.

> [!NOTE]
> : Extensão VMAccess executa um script, como a raiz da mesma forma, utilização do SSH pode.  No entanto, utilizar a extensão da VM permite várias funcionalidades que ofertas do Azure que podem ser úteis, consoante o seu cenário.
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilidade de nuvem init na VM do Azure criação rápida aliases de imagem:
| Alias | Publicador | Oferta | SKU | Versão | init de nuvem |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |mais recente |não |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |sim |
| Debian |credativ |Debian |8 |mais recente |não |
| openSUSE |SUSE |openSUSE |13.2 |mais recente |não |
| RHEL |Redhat |RHEL |7.2 |mais recente |não |
| UbuntuLTS |Canónico |UbuntuServer |14.04.4-LTS |mais recente |sim |

Microsoft está a trabalhar com os nossos parceiros de obter init cloud incluídos e a funcionar nas imagens que fornecem ao Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>A adição de um script de nuvem init para a criação de VM com a CLI do Azure
Para executar um script de nuvem init quando criar uma VM no Azure, especifique o ficheiro de nuvem init utilizando a CLI do Azure `--custom-data` mudar.

Crie um grupo de recursos para iniciar as VMs em.

```azurecli
azure group create myResourceGroup westus
```

Crie uma VM com Linux utilizando nuvem init configurá-lo durante o arranque.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Criar um script de nuvem init para definir o nome do anfitrião de uma VM com Linux
Uma das definições mais simples e mais importantes para qualquer VM com Linux seria o nome do anfitrião. Iremos pode facilmente definir esta nuvem init a utilizar com este script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Script de nuvem init de exemplo com o nome `cloud_config_hostname.txt`.
```sh
#cloud-config
hostname: myservername
```

Durante o arranque inicial da VM, este script de nuvem init define o nome de anfitrião para `myservername`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

Início de sessão e verificar o nome do anfitrião da VM nova.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Criar um script de nuvem init para atualizar o Linux
Para segurança, quer a VM com Ubuntu sejam atualizadas no primeiro arranque.  Utilização de nuvem-init, pode fazê-lo com o script a seguir, consoante a distribuição de Linux que está a utilizar.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Script de nuvem init exemplo `cloud_config_apt_upgrade.txt` para a família Debian
```sh
#cloud-config
apt_upgrade: true
```

Depois de ter arrancado Linux, todos os pacotes instalados são atualizados através de `apt-get`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

Início de sessão e certifique-se de que todos os pacotes são atualizados.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Criar um script de nuvem init para adicionar um utilizador para Linux
Uma das tarefas primeiro qualquer nova VM do Linux é para adicionar um utilizador para si próprio ou para evitar a utilização `root`. SSH chaves são melhor prática de segurança e de Facilidade de utilização e estes são adicionados ao `~/.ssh/authorized_keys` ficheiro com este script de nuvem init.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Script de nuvem init exemplo `cloud_config_add_users.txt` para Debian família
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Depois de ter arrancado Linux, todos os utilizadores listados são criados e adicionados ao grupo de sudo.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

Início de sessão e certifique-se o utilizador recentemente criado.

```bash
ssh myVM
cat /etc/group
```

Saída

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Passos Seguintes
Nuvem init se está a tornar uma forma de padrão para modificar a VM com Linux no arranque. O Azure também tem extensões VM, que permitem-lhe modificar a sua LinuxVM no arranque ou enquanto está em execução. Por exemplo, pode utilizar o Azure VMAccessExtension para repor as informações de utilizador ou SSH enquanto a VM está em execução. Com a nuvem-init, terá de reiniciar o computador para repor a palavra-passe.

[Sobre as funcionalidades e as extensões de máquina virtual](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Gerir utilizadores, SSH e verificação ou reparar discos em VMs do Linux do Azure com a extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

