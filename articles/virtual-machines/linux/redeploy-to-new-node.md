---
title: "Volte a implementar máquinas virtuais do Linux no Azure | Microsoft Docs"
description: "Como voltar a implementar máquinas virtuais do Linux no Azure para mitigar problemas de ligação SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2017
ms.author: iainfou
ms.openlocfilehash: 7bf69b2a3c006faa0dc0144313e5ebb64e941e2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Volte a implementar máquina virtual do Linux para o novo nó do Azure
Se enfrentam dificuldades a resolução de problemas de SSH ou acesso à aplicação a uma máquina virtual (VM) do Linux no Azure, voltar a implementar a VM pode ajudar. Quando voltar a implementar uma VM, muda a VM para um novo nó dentro da infraestrutura do Azure e, em seguida, for ligado-lo novamente. Todas as suas opções de configuração e os recursos associados são mantidos. Este artigo mostra como voltar a implementar uma VM utilizando a CLI do Azure ou o portal do Azure.

> [!NOTE]
> Depois de voltar a implementar uma VM, o disco temporário é perdido e endereços IP dinâmicos associados à interface de rede virtual são atualizados. 

Pode voltar a implementar uma VM com uma das seguintes opções. Só tem de escolher uma opção para voltar a implementar a VM:

- [CLI 2.0 do Azure](#azure-cli-20)
- [CLI do Azure 1.0](#azure-cli-10)
- [Portal do Azure](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Utilizar a CLI do Azure 2.0
Instalar a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) e início de sessão para um Azure conta através de [início de sessão az](/cli/azure/#login).

Volte a implementar a VM com [volte a implementar az vm](/cli/azure/vm#redeploy). O exemplo seguinte redeploys VM com o nome *myVM* no grupo de recursos denominado *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Utilizar a CLI do Azure 1.0
Instalar o [mais recente do Azure CLI 1.0](../../cli-install-nodejs.md), inicie sessão conta do Azure e certifique-se de que está no modo Resource Manager (`azure config mode arm`).

O exemplo seguinte redeploys VM com o nome *myVM* no grupo de recursos denominado *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas em ligar à VM, pode encontrar ajuda específica no [resolução de problemas de ligações SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [detalhadas passos de resolução de problemas de SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se não conseguir aceder uma aplicação em execução na sua VM, pode ainda ler [aplicação resolução de problemas](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

