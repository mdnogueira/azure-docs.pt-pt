---
title: "Exemplo de Script do CLI do Azure - reinício VMs | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - reinício VMs por tag e ID"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: ea114f484c774573b7d219cff9102a7308af356e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restart-vms"></a>Reinício de VMs

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Este exemplo mostra algumas formas de obter algumas VMs e reiniciá-las.

O primeiro reinício todas as VMs no grupo de recursos.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

O segundo obtém as VMs marcadas com `az resouce list` filtros para os recursos que são as VMs e reinicia esses VMs.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Este exemplo funciona numa shell de deteção. Para opções sobre a execução de scripts da CLI do Azure num cliente Windows, consulte [com a CLI do Azure no Windows](../windows/cli-options.md).


## <a name="sample-script"></a>Script de exemplo

O exemplo tem três scripts.
Primeiro Aprovisiona as máquinas virtuais.
Utiliza a opção de não-aguardar para que o comando devolve sem aguardar a resposta de cada VM a ser aprovisionado.
O segundo aguarda para as VMs ser totalmente aprovisionado.
O script terceiro reinicia todas as VMs que foram aprovisionamento e, em seguida, apenas as VMs marcadas.

### <a name="provision-the-vms"></a>Aprovisionar as máquinas virtuais

Este script cria um grupo de recursos e, em seguida, cria três VMs reiniciar.
Dois dos mesmos são etiquetados.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Wait

Este script verifica o estado de aprovisionamento cada 20 segundos até que todos os três VMs são aprovisionadas ou um deles falha para aprovisionar.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Reinicie as VMs

Este script reinicia todas as VMs no grupo de recursos e, em seguida, reinicia-VMs marcadas.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Depois de executar o script de exemplo, o seguinte comando pode ser utilizado para remover os grupos de recursos, VMs e todos os recursos relacionados.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, máquina virtual, conjunto de disponibilidade, o Balanceador de carga e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Criar grupo AZ](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos na qual todos os recursos são armazenados. |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Cria as máquinas virtuais.  |
| [lista de vm AZ](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Utilizado com `--query` para garantir que as VMs são aprovisionadas antes de reiniciar e, em seguida, para obter os IDs das VMs reiniciá-las. |
| [lista de recursos de AZ](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Utilizado com `--query` para obter os IDs das VMs com a etiqueta. |
| [reinício de vm AZ](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Reinicia as VMs. |
| [eliminação do grupo de AZ](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação de VM do Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
