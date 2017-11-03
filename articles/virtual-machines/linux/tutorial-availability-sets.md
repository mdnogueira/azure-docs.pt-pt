---
title: Tutorial de conjuntos de disponibilidade para VMs com Linux no Azure | Microsoft Docs
description: Saiba mais sobre os conjuntos de disponibilidade para VMs com Linux no Azure.
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e7780a29f6633b444608d96012fabe67b9b6d924
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-availability-sets"></a>Como utilizar os conjuntos de disponibilidade


Neste tutorial, irá aprender a aumentar a disponibilidade e fiabilidade das suas soluções de Máquina Virtual no Azure através de uma capacidade chamada conjuntos de disponibilidade. Conjuntos de disponibilidade Certifique-se de que as VMs que implementa no Azure estão distribuídas vários clusters hardware isolado. Fazer isto garante que se ocorre uma falha de hardware ou software no Azure, apenas um subconjunto das suas VMs são afetados e de que a solução global permanece disponível e operacional.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verifique os tamanhos VM disponíveis


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="availability-set-overview"></a>Descrição geral do conjunto de disponibilidade

Um conjunto de disponibilidade é uma capacidade de agrupamento lógico que pode utilizar no Azure para garantir que os recursos da VM, colocar dentro da mesma estão isolados umas das outras quando estão implementadas dentro de um datacenter do Azure. Azure garante que as VMs colocar dentro de um conjunto de disponibilidade sejam executadas em vários servidores físicos, computação bastidores, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de software do Azure ou de hardware, apenas um subconjunto das suas VMs são afetados e global da sua aplicação permanece cópias de segurança e continua a estar disponíveis para os seus clientes. Conjuntos de disponibilidade são uma capacidade essencial quando pretender criar soluções de nuvem fiável.

Vejamos uma típica VM solução baseada em que poderá ter 4 servidores de web de front-end e utilize 2 VMs de back-end que aloja uma base de dados. Com o Azure, iria querer definir dois conjuntos de disponibilidade antes de implementar as suas VMs: um conjunto de disponibilidade para a camada "web" e um conjunto de disponibilidade para a camada "base de dados". Quando cria uma nova VM, em seguida, pode especificar a conjunto de disponibilidade como um parâmetro para a vm az criar comando e Azure automaticamente assegura que as VMs que cria no conjunto disponível são isoladas entre vários recursos de hardware físico. Se o hardware físico que o servidor Web ou VMs do servidor de base de dados está em execução no tem um problema, sabe que as outras instâncias do servidor Web e as VMs de base de dados permanecem em execução por estarem em hardware diferente.

Utilize conjuntos de disponibilidade quando pretender implementar as soluções baseadas na VM fiáveis, no Azure.


## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Pode criar um conjunto através de disponibilidade [az vm conjunto de disponibilidade criar](/cli/azure/vm/availability-set#create). Neste exemplo, vamos definir ambos os o número de domínios de atualização e de falhas em *2* para o conjunto nomeada de disponibilidade *myAvailabilitySet* no *myResourceGroupAvailability* grupo de recursos.

Crie um grupo de recursos.

```azurecli-interactive 
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Conjuntos de disponibilidade permitem-lhe isolar os recursos em domínios de falhas e domínios de atualização. A **domínio de falhas** representa uma coleção isolada do servidor + rede + armazenamento recursos. No exemplo anterior, estamos a indicar que queremos nosso conjunto de disponibilidade para ser distribuída em, pelo menos, dois domínios de falhas quando os nossas VMs são implementadas. Também iremos indicar que queremos nosso conjunto distribuída em dois de disponibilidade **domínios de atualização**.  Dois domínios de atualização Certifique-se de que quando o Azure executa as atualizações de software nosso recursos VM isolados, para impedir que todos os softwares com por baixo do nosso VM de serem actualizados ao mesmo tempo.


## <a name="create-vms-inside-an-availability-set"></a>Criar VMs dentro de um conjunto de disponibilidade

As VMs têm de ser criadas dentro da conjunto de disponibilidade para Certifique-se que corretamente estão distribuídos o hardware. Não é possível adicionar uma VM existente para um conjunto depois de criado de disponibilidade. 

Quando cria uma VM utilizando [az vm criar](/cli/azure/vm#create) especificar a disponibilidade definida utilizando o `--availability-set` parâmetro para especificar o nome do conjunto de disponibilidade.

```azurecli-interactive 
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Iremos agora, tem duas máquinas virtuais no nosso conjunto de disponibilidade criado recentemente. Porque se encontram no mesmo conjunto de disponibilidade, o Azure garante que as VMs e todos os respetivos recursos (incluindo os discos de dados) estão distribuídos isolado hardware físico. Esta distribuição ajuda Certifique-se muito maior disponibilidade da nossa solução global de VM.

Se observar a conjunto de disponibilidade no portal, acedendo a grupos de recursos > myResourceGroupAvailability > myAvailabilitySet, deverá ver a forma como as VMs estão distribuídas a falha de 2 e domínios de atualização.

![Conjunto de disponibilidade no portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Verifique a existência de tamanhos VM disponíveis 

Pode adicionar mais VMs para o conjunto mais tarde de disponibilidade, mas é necessário saber quais os tamanhos de VM estão disponíveis no hardware.  Utilize [lista-tamanhos do conjunto de disponibilidade de vm az](/cli/azure/availability-set#list-sizes) para listar todos os tamanhos disponíveis no hardware de cluster para o conjunto de disponibilidade.

```azurecli-interactive 
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM num conjunto de disponibilidade
> * Verifique os tamanhos VM disponíveis

Avançar para o próximo tutorial para saber mais sobre os conjuntos de dimensionamento de máquina virtual.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento de VM](tutorial-create-vmss.md)

