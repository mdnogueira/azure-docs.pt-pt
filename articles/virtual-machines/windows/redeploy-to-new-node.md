---
title: "Volte a implementar máquinas virtuais do Windows no Azure | Microsoft Docs"
description: "Como voltar a implementar máquinas virtuais do Windows no Azure para mitigar problemas de ligação de RDP."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/26/2017
ms.author: genli
ms.openlocfilehash: 0787d5366dbe59b35a297416ac3ce75e9e6e7d26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Volte a implementar máquina virtual do Windows para o novo nó do Azure
Se tiver sido enfrentam dificuldades resolução de problemas de ambiente de trabalho remoto (RDP) ligação ou aplicação acesso baseado no Windows Azure máquina virtual (VM), voltar a implementar a VM pode ajudar. Quando voltar a implementar uma VM, muda a VM para um novo nó dentro da infraestrutura do Azure e, em seguida, for ligado-la novamente, manter todas as suas opções de configuração e os recursos associados. Este artigo mostra como voltar a implementar uma VM com o Azure PowerShell ou o portal do Azure.

> [!NOTE]
> Depois de voltar a implementar uma VM, o disco temporário é perdido e endereços IP dinâmicos associados à interface de rede virtual são atualizados. 


## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Certifique-se de que o Azure PowerShell mais recente 1. x instalado no seu computador. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

O exemplo seguinte implementa VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas em ligar à VM, pode encontrar ajuda específica no [resolução de problemas nas ligações RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [RDP passos de resolução de problemas de detalhado](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Se não conseguir aceder uma aplicação em execução na sua VM, pode ainda ler [aplicação resolução de problemas](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

