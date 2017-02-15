---
title: "Criar uma VM do Linux com a CLI do Azure 2.0 (Pré-visualização) | Microsoft Azure"
description: "Criar uma VM do Linux com a CLI do Azure 2.0 (Pré-visualização)."
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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 95b924257c64a115728c66956d5ea38eb8764a35
ms.openlocfilehash: 70592ac773aced0bfcec5c7418a6dc53555fab33


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>Criar uma VM do Linux com a CLI do Azure 2.0 (Pré-visualização) (az.py)
Este artigo mostra como implementar rapidamente uma máquina virtual (VM) do Linux no Azure através do comando [az vm create](/cli/azure/vm#create) na CLI do Azure 2.0 (Pré-visualização). 

> [!NOTE] 
> A CLI do Azure 2.0 (Pré-visualização) é a nossa CLI multiplataforma de próxima geração. [Experimente](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)
>
> Os nossos outros documentos utilizam a CLI do Azure existente. Para criar uma VM com a CLI do Azure atual (1.0) e não com a CLI 2.0 (Pré-visualização), veja [Criar uma VM através da CLI do Azure](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para criar uma VM, precisa de: 

* uma conta do Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* da [CLI do Azure v. 2.0 (Pré-visualização)](/cli/azure/install-az-cli2) instalada
* ter sessão iniciada na sua conta do Azure (escreva[az login](/cli/azure/#login))

Também pode implementar rapidamente uma VM do Linux através do [portal do Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O exemplo seguinte mostra como implementar uma VM Debian e ligar a chave Secure Shell (SSH) (os seus argumentos podem ser diferentes; se quiser outra imagem, pode [procurá-la](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em primeiro lugar, escreva [az group create](/cli/azure/group#create) para criar um grupo de recursos que contenha todos os recursos implementados:

```azurecli
az group create -n myResourceGroup -l westus
```

O resultado tem um aspeto semelhante ao seguinte (pode escolher outra opção de `--output` se quiser):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>Criar a VM com a imagem Debian mais recente

Agora, pode criar a sua VM e o respetivo ambiente. Não se esqueça de substituir o valor de `----public-ip-address-dns-name` por um exclusivo; é possível que o valor abaixo possa já não estar disponível.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


O resultado tem o seguinte aspeto. Tenha em atenção os valores `publicIpAddress` ou `fqdn` para **ssh** na sua VM.


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

Inicie sessão na sua VM com o endereço IP público apresentado no resultado. Também pode utilizar o nome de domínio completamente qualificado (FQDN) apresentado.

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

Deverá ver algo semelhante ao resultado seguinte, consoante a distribuição que escolheu:

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Passos seguintes
O comando `az vm create` é a forma mais rápida de implementar uma VM para que possa iniciar sessão numa shell bash e começar a trabalhar. No entanto, utilizar o comando `az vm create` não lhe dá um controlo extenso nem lhe permite criar um ambiente mais complexo.  Para implementar uma VM com Linux personalizada para a sua infraestrutura, pode seguir qualquer um dos artigos seguintes:

* [Utilizar um modelo do Azure Resource Manager para criar uma implementação específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM com Linux Protegida por SSH no Azure através de modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Também pode [utilizar o controlador `docker-machine` do Azure com vários comandos para criar rapidamente uma VM do Linux como anfitrião docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e, se estiver a utilizar Java, experimente o método [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Jan17_HO1-->


