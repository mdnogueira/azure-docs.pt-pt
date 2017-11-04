---
title: Utilizar a cloud init para personalizar uma VM com Linux | Microsoft Docs
description: "Como utilizar a cloud init para personalizar uma VM com Linux durante a criação com o 2.0 CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Utilizar a cloud init para personalizar uma VM com Linux no Azure
Este artigo mostra como utilizar [nuvem init](https://cloudinit.readthedocs.io) para definir o nome do anfitrião, pacotes de atualização e gerir contas de utilizador numa máquina virtual (VM) no Azure. Estes scripts de nuvem init executados no arranque quando criar uma VM com o 2.0 CLI do Azure. Para obter uma descrição mais aprofundada sobre como instalar aplicações, escrever ficheiros de configuração e chaves do Cofre de chaves de inserir, consulte [neste tutorial](tutorial-automate-vm-deployment.md). Também pode efetuar estes passos com a [CLI 1.0 do Azure](using-cloud-init-nodejs.md).


## <a name="cloud-init-overview"></a>Descrição geral da nuvem init
[Nuvem init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux como efetua o arranque pela primeira vez. Pode utilizar a cloud init para instalar pacotes e escrever em ficheiros, ou para configurar utilizadores e de segurança. Como é executado na nuvem init durante o processo de arranque inicial, existem não existem passos adicionais ou agentes necessários para aplicar a configuração.

Nuvem init também funciona em distribuições. Por exemplo, não utilize **apt get instalação** ou **yum instalar** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes para instalar. Nuvem init utiliza automaticamente a ferramenta de gestão do pacote nativo para distro que selecionar.

Estamos a trabalhar com os nossos parceiros de obter init cloud incluídos e a funcionar nas imagens que fornecem ao Azure. A tabela seguinte descreve a disponibilidade de nuvem init atual nas imagens de plataforma do Azure:

| Alias | Publicador | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canónico |UbuntuServer |16.04 LTS |mais recente |
| UbuntuLTS |Canónico |UbuntuServer |14.04.5-LTS |mais recente |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |


## <a name="set-the-hostname-with-cloud-init"></a>Definir o nome de anfitrião com init de nuvem
Nuvem init ficheiros são escritos no [YAML](http://www.yaml.org). Para executar um script de nuvem init quando criar uma VM no Azure com [az vm criar](/cli/azure/vm#create), especifique o ficheiro de init de nuvem com o `--custom-data` mudar. Vamos ver alguns exemplos de que pode configurar com um ficheiro de nuvem init. Um cenário comum consiste em definir o nome do anfitrião de uma VM. Por predefinição, o nome do anfitrião é o mesmo que o nome da VM. 

Em primeiro lugar, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria o grupo de recursos com o nome *myResourceGroup* no *eastus* localização:

```azurecli
az group create --name myResourceGroup --location eastus
```

Na sua shell atual, crie um ficheiro denominado *cloud_init_hostname.txt* e cole a seguinte configuração. Por exemplo, crie o ficheiro na Shell na nuvem não no seu computador local. Pode utilizar qualquer editor que desejar. Na Shell de nuvem, introduza `sensible-editor cloud_init_hostname.txt` para criar o ficheiro e ver uma lista de editores disponíveis. Certifique-se de que o ficheiro de toda a nuvem-init é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
hostname: myhostname
```

Agora, crie uma VM com [az vm criar](/cli/azure/vm#create) e especifique o ficheiro de nuvem init com `--custom-data cloud_init_hostname.txt` da seguinte forma:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

Depois de criado, a CLI do Azure mostra informações sobre a VM. Utilize o `publicIpAddress` para SSH para a VM. Introduza o seu próprio endereço da seguinte forma:

```bash
ssh azureuser@publicIpAddress
```

Para ver o nome da VM, utilize o `hostname` comando da seguinte forma:

```bash
hostname
```

A VM deve reportar o nome do anfitrião como esse valor definido no ficheiro de nuvem init, conforme mostrado no seguinte exemplo de saída:

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>Atualizar uma VM com init de nuvem
Por motivos de segurança, poderá querer configurar uma VM para aplicar as atualizações mais recentes no primeiro arranque. Como nuvem init funciona em diferentes distros do Linux, é necessário especificar `apt` ou `yum` para o Gestor de pacotes. Em vez disso, é possível definir `package_upgrade` e permitir que o processo de nuvem init determinar o mecanismo de adequado para distro em utilização. Este fluxo de trabalho permite-lhe utilizar os scripts de nuvem init mesmo em distros.

Para ver o processo de atualização em ação, crie um ficheiro de nuvem init denominado *cloud_init_upgrade.txt* e cole a seguinte configuração:

```yaml
#cloud-config
package_upgrade: true
```

Agora, crie uma VM com [az vm criar](/cli/azure/vm#create) e especifique o ficheiro de nuvem init com `--custom-data cloud_init_upgrade.txt` da seguinte forma:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

SSH para o endereço IP público da sua VM apresentado no resultado do comando anterior. Introduza o seu próprio endereço IP público da seguinte forma:

```bash
ssh azureuser@publicIpAddress
```

Execute a ferramenta de gestão de pacote e procurar atualizações. O exemplo seguinte utiliza `apt-get` numa VM com Ubuntu:

```bash
sudo apt-get upgrade
```

Como nuvem init verificadas e atualizações instaladas no arranque, existem não existem atualizações para aplicar, conforme mostrado no seguinte exemplo de saída:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Adicionar um utilizador para uma VM com init de nuvem
Uma das tarefas primeiro qualquer nova VM do Linux é adicionar um utilizador para si próprio evitar a utilização de *raiz*. As chaves SSH são a melhor prática de segurança e facilidade de utilização. As chaves são adicionadas para o *~/.ssh/authorized_keys* ficheiro com este script de nuvem init.

Para adicionar um utilizador a uma VM com Linux, crie um ficheiro de nuvem init denominado *cloud_init_add_user.txt* e cole a seguinte configuração. Indique a sua própria chave pública (tais como o conteúdo do *~/.ssh/id_rsa.pub*) para *ssh autorizado-chaves*:

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

Agora, crie uma VM com [az vm criar](/cli/azure/vm#create) e especifique o ficheiro de nuvem init com `--custom-data cloud_init_add_user.txt` da seguinte forma:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

SSH para o endereço IP público da sua VM apresentado no resultado do comando anterior. Introduza o seu próprio endereço IP público da seguinte forma:

```bash
ssh myadminuser@publicIpAddress
```

Para confirmar que o utilizador foi adicionado para a VM e os grupos especificados, ver o conteúdo do *etc/grupo* ficheiro da seguinte forma:

```bash
cat /etc/group
```

O resultado de exemplo seguinte mostra o utilizador a *cloud_init_add_user.txt* ficheiro foi adicionado para a VM e o grupo adequado:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Passos seguintes
Init de nuvem é uma das formas padrão para modificar as suas VMs com Linux no arranque. No Azure, também pode utilizar extensões VM para modificar a VM com Linux no arranque, ou quando está em execução. Por exemplo, pode utilizar a extensão de VM do Azure para executar um script numa VM em execução, não apenas no primeiro arranque. Para obter mais informações sobre as extensões VM, consulte [VM extensões e funcionalidades](extensions-features.md), ou para obter exemplos sobre como utilizar a extensão, consulte [SSH, utilizadores, gerir e verificar ou reparar discos em VMs do Linux do Azure com a extensão VMAccess](using-vmaccess-extension.md).