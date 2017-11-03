---
title: Criar imagens VM personalizadas com a CLI do Azure | Microsoft Docs
description: Tutorial - criar uma imagem VM personalizada utilizando a CLI do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/21/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 23212bc947dc523561acdf9d1ca784d1bab2c84c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Criar uma imagem personalizada da VM do Azure utilizando a CLI

São imagens personalizadas, como imagens do marketplace, mas que são criados por si. Imagens personalizadas podem ser utilizadas para configurações de arranque do sistema, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações de SO. Neste tutorial, vai criar sua imagem personalizada de uma máquina virtual do Azure. Saiba como:

> [!div class="checklist"]
> * Desaprovisionar e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Lista todas as imagens na sua subscrição
> * Eliminar uma imagem


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar

Como efetuar uma VM existente e ativá-la para uma imagem personalizada reutilizável que pode utilizar para criar novas instâncias VM de detalhe os passos abaixo.

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se for necessário, isto [script de exemplo](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) pode criar uma por si. Quando trabalhar com o tutorial, substitua o grupo de recursos e VM nomes sempre que necessário.

## <a name="create-a-custom-image"></a>Criar uma imagem personalizada

Para criar uma imagem de uma máquina virtual, terá de preparar a VM ao desaprovisionamento, Desalocação e, em seguida, marcar a VM como generalizado de origem. Assim que a VM tenha sido preparada, pode criar uma imagem.

### <a name="deprovision-the-vm"></a>Desaprovisionar a VM 

Desaprovisionamento generaliza VM ao remover informações específicas do computador. Este generalização torna possível implementar várias VMs a partir de uma única imagem. Durante o desaprovisionamento, o nome do anfitrião é reposto a *localhost.localdomain*. Chaves de anfitrião do SSH, configurações de nameserver, palavra-passe raiz e concessões DHCP em cache também são eliminados.

Para desaprovisionar a VM, utilize o agente da VM do Azure (waagent). O agente da VM do Azure está instalado na VM e gere de aprovisionamento e interagir com o controlador de recursos de infraestrutura do Azure. Para obter mais informações, consulte o [guia de utilizador do agente Linux do Azure](agent-user-guide.md).

Ligar à VM através do SSH e execute o comando para desaprovisionar a VM. Com o `+user` argumento, a última conta de utilizador aprovisionado e quaisquer dados associados também são eliminados. Substitua o endereço IP de exemplo com o endereço IP público da sua VM.

SSH para a VM.
```bash
ssh azureuser@52.174.34.95
```
Desaprovisionar a VM.

```bash
sudo waagent -deprovision+user -force
```
Feche a sessão SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desalocar e marcar a VM como generalizado

Para criar uma imagem, a VM tem de ser anulada. Desalocação da VM utilizando [az vm desalocar](/cli//azure/vm#deallocate). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Por fim, definir o estado da VM como generalizado com [az vm Generalizar](/cli//azure/vm#generalize) para a plataforma do Azure confie a VM tenha sido generalizada. Só pode criar uma imagem de uma VM generalizada.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Criar a imagem

Agora pode criar uma imagem da VM utilizando [criar imagem az](/cli//azure/image#create). O exemplo seguinte cria uma imagem com o nome *myImage* de uma VM chamada *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Criar as VMs a partir da imagem

Agora que tem uma imagem, pode criar uma ou mais VMs novas a partir da imagem utilizando [az vm criar](/cli/azure/vm#create). O exemplo seguinte cria uma VM chamada *myVMfromImage* na imagem com o nome do *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>Gestão de imagens 

Seguem-se alguns exemplos de tarefas comuns de gestão de imagem e como conclui-los utilizando a CLI do Azure.

Liste todas as imagens de por nome de um formato de tabela.

```azurecli-interactive 
az image list \
  --resource-group myResourceGroup
```

Elimine uma imagem. Neste exemplo elimina a imagem com o nome *myOldImage* do *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, vai criar uma imagem VM personalizada. Aprendeu a:

> [!div class="checklist"]
> * Desaprovisionar e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Lista todas as imagens na sua subscrição
> * Eliminar uma imagem

Avançar para o próximo tutorial para saber mais sobre máquinas virtuais altamente disponíveis.

> [!div class="nextstepaction"]
> [Criar VMs de elevada](tutorial-availability-sets.md).

