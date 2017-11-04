---
title: "Reponha o acesso em VMs do Linux do Azure com a extensão VMAccess | Microsoft Docs"
description: "Reponha o acesso em VMs do Linux do Azure com a extensão VMAccess."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
ms.openlocfilehash: 278bf1785aac71068ab94cf9916af69a204c44be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Gerir utilizadores, SSH e verificação ou reparar discos em VMs do Linux do Azure utilizando a extensão VMAccess com a CLI do Azure 1.0
Este artigo mostra como utilizar a extensão do Azure VMAcesss para verificar ou reparar um disco, reponha o acesso de utilizador, gerir contas de utilizador ou repor a configuração de SSHD no Linux. O artigo requer:

* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* a [CLI do Azure](../../cli-install-nodejs.md) com sessão iniciada com `azure login`.
* a CLI do Azure *tem de estar no* modo Azure Resource Manager`azure config mode arm`.


## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#quick-commands)– nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos


## <a name="quick-commands"></a>Comandos rápidos
Existem duas formas de utilizar o VMAccess em VMs do Linux:

* Utilizar a CLI do Azure 1.0 e os parâmetros necessários.
* A utilizar não processados ficheiros JSON que VMAccess processa e, em seguida, agir em.

Para a secção do comando rápido, vamos utilizar a CLI do Azure 1.0 `azure vm reset-access` método. Nos seguintes exemplos de comando, substitua os valores que contêm "exemplo" com os valores do seu próprio ambiente.

## <a name="create-a-resource-group-and-linux-vm"></a>Criar um grupo de recursos e uma VM com Linux
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Criar uma VM Debian
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>Repor palavra-passe de raiz
Para repor a palavra-passe de raiz:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>Repor chave SSH
Para repor a chave SSH de um utilizador não raiz:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Criar um utilizador
Para criar um utilizador:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>Remover um utilizador
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>Repor SSHD
Para repor a configuração de SSHD:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>Instruções detalhadas
### <a name="vmaccess-defined"></a>VMAccess definida:
O disco no Linux VM é Mostrar erros. Examinar repor a palavra-passe de raiz para a VM com Linux ou a chave privada SSH sejam eliminados acidentalmente. Se o que aconteceu volta nos dias do Centro de dados, terá de existir uma unidade e, em seguida, abra o KVM para obter na consola do servidor. Considere a extensão VMAccess do Azure como esse comutador KVM que lhe permite aceder à consola reponha o acesso ao Linux ou efetuar a manutenção de nível de disco.

Para instruções detalhadas, iremos utilizar o formato extenso de VMAccess, que utiliza ficheiros JSON não processados.  Estes ficheiros VMAccess JSON também podem ser chamados a partir de modelos do Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Utilizar o VMAccess para verificar ou reparar o disco de uma VM com Linux
Utilizar o VMAccess para fazer uma fsck executar no disco em que a VM com Linux.  Também pode efetuar uma verificação de disco e a reparação do disco utilizando um VMAccess.

Para verificar e, em seguida, reparar a utilização de disco este script de VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Execute o script de VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Utilizar o VMAccess para repor o acesso de utilizador para Linux
Se tiver perdido o acesso de raiz na sua VM do Linux, pode iniciar um script de VMAccess para repor a palavra-passe de raiz.

Para repor a palavra-passe de raiz, utilize este script de VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

Execute o script de VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

Para repor a chave SSH de um utilizador não raiz, utilize este script de VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

Execute o script de VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Utilizar o VMAccess para gerir contas de utilizador no Linux
VMAccess é um script de Python que pode ser utilizado para gerir os utilizadores na sua VM do Linux sem iniciar sessão e utilizar o sudo ou a conta raiz.

Para criar um utilizador, utilize este script de VMAccess:

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Execute o script de VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

Para eliminar um utilizador, utilize este script de VMAccess:

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

Execute o script de VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Utilizar o VMAccess para repor a configuração de SSHD
Se efetuar alterações à configuração do Linux VMs SSHD e fechar a ligação SSH antes de a verificar as alterações, pode ser impedidos por SSH'ing novamente.  Pode ser utilizado o VMAccess para repor a configuração de SSHD uma configuração em condições conhecida sem a ser registado através de SSH.

Para repor a configuração de SSHD utilizar este script de VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Execute o script de VMAccess com:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Passos seguintes
Atualizar Linux utilizar extensões de VMAccess do Azure é um método para efetuar alterações numa VM com Linux em execução.  Também pode utilizar ferramentas como init de nuvem e modelos do Azure para modificar a VM com Linux no arranque.

[Sobre as funcionalidades e as extensões de máquina virtual](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Criação de modelos Azure Resource Manager com extensões de VM com Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Nuvem init a utilizar para personalizar uma VM com Linux durante a criação](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

