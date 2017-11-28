---
title: "Gerir conjuntos de dimensionamento de Máquina Virtual com a CLI do Azure 2.0 | Microsoft Docs"
description: "Capacidade de conjunto de comandos de Azure CLI 2.0 comuns para gerir conjuntos de dimensionamento de Máquina Virtual, tais como iniciar e parar uma instância ou altere a escala."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 5686d8bd3f9817be2308583afe778e0615154580
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Gerir um conjunto com o 2.0 CLI do Azure de dimensionamento de máquina virtual
Ao longo do ciclo de vida de um conjunto de dimensionamento de máquina virtual, poderá ter de executar um ou mais tarefas de gestão. Além disso, pode querer criar scripts que automatizem várias tarefas de ciclo de vida. Este artigo fornece detalhes sobre alguns dos comandos do Azure CLI 2.0 comuns que permitem-lhe efetuar estas tarefas.

Para concluir estas tarefas de gestão, terá de compilação mais recente do Azure CLI 2.0. Para obter informações sobre como instalar e utilizar a versão mais recente, consulte [instalar o 2.0 CLI do Azure](/cli/azure/install-azure-cli). Se precisar de criar um conjunto de dimensionamento de máquina virtual, pode [criar um conjunto no portal do Azure de dimensionamento](virtual-machine-scale-sets-portal-create.md).


## <a name="view-information-about-a-scale-set"></a>Ver informações sobre um conjunto de dimensionamento
Para ver a informação geral sobre um conjunto de dimensionamento, utilize [mostrar de vmss az](/cli/azure/vmss#show). O exemplo seguinte obtém informações sobre o conjunto nomeado de dimensionamento *myScaleSet* no *myResourceGroup* grupo de recursos. Introduza os seus próprios nomes da seguinte forma:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Vista VMs num conjunto de dimensionamento
Para ver uma lista de instância de VM num conjunto de dimensionamento, utilize [az vmss-instâncias da lista](/cli/azure/vmss#list-instances). O exemplo seguinte lista todas as instâncias de VM o conjunto nomeado de dimensionamento *myScaleSet* no *myResourceGroup* grupo de recursos. Forneça os seus próprios valores para estes nomes de:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Para ver informações adicionais sobre uma instância VM específica, adicione o `--instance-id` parâmetro [az vmss get--vista de instância](/cli/azure/vmss#get-instance-view) e especifique uma instância para ver. O exemplo seguinte visualiza informações sobre a instância de VM *0* no conjunto nomeado de dimensionamento *myScaleSet* e *myResourceGroup* grupo de recursos. Introduza os seus próprios nomes da seguinte forma:

```azurecli
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Informações de ligação de lista de VMs
Para ligar às VMs num conjunto de dimensionamento, SSH ou RDP para um atribuído público IP endereço e número de porta. Por predefinição, as regras de tradução de endereços de rede (NAT) são adicionadas ao balanceador de carga do Azure, que reencaminha o tráfego de ligação remota para cada VM. Para listar os endereços e portas para ligar a instâncias VM num conjunto de dimensionamento, utilize [az vmss lista-instância--informações de ligação](/cli/azure/vmss#list-instance-connection-info). As seguintes informações de ligação de lista de exemplo para a VM instâncias no conjunto nomeado de dimensionamento *myScaleSet* e no *myResourceGroup* grupo de recursos. Forneça os seus próprios valores para estes nomes de:

```azurecli
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostrou informações sobre o conjunto de dimensionamento e as instâncias de VM. Para aumentar ou reduzir o número de instâncias no conjunto de dimensionamento, pode alterar a capacidade. O conjunto de dimensionamento cria ou remove o número necessário de VMs, em seguida, configura as VMs para receber tráfego de aplicações.

Para ver o número de instâncias tem atualmente num conjunto de dimensionamento, utilize [mostrar de vmss az](/cli/azure/vmss#show) e consultar no *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Pode, em seguida, manualmente aumentar ou reduzir o número de máquinas virtuais da escala com [escala de vmss az](/cli/azure/vmss#scale). O exemplo a seguir define o número de VMs no seu dimensionamento definido como *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Se utiliza um alguns minutos a atualizar a capacidade do seu dimensionamento definido. Se diminuir a capacidade de uma escala definido, as VMs com a instância mais elevada IDs são removidos pela primeira vez.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Parar e iniciar as VMs num conjunto de dimensionamento
Para parar uma ou mais VMs num conjunto de dimensionamento, utilize [parar de vmss az](/cli/azure/vmss/stop). O `--instance-ids` parâmetro permite-lhe especificar uma ou mais VMs para parar. Se não especificar que um ID de instância, todas as VMs no conjunto de dimensionamento estão parados. Para deixar de várias VMs, separe cada ID de instância com um espaço.

O exemplo seguinte deixa de instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e *myResourceGroup* grupo de recursos. Forneça os valores da seguinte forma:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

VMs paradas permanecem alocadas e continuam a implicar custos de computação. Se desejar em vez disso, as VMs para ser desalocada e apenas pagar de armazenamento, utilize [az vmss desalocar](/cli/azure/vmss#deallocate). Ao anular atribuição de várias VMs, separe cada ID de instância com um espaço. O exemplo seguinte interrompe e deallocates instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e *myResourceGroup* grupo de recursos. Forneça os valores da seguinte forma:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs num conjunto de dimensionamento
Para iniciar uma ou mais VMs num conjunto de dimensionamento, utilize [az vmss iniciar](/cli/azure/vmss#start). O `--instance-ids` parâmetro permite-lhe especificar uma ou mais VMs para iniciar. Se não especificar que um ID de instância, todas as VMs no conjunto de dimensionamento são iniciados. Para começar a várias VMs, separe cada ID de instância com um espaço.

O exemplo seguinte inicia a instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e *myResourceGroup* grupo de recursos. Forneça os valores da seguinte forma:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Reinicie as VMs num conjunto de dimensionamento
Para reiniciar uma ou mais VMs num conjunto de dimensionamento, utilize [az vmss reiniciar](/cli/azure/vmss#restart). O `--instance-ids` parâmetro permite-lhe especificar uma ou mais VMs para reiniciar. Se não especificar um ID de instância, são reiniciadas todas as VMs no conjunto de dimensionamento. Para reiniciar várias VMs, separe cada ID de instância com um espaço.

O exemplo seguinte reinicia instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e *myResourceGroup* grupo de recursos. Forneça os valores da seguinte forma:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Remover VMs a partir de um conjunto de dimensionamento
Para remover uma ou mais VMs num conjunto de dimensionamento, utilize [az vmss delete-instâncias](/cli/azure/vmss#delete-instances). O ' – ids de instância "parâmetro permite-lhe especificar uma ou mais VMs para remover. Se especificar * para a instância de ID, todas as VMs no conjunto de dimensionamento são removidos. Para remover várias VMs, separe cada ID de instância com um espaço.

O exemplo a seguir remove instância *0* no conjunto nomeado de dimensionamento *myScaleSet* e *myResourceGroup* grupo de recursos. Forneça os valores da seguinte forma:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Passos seguintes
Outras tarefas comuns para conjuntos de dimensionamento incluem como [implementar uma aplicação](virtual-machine-scale-sets-deploy-app.md), e [atualizar instâncias de VM](virtual-machine-scale-sets-upgrade-scale-set.md). Também pode utilizar a CLI do Azure para [configurar regras de dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md).
