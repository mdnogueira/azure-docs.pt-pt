---
title: Criar uma VM do Linux com a CLI 2.0 do Azure | Microsoft Azure
description: "Criar uma VM com Linux através da CLI 2.0 do Azure."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: cc51b04c31c02aabf25c9efb1e9cd975077811a4
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-linux-vm-using-the-azure-cli-20"></a>Criar uma VM com Linux através da CLI 2.0 do Azure
Este artigo mostra como implementar rapidamente uma máquina virtual (VM) do Linux no Azure através do comando [az vm create](/cli/azure/vm#create) na CLI 2.0 do Azure com os discos geridos e os discos nas contas de armazenamento nativas. Também pode efetuar estes passos com a [CLI 1.0 do Azure](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para criar uma VM, precisa de: 

* uma conta do Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) instalada
* ter sessão iniciada na sua conta do Azure (escreva[az login](/cli/azure/#login))

(Também pode implementar uma VM com Linux através do [portal do Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

O exemplo seguinte mostra como implementar uma VM Debian e ligá-la com a chave Secure Shell (SSH). Os argumentos podem ser diferentes; se pretender uma imagem diferente, pode [procurar por uma](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="using-managed-disks"></a>Utilizar os Managed Disks

Para utilizar os discos geridos do Azure, tem de utilizar uma região que os suporte. Em primeiro lugar, escreva [az group create](/cli/azure/group#create) para criar um grupo de recursos que contenha todos os recursos implementados:

```azurecli
 az group create -n myResourceGroup -l westus
```

O resultado tem um aspeto semelhante ao seguinte (pode especificar outra opção de `--output` se quiser ver um formato diferente):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>Criar a VM 
Agora, pode criar a sua VM e o respetivo ambiente. Não se esqueça de substituir o valor de `--public-ip-address-dns-name` por um exclusivo; é possível que o valor abaixo possa já não estar disponível.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


O resultado tem o seguinte aspeto. Tenha em atenção os valores `publicIpAddress` ou `fqdn` para **ssh** na sua VM.


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

Inicie sessão na VM com o endereço IP público ou o nome de domínio completamente qualificado (FQDN) listado no resultado.

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

Deverá ver algo semelhante ao resultado seguinte, consoante a distribuição que escolheu:

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

Consulte [Next Steps (Próximos Passos)](#next-steps) para ver outras coisas que pode fazer com a nova VM através dos discos geridos.

## <a name="using-unmanaged-disks"></a>Utilizar discos não geridos 

VMs que utilizam discos de armazenamento não geridos têm contas de armazenamento não geridas. Em primeiro lugar, escreva [az group create](/cli/azure/group#create) para criar um grupo de recursos que contenha todos os recursos implementados:

```azurecli
az group create --name nativedisks --location westus
```

O resultado tem um aspeto semelhante ao seguinte (pode escolher outra opção de `--output` se quiser):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>Criar a VM 

Agora, pode criar a sua VM e o respetivo ambiente. Utilize o sinalizador `--use-unmanaged-disk` para criar a VM com discos não geridos. Também é criada uma conta de armazenamento não gerida. Não se esqueça de substituir o valor de `--public-ip-address-dns-name` por um exclusivo; é possível que o valor abaixo possa já não estar disponível.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-unmanaged-disk
```

O resultado tem o seguinte aspeto. Tenha em atenção os valores `publicIpAddress` ou `fqdn` para **ssh** na sua VM.

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

Inicie sessão na VM com o endereço IP público ou o nome de domínio completamente qualificado (FQDN), ambos listados no resultado acima.

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

Deverá ver algo semelhante ao resultado seguinte, consoante a distribuição que escolheu:

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Passos seguintes
O comando `az vm create` é a forma mais rápida de implementar uma VM para que possa iniciar sessão numa shell bash e começar a trabalhar. No entanto, utilizar o comando `az vm create` não lhe dá um controlo extenso nem lhe permite criar um ambiente mais complexo.  Para implementar uma VM com Linux personalizada para a sua infraestrutura, pode seguir qualquer um dos artigos seguintes:

* [Utilizar um modelo do Azure Resource Manager para criar uma implementação específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM com Linux Protegida por SSH no Azure através de modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Também pode [utilizar o controlador `docker-machine` do Azure com vários comandos para criar rapidamente uma VM do Linux como anfitrião docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e, se estiver a utilizar Java, experimente o método [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).


