---
title: Exemplo de Script CLI do Azure - disco do sistema operativo de montagem | Microsoft Docs
description: Exemplo de Script CLI do Azure - montar o disco do sistema operativo
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c32ea5e6cade34a9c8dac0eab523ebcaa10ef039
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Resolver problemas de um disco de sistema operativo de VMs

Este script monta o disco do sistema operativo de uma máquina virtual falha ou problemática como um disco de dados para uma segunda máquina virtual. Isto pode ser útil quando a resolução de problemas do disco problemas ou a recuperação de dados. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Explicação de script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando nas ligações de tabela para a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Mostrar de vm AZ](https://docs.microsoft.com/cli/azure/vm#az_vm_show) | Devolva uma lista de máquinas virtuais. Neste caso, a opção de consulta é utilizada para devolver o disco de sistema operativo da máquina virtual. Este valor é então adicionado ao nome de variável 'uri'. |
| [eliminação de vm AZ](https://docs.microsoft.com/cli/azure/vm#az_vm_delete) | Elimina uma máquina virtual. |
| [Criar AZ vm](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Cria uma máquina virtual.  |
| [anexar o disco da vm AZ](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) | Anexa um disco a uma máquina virtual. |
| [AZ vm lista--endereços ip](https://docs.microsoft.com/cli/azure/vm#az_vm_list_ip_addresses) | Devolve os endereços IP de uma máquina virtual. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, consulte [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script do CLI de máquina virtual adicional que podem ser encontrados no [documentação de VM do Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
