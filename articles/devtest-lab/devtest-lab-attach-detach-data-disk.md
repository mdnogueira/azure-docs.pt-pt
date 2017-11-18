---
title: "Anexar ou desanexar um disco de dados para uma máquina virtual no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como ligar ou desligar um disco de dados para uma máquina virtual no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: 3183c61323b1b9ce22b0b64d9021f683b2276d9d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Anexar ou desanexar um disco de dados para uma máquina virtual no Azure DevTest Labs
[Discos do Azure gerida](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/managed-disks-overview) gere as contas de armazenamento associadas aos discos de dados de máquina virtual. Um utilizador liga um novos dados em disco para uma VM, especifica o tipo e o tamanho de disco necessários e o Azure cria e gere automaticamente o disco. O disco de dados, em seguida, pode ser desligado da VM e um novamente ligado mais tarde para a mesma VM ou anexado a uma VM diferente, que pertence ao mesmo utilizador.

Esta funcionalidade é útil para a gestão de armazenamento ou de software fora de cada máquina virtual individual. Se o armazenamento ou o software já existe no interior de um disco de dados, este pode ser facilmente ligado, anular a exposição e novamente ligado a qualquer VM que é propriedade de utilizador que é proprietária desse disco de dados.

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Antes de ligar um disco de dados para uma VM, reveja estas sugestões:

- O tamanho da VM controla quantos discos de dados, pode anexar. Para obter mais informações, consulte [tamanhos das virtual machines](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes).
- Apenas pode anexar um disco de dados para uma VM que está em execução. Certifique-se que a VM está em execução antes de tentar anexar um disco de dados.

### <a name="attach-a-new-disk"></a>Anexe um novo disco
Siga estes passos para criar e anexar um disco de dados geridos novo a uma VM no Azure DevTest Labs.

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório pretendido. 
1. Na lista de **meu máquinas de virtuais**, selecione uma VM em execução.
1. No menu à esquerda, selecione **discos**.

    ![Selecione os discos de dados para uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Escolha **anexar novo** para criar um novo disco de dados e ligá-lo para a VM.

    ![Anexar o disco de dados de novo a uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Concluir o **anexar novo disco** painel ao introduzir um nome de disco de dados, tipo e tamanho.

    ![Preencha o formulário "anexar o disco novo"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Selecione **OK**.

Após alguns instantes, o novo disco de dados é criado e ligado à VM e é apresentado na lista de **discos de dados** para essa VM.

### <a name="attach-an-existing-disk"></a>Anexar um disco existente
Siga estes passos para reattach um disco de dados disponíveis existente para uma VM em execução. 

1. Selecione uma VM em execução para o qual pretende reattach um disco de dados.
1. No menu à esquerda, selecione **discos**.
1. Selecione **anexar existente** para anexar um disco de dados disponíveis para a VM.

    ![Anexar o disco de dados existente para uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Do **anexar o disco existente** painel, selecione OK.

    ![Anexar o disco de dados existente para uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Após alguns instantes, o disco de dados está ligado à VM e é apresentada na lista de **discos de dados** para essa VM.

## <a name="detach-a-data-disk"></a>Desanexar um disco de dados
Quando já não necessita de um disco de dados que está ligado a uma VM, pode facilmente desanexá-lo. A anular a exposição remove o disco da VM, mas mantém-no armazenamento para uma utilização posterior.

Se pretender utilizar novamente os dados existentes no disco, pode reattach-lo para a mesma máquina virtual ou para outro.

### <a name="detach-from-the-vms-management-pane"></a>Anular a exposição a partir do painel de gestão da VM
1. Na sua lista de máquinas virtuais, selecione uma VM que possui um disco de dados ligado.
1. No menu à esquerda, selecione **discos**.

    ![Selecione os discos de dados para uma máquina virtual](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. Na lista de **discos de dados**, selecione o disco de dados que pretende desligar.
1. Selecione **anulação de exposições** da parte superior do painel de detalhes do disco.

    ![Desanexar um disco de dados](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Selecione **Sim** para confirmar que pretende desanexar o disco de dados.

O disco é desligado e está disponível ao ligar a outra VM. 
### <a name="detach-from-the-labs-main-pane"></a>Anular a exposição a partir do painel principal do laboratório
1. No painel principal do seu laboratório, selecione **meu discos de dados**.

    ![Aceder a discos de dados do laboratório](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Clique no disco de dados que pretende desanexar – ou selecione o botão de reticências (…) – e escolha **anulação de exposições**.

    ![Desanexar um disco de dados](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Selecione **Sim** para confirmar que pretende desanexá-lo.

   > [!NOTE]
   > Se um disco de dados já está desligado, pode optar por removê-lo na sua lista de discos de dados disponíveis, selecionando **eliminar**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Atualizar um disco de dados não geridos
Se tiver uma VM existente que utiliza discos de dados não geridos, pode converter facilmente a VM para utilizar discos geridos. Este processo converte o disco do SO e discos de dados anexados.

Para atualizar um disco de dados não geridos, siga os passos descritos neste artigo para [desanexar o disco de dados](#detach-a-data-disk) de uma VM não gerida. Em seguida, [reattach o disco](#attach-an-existing-disk) numa VM gerida para atualizar automaticamente os dados de disco de não gerido para gerido.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba como gerir discos de dados para [claimable máquinas de virtuais](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

