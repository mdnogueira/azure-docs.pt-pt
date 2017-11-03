---
title: "Ver e utilizar o modelo Azure Resource Manager de uma máquina virtual | Microsoft Docs"
description: "Saiba como utilizar o modelo Azure Resource Manager a partir de uma máquina virtual para criar outras VMs"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: tarcher
ms.openlocfilehash: 0807ab367b91be5acd261f2b58ca2112b2c9e380
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-virtual-machines-azure-resource-manager-template"></a>Utilizar o modelo Azure Resource Manager de uma máquina virtual

Quando estiver a criar uma máquina virtual (VM) no DevTest Labs através de [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), pode ver o modelo Azure Resource Manager antes de guardar a VM. O modelo, em seguida, pode ser utilizado como base para criar mais laboratório VMs com as mesmas definições.

Este artigo descreve como visualizar o modelo do Resource Manager, ao criar uma VM e como implementá-la mais tarde para automatizar a criação da VM mesma.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>MULTI-VM vs modelos do Resource Manager única VM
Existem duas formas de criar VMs no DevTest Labs com um modelo do Resource Manager: aprovisionar o recurso de Microsoft.DevTestLab/labs/virtualmachines ou o aprovisionar o recurso de Microsoft.Commpute/virtualmachines. Cada um é utilizado em cenários diferentes e necessitam de permissões diferentes.

- Modelos do Resource Manager que utilizar um tipo de recurso Microsoft.DevTestLab/labs/virtualmachines (conforme declarado na propriedade "recursos" no modelo) podem aprovisionar VMs de laboratório individuais. Cada VM, em seguida, apresentado como um único item na lista de máquinas virtuais DevTest Labs:

   ![Lista de VMs como único itens na lista de máquinas virtuais DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Este tipo de modelo do Resource Manager pode ser aprovisionado através do comando do PowerShell do Azure **New-AzureRmResourceGroupDeployment** ou através do comando da CLI do Azure **criar a implementação do grupo az**. Requer permissões de administrador, para que os utilizadores que são atribuídos a uma função de utilizador DevTest Labs não é possível efetuar a implementação. 

- Modelos do Resource Manager que utilizar um tipo de recurso Microsoft.Compute/virtualmachines podem aprovisionar várias VMs como um ambiente único na lista de máquinas virtuais DevTest Labs:

   ![Lista de VMs como único itens na lista de máquinas virtuais DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   As VMs no mesmo ambiente podem ser geridas em conjunto e partilham o mesmo ciclo de vida. Os utilizadores que são atribuídos a uma função de utilizador DevTest Labs podem criar ambientes que utilizam esses modelos, desde que o administrador configurou o laboratório dessa forma.

O resto deste artigo aborda os modelos do Resource Manager que utilizam Mirosoft.DevTestLab/labs/virtualmachines. Estes são utilizados por administradores de laboratório para automatizar a criação de VM de laboratório (por exemplo, VMs claimable) ou a geração de imagem dourada (por exemplo, a fábrica de imagem).

[Melhores práticas para a criação de modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferece muitas orientações e sugestões para ajudar a criar os modelos Azure Resource Manager que são fiáveis e fáceis de utilizar.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Ver e guardar o modelo do Resource Manager de uma máquina virtual
1. Siga os passos indicados em [crie a sua primeira VM num laboratório](devtest-lab-create-first-vm.md) para começar a criar uma máquina virtual.
1. Introduza as informações necessárias para a máquina virtual e adicione quaisquer artefactos que pretende para esta VM.
1. Na parte inferior da janela de definições a configurar, escolha **modelo ARM vista**.

   ![Botão de modelo ARM do Vista](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Copie e guarde o modelo do Resource Manager para utilizar mais tarde para criar outra máquina virtual.

   ![Modelo do Resource manager para guardar para utilização posterior](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Depois de guardar o modelo do Resource Manager, tem de atualizar a secção de parâmetros do modelo para poder utilizá-lo. Pode criar um parameter.json que customizes apenas os parâmetros, fora o modelo do Resource Manager. 

![Personalizar paramaters utilizando um ficheiro JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>Implementar um modelo do Resource Manager para criar uma VM
Depois de guardar um modelo do Resource Manager e personalizada-lo para as suas necessidades, pode utilizá-lo a automatizar a criação de VM. [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) descreve como utilizar o Azure PowerShell com modelos do Resource Manager para implementar os recursos no Azure. [Implementar recursos com modelos do Resource Manager e a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) descreve como utilizar a CLI do Azure com modelos do Resource Manager para implementar os recursos no Azure.

> [!NOTE]
> Apenas um utilizador com permissões de proprietário do laboratório pode criar VMs a partir de um modelo do Resource Manager, utilizando o Azure PowerShell. Se pretender automatizar a criação de VM com um modelo do Resource Manager e apenas tiver permissões de utilizador, pode utilizar o [ **az laboratório vm criar** no CLI](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).

### <a name="next-steps"></a>Passos seguintes
* Saiba como [criar ambientes de várias VMS com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
* Explorar mais modelos de Gestor de recursos de início rápido para a automatização de DevTest Labs do [público repositório do GitHub do DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
