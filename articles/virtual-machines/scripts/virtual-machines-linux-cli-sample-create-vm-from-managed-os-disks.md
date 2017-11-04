---
title: Script CLI do Azure de exemplo - criar uma VM ao anexar um disco como disco de SO gerido | Microsoft Docs
description: Script CLI do Azure de exemplo - criar uma VM ao anexar um disco gerido como disco do SO
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 2141ea4fd25dfc69ada02c54c4f6b6b717b8e7db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Criar uma máquina virtual utilizando um disco de SO gerido existente com a CLI

Este script cria uma máquina virtual, anexar um disco existente gerido como disco do SO. Utilize este script no precedente cenários:
* Criar uma VM a partir de um disco de SO gerido existente que foi copiado a partir de um disco gerido numa subscrição diferente
* Criar uma VM a partir de um disco gerido existente criada a partir do ficheiro VHD especializado 
* Criar uma VM a partir de um disco de SO gerido existente que foi criado a partir de um instantâneo 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para obter propriedades de disco gerido, anexar um disco gerido para uma nova VM e criar uma VM. Cada item nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Mostrar de disco AZ](https://docs.microsoft.com/cli/azure/disk#az_disk_show) | Obtém as propriedades de disco gerido com o nome do disco e o nome do grupo de recursos. Propriedade de ID é utilizada para anexar um disco gerido para uma nova VM |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Cria uma VM a utilizar um disco de SO gerido |
## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação de VM do Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
