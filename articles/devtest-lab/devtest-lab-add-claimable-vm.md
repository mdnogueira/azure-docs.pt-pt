---
title: "Criar e gerir VMs claimable num laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar uma máquina virtual claimable para um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: tarcher
ms.openlocfilehash: 17ddf920dbed6b561c657495b2554d8c1450831c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Criar e gerir claimable VMs no Azure DevTest Labs
Adicionar uma VM claimable para um laboratório de forma semelhante ao procedimento [adicionar uma VM padrão](devtest-lab-add-vm.md) – num *base* que está a um [imagem personalizada](devtest-lab-create-template.md), [fórmula](devtest-lab-manage-formulas.md), ou [imagem do Marketplace](devtest-lab-configure-marketplace-images.md). Este tutorial explica-lhe utilizar o portal do Azure para adicionar uma VM claimable para um laboratório no DevTest Labs e mostra os processos de um utilizador que se segue para afirmações e unclaim a VM.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Passos para adicionar uma VM claimable a um laboratório no Azure DevTest Labs
1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório no qual pretende criar a VM claimable.  
1. No laboratório de **descrição geral** painel, selecione **+ adicionar**.  

    ![VM botão Adicionar](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. No **escolher uma base** área, selecione uma base para a VM.
1. No **Máquina Virtual** painel, introduza um nome para a nova máquina virtual no **nome da Máquina Virtual** caixa de texto.

    ![Painel VM de laboratório](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Introduza um **nome de utilizador** que é concedido privilégios de administrador na máquina virtual.  
1. Se pretender utilizar uma palavra-passe armazenada no seu [arquivo secreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), selecione **utilizar um segredo guardado**e especifique um valor de chave que corresponda ao seu segredo (palavra-passe). Caso contrário, introduza uma palavra-passe no campo de texto com etiqueta **escreva um valor**.
1. O **tipo de disco de Máquina Virtual** determina o tipo de disco de armazenamento permitido para as máquinas virtuais no laboratório.
1. Selecione **tamanho da Máquina Virtual** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho de disco rígido da VM para criar.
1. Selecione **artefactos** e, na lista de artefactos, selecione e configure os artefactos que pretende adicionar à imagem de base. Se estiver familiarizado com DevTest Labs ou configurar artefactos, consulte o [adicionar um artefacto existente para uma VM](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) secção e, em seguida, volte aqui quando foi concluída.
1. Selecione **definições avançadas** para configurar opções de expiração e as opções de rede da VM. Em **afirmação opções**, escolha **Sim** para tornar a máquina claimable.

  ![Escolha esta opção para que a VM claimable.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Se pretender ver ou copiar o modelo Azure Resource Manager, consulte o [modelo Azure Resource Manager guardar](devtest-lab-add-vm.md#save-azure-resource-manager-template) secção e devolver aqui quando foi concluída.
1. Selecione **criar** para adicionar a VM especificada para o laboratório.

   É apresentado o estado da criação da VM, primeiro como **criar**, em seguida, como **executar** depois da VM foi iniciada.

> [!NOTE]
> Se implementar VMs de laboratório através de [modelos Azure Resource Manager](devtest-lab-create-environment-from-arm.md), pode criar claimable VMs definindo a **allowClaim** propriedade para VERDADEIRO na secção de propriedades.
>
>

## <a name="using-a-claimable-vm"></a>Utilizar uma VM claimable

Um utilizador pode afirmação qualquer VM na lista de "Claimable virtual machines" efetuando um destes passos:

* Na lista de "Claimable virtual machines" na parte inferior do painel de "descrição geral" o laboratório, faça duplo clique das VMs na lista e escolha **máquina afirmação**.

 ![Pedir uma VM claimable específica.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Na parte superior do painel de "descrição geral", escolha **afirmação qualquer**. Uma máquina virtual aleatória é atribuída a partir da lista de claimable VMs.

 ![Qualquer VM claimable o pedido.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Depois de uma VM de afirmações de um utilizador, são movida para cima na respetiva lista de "Os meus máquinas virtuais" e já não consta claimable por qualquer outro utilizador.

## <a name="unclaim-a-vm"></a>Unclaim uma VM

Quando um utilizador é terminar com uma VM pedida e pretende tornar disponível para outra pessoa, pode devolvem a VM pedida à lista de máquinas virtuais claimable efetuando um destes passos:

- Na lista de "Os meus máquinas virtuais", faça duplo clique das VMs na lista – ou selecione o botão de reticências (…) – e escolha **Unclaim**.

  ![Unclaim uma VM na lista de VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Na lista de "Os meus máquinas virtuais", selecione uma VM para abrir o painel de gestão, em seguida, selecione **Unclaim** na barra de menu superior.

  ![Unclaim uma VM no painel de gestão da VM.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Quando um utilizador unclaims uma VM, já não possui permissões para essa VM de laboratório específica.

### <a name="transferring-the-data-disk"></a>Transferir o disco de dados
Se uma VM claimable tem um disco de dados ligado ao mesmo e um utilizador unclaims-lo, o disco de dados situa-se com a VM. Quando outro utilizador, em seguida, as afirmações que a VM, esse utilizador novo afirmações o disco de dados, bem como a VM.

Isto é conhecido como "Transferir o disco de dados". O disco de dados, em seguida, fica disponível na lista do novo utilizador de **meu discos de dados** -lhes a gerir.

![Unclaim discos de dados.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Passos seguintes
* Depois de criado, pode ligar à VM selecionando **Connect** no respetivo painel de gestão.
* Explorar o [Galeria de modelo de início rápido do Azure Resource Manager para DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
