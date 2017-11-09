---
title: "Inicie sessão numa VM do Azure clássico | Microsoft Docs"
description: "Utilize o portal do Azure para iniciar sessão na máquina virtual do Windows criada com o modelo de implementação clássica."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 510b394256bbe86a5eb5bfbc3af4681670b89de3
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Iniciar sessão numa máquina virtual do Windows através do portal do Azure
No portal do Azure, utilize o **Connect** botão para iniciar uma sessão de ambiente de trabalho remoto e inicie sessão numa VM do Windows.

Pretende ligar a uma VM com Linux? Consulte [como iniciar sessão para uma máquina virtual com Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para obter informações sobre como iniciar sessão a uma VM com o modelo do Resource Manager, consulte [aqui](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual
1. Inicie sessão no Portal do Azure.
2. Clique na máquina virtual que pretende aceder. O nome está listado no **todos os recursos** painel.

    ![Localizações de máquina virtual](./media/connect-logon/azureportaldashboard.png)

3. Clique em **Connect** na barra de comando visível o dashboard de máquina virtual.

    ![Ligar o ícone para a máquina virtual](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sessão na máquina virtual
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Passos seguintes
* Se o **Connect** botão está inativo ou tenha outros problemas com a ligação de ambiente de trabalho remoto, tente a repor a configuração. Clique em **reponha o acesso remoto** partir do dashboard de máquina virtual.

    ![Reposição-remota-acesso](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Para problemas com a palavra-passe, tente repor. Clique em **Repor palavra-passe** ao longo do limite esquerdo do virtual machine dashboard, em **suporte + resolução de problemas**.

    ![Reposição de palavra-passe](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Se estas sugestões não funcionam ou não são o que precisa de, consulte [ligações de resolução de problemas de ambiente de trabalho remoto para uma baseado no Windows Azure Máquina Virtual](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este artigo orienta-o ao longo do diagnóstico e da resolução de problemas comuns.
