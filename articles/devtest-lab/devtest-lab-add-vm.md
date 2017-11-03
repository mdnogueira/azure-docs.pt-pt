---
title: "Adicionar uma VM para um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar uma máquina virtual para um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
ms.openlocfilehash: 449bffb040dafc8edd0b8b0afd80dbea35cd28ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma VM para um laboratório no Azure DevTest Labs
Se já tiver [criado a sua primeira VM](devtest-lab-create-first-vm.md), provavelmente fez, pelo que, de um previamente carregado [imagem do marketplace](devtest-lab-configure-marketplace-images.md). Agora, se pretender adicionar VMs subsequentes para o laboratório, pode também escolher um *base* que está a um [imagem personalizada](devtest-lab-create-template.md) ou um [fórmula](devtest-lab-manage-formulas.md). Este tutorial explica como utilizar o portal do Azure para adicionar uma VM para um laboratório no DevTest Labs.

Este artigo mostra também como gerir os artefactos para uma VM no laboratório.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Passos para adicionar uma VM a um laboratório no Azure DevTest Labs
1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório no qual pretende criar a VM.  
1. No laboratório de **descrição geral** painel, selecione **+ adicionar**.  

    ![VM botão Adicionar](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. No **escolher uma base** painel, selecione uma base para a VM.
1. No **Máquina Virtual** painel, introduza um nome para a nova máquina virtual no **nome da Máquina Virtual** caixa de texto.

    ![Painel VM de laboratório](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Introduza um **nome de utilizador** que é concedido privilégios de administrador na máquina virtual.  
1. Se pretender utilizar uma palavra-passe armazenada no seu [arquivo secreto](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), selecione **utilizar um segredo guardado**e especifique um valor de chave que corresponda ao seu segredo (palavra-passe). Caso contrário, introduza uma palavra-passe no campo de texto com etiqueta **escreva um valor**.
1. O **tipo de disco de Máquina Virtual** determina o tipo de disco de armazenamento permitido para as máquinas virtuais no laboratório.
1. Selecione **tamanho da Máquina Virtual** e selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho de disco rígido da VM para criar.
1. Selecione **artefactos** - na lista de artefactos - selecionar e configurar os artefactos que pretende adicionar à imagem de base.
    **Nota:** se estiver familiarizado com DevTest Labs ou configurar artefactos, consulte o [adicionar um artefacto existente para uma VM](#add-an-existing-artifact-to-a-vm) secção e, em seguida, volte aqui quando foi concluída.
1. Selecione **definições avançadas** para configurar opções de expiração e as opções de rede da VM. 

   Para definir uma opção de expiração, escolha o ícone de calendário para especificar uma data em que a VM será automaticamente eliminada.  Por predefinição, a VM nunca irá expirar. 
1. Se pretender ver ou copiar o modelo Azure Resource Manager, consulte o [modelo Azure Resource Manager guardar](#save-azure-resource-manager-template) secção e devolver aqui quando foi concluída.
1. Selecione **criar** para adicionar a VM especificada para o laboratório.
1. O painel de laboratório indicará o estado da criação da VM - primeiro **criar**, em seguida, como **executar** depois da VM foi iniciada.

> [!NOTE]
> [Adicionar uma VM claimable](devtest-lab-add-claimable-vm.md) mostra-lhe como tornar a VM claimable para que fique disponível para utilização por qualquer utilizador no laboratório.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Adicionar um artefacto existente para uma VM
Ao criar uma VM, pode adicionar artefactos existentes. Cada laboratório inclui os artefactos pública repositório de artefactos de DevTest Labs, bem como os artefactos que tiver criado e adicionado ao seu repositório de artefactos.

* Azure DevTest Labs *artefactos* permitem-lhe especificar *ações* que são efetuadas quando a VM é aprovisionada, como executar scripts do Windows PowerShell, executar comandos de Bash e instalar software.
* Artefactos *parâmetros* permitem-lhe personalizar os artefactos para o seu cenário específico

Para saber como criar artefactos, consulte o artigo [aprender a criar os seus artefactos para utilização com DevTest Labs](devtest-lab-artifact-author.md).

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório que contém a VM com o qual pretende trabalhar.  
1. Selecione **meu máquinas de virtuais**.
1. Selecione a VM pretendida.
1. Selecione **artefactos**. 
1. Selecione **se aplicam a artefactos**.
1. No **se aplicam a artefactos** painel, selecione o artefacto que pretende adicionar à VM.
1. No **adicionar artefactos** painel, introduza os valores de parâmetros necessários e os parâmetros opcionais que precisa.  
1. Selecione **adicionar** para adicionar o artefacto e voltar para o **se aplicam a artefactos** painel.
1. Continue a adicionar artefactos conforme necessário para a VM.
1. Depois de adicionar os seus artefactos, pode [alterar a ordem na qual são executados os artefactos](#change-the-order-in-which-artifacts-are-run). Pode também aceder novamente à [ver ou modificar um artefacto](#view-or-modify-an-artifact).
1. Quando tiver terminado a adição de artefactos, selecione **aplicar**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem na qual são executados artefactos
Por predefinição, as ações dos artefactos são executadas pela ordem na qual são adicionadas à VM. Os passos seguintes mostram como alterar a ordem na qual são executados os artefactos.

1. Na parte superior do **se aplicam a artefactos** painel, selecione a ligação que indica o número de artefactos que foram adicionados à VM.
   
    ![Número de artefactos adicionado à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No **selecionado artefactos** painel, arraste e largue os artefactos para a ordem pretendida. **Nota:** se tiver problemas em arrastar o artefacto, certifique-se de que são arrastar do lado esquerdo do artefacto. 
1. Selecione **OK** quando terminar.  

## <a name="view-or-modify-an-artifact"></a>Ver ou modificar um artefacto
Os passos seguintes mostram como ver ou modificar os parâmetros de um artefacto:

1. Na parte superior do **se aplicam a artefactos** painel, selecione a ligação que indica o número de artefactos que foram adicionados à VM.
   
    ![Número de artefactos adicionado à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No **selecionado artefactos** painel, selecione o artefacto que pretende ver ou editar.  
1. No **adicionar artefactos** painel, certifique-qualquer necessárias alterações, selecione **OK** para fechar o **adicionar artefactos** painel.
1. Selecione **OK** para fechar o **selecionado artefactos** painel.

## <a name="save-azure-resource-manager-template"></a>Guardar o modelo Azure Resource Manager
Um modelo Azure Resource Manager fornece uma forma declarativa de definir uma implementação repeatable. Os passos seguintes explicam como guardar o modelo Azure Resource Manager para a VM que está a ser criada.
Depois de guardar, pode utilizar o modelo Azure Resource Manager para [implementar novas VMs com o Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. No **Máquina Virtual** painel, selecione **ver o modelo de ARM**.
2. No **modelo de vista do Azure Resource Manager** painel, selecione o texto do modelo.
3. Copie o texto selecionado para a área de transferência.
4. Selecione **OK** para fechar o **painel de vista do Azure Resource Manager modelo**.
5. Abra um editor de texto.
6. Colar o texto do modelo da área de transferência.
7. Guarde o ficheiro para utilização posterior.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Passos seguintes
* Quando tiver sido criada a VM, pode ligar à VM selecionando **Connect** no painel da VM.
* Saiba como [criar artefactos personalizados para a VM do DevTest Labs](devtest-lab-artifact-author.md).
* Explorar o [Galeria de modelo de início rápido do DevTest Labs do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
