---
title: "Crie a sua primeira VM num laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como criar a sua primeira máquina virtual num laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2017
ms.author: tarcher
ms.openlocfilehash: aa6b60b799e1e98815cf288d5612f98cd77cc00e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Crie a sua primeira VM num laboratório no Azure DevTest Labs

Quando aceder DevTest Labs inicialmente e pretende criar a sua primeira VM, fazê-lo irá, provavelmente, utilizando um previamente carregado [imagem do base marketplace](devtest-lab-configure-marketplace-images.md). Mais tarde no, também poderá escolher uma [imagem personalizada e uma fórmula](devtest-lab-add-vm.md) durante a criação de mais VMs. 

Este tutorial explica como utilizar o portal do Azure para adicionar a sua primeira VM a um laboratório no DevTest Labs.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Passos para adicionar a sua primeira VM a um laboratório no Azure DevTest Labs
1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório no qual pretende criar a VM.  
1. No laboratório de **descrição geral** painel, selecione **+ adicionar**.  

    ![VM botão Adicionar](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. No **escolher uma base** painel, selecione um mercado de imagem para a VM.
1. No **Máquina Virtual** painel, introduza um nome para a nova máquina virtual no **nome da Máquina Virtual** caixa de texto.

    ![Painel VM de laboratório](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. Introduza um **nome de utilizador** que é concedido privilégios de administrador na máquina virtual.  
1. Introduza uma palavra-passe no campo de texto com etiqueta **escreva um valor**.
1. O **tipo de disco de Máquina Virtual** determina o tipo de disco de armazenamento permitido para as máquinas virtuais no laboratório.
1. Selecione **tamanho da Máquina Virtual** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho de disco rígido da VM para criar.
1. Selecione **artefactos** - na lista de artefactos - selecionar e configurar os artefactos que pretende adicionar à imagem de base.
    **Nota:** se estiver familiarizado com DevTest Labs ou configurar artefactos, consulte o [adicionar um artefacto existente para uma VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) secção e, em seguida, volte aqui quando foi concluída.
1. Selecione **criar** para adicionar a VM especificada para o laboratório.

   O painel de laboratório indicará o estado da criação da VM - primeiro **criar**, em seguida, como **executar** depois da VM foi iniciada.

## <a name="next-steps"></a>Passos seguintes
* Quando tiver sido criada a VM, pode ligar à VM selecionando **Connect** no painel da VM.
* Veja [adicionar uma VM para um laboratório](devtest-lab-add-vm.md) para informações mais completas sobre como adicionar VMs subsequentes no laboratório.
* Explorar o [Galeria de modelo de início rápido do DevTest Labs do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
