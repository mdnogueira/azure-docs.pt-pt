---
title: "Reiniciar uma VM num laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como reiniciar uma máquina virtual no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: tarcher
ms.openlocfilehash: 4e46a7b21c8c9900ba61e9cf41000b341645133c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Reiniciar uma VM num laboratório no Azure DevTest Labs
Pode rapidamente e facilmente reiniciar uma máquina virtual no DevTest Labs seguindo os passos neste artigo. Considere o seguinte antes de reiniciar uma VM:

- Tem de executar a VM para a funcionalidade de reinício esteja ativado.
- Se um utilizador estiver ligado a uma VM em execução quando ele efetuar um reinício, estes têm restabeleça a ligação à VM depois de iniciar uma cópia de segurança.
- Se está a ser aplicado um artefacto quando reiniciar a VM, receberá um aviso que o artefacto não pode ser aplicado. 

    ![Aviso ao reiniciar ao aplicar artefactos](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Se a VM estagnou ao aplicar um artefacto, pode utilizar a funcionalidade VM de reinício como uma forma de potencial para resolver o problema.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Passos para reiniciar uma VM num laboratório no Azure DevTest Labs
1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório que inclua a VM que pretende reiniciar.  
1. No painel esquerdo, selecione **My máquinas de virtuais**. 
1. Na lista de VMs, selecione uma VM em execução.
1. Na parte superior do painel de gestão de VM, selecione **reiniciar**.  

    ![Reinicie o botão VM](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitorizar o estado de reinício, selecionando o **notificações** na parte superior direito da janela.

    ![Visualizar o estado de reinício de VM](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Também pode reiniciar uma VM em execução, selecionando o botão de reticências (…) na lista de **My máquinas de virtuais**.

![Reiniciar a VM através de reticências](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Passos seguintes
* Depois de ser reiniciado, poderá restabelecer a ligação à VM selecionando **Connect** no respetivo painel de gestão.
* Explorar o [Galeria de modelo de início rápido DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples)
