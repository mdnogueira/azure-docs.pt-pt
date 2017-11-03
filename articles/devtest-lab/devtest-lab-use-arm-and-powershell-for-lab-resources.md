---
title: "Criar ou modificar laboratórios automaticamente utilizando os modelos Azure Resource Manager com o PowerShell | Microsoft Docs"
description: "Saiba como utilizar os modelos Azure Resource Manager com o PowerShell para criar ou modificar laboratórios automaticamente num laboratório DevTest"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: tarcher
ms.openlocfilehash: cea4531175df2cc39790497dc049d27e23ffa0c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Criar ou modificar laboratórios automaticamente com modelos Azure Resource Manager e o PowerShell

DevTest Labs fornece muitos modelos Azure Resource Manager e scripts do PowerShell que podem ajudá-lo rapidamente e criar automaticamente laboratórios novo ou modificar laboratórios existentes e, em seguida, implementar estes recursos.

Este artigo ajuda-ajudá-lo durante o processo de utilizar estes modelos e scripts para automatizar a criação, modificação e a implementação da sua laboratórios. Este artigo também mostra onde pode encontrar mais informações sobre como utilizar o PowerShell para efetuar algumas tarefas comuns no DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Passo 1: Recolher os modelos e scripts
Pode encontrar previamente efetuadas [modelos Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) e [scripts do PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) no nosso público [repositório do Github](https://github.com/Azure/azure-devtestlab). Utilizá-los como-é, ou personalize-os para as suas necessidades e armazená-las na sua própria [repositório de Git privado](devtest-lab-add-artifact-repo.md). 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Passo 2: Modificar o modelo Azure Resource Manager
Pode seguir os passos indicados em [criar o primeiro modelo Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-create-first-template) se nunca tiver criado um modelo antes de.

Além disso, [melhores práticas para a criação de modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferece muitas orientações e sugestões para ajudar a criar os modelos Azure Resource Manager que são fiáveis e fáceis de utilizar. Normalmente, irá utilizar uma variação de um dos abordagens ou exemplos fornecidos e modificar o modelo para as suas necessidades.

## <a name="step-3-deploy-resources-with-powershell"></a>Passo 3: Implementar recursos com o PowerShell
Depois de ter personalizada os modelos e os scripts, siga os passos necessários para [implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). O artigo fornece informações gerais sobre como utilizar o Azure PowerShell com modelos Azure Resource Manager para implementar os recursos no Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Tarefas comuns que pode efetuar no DevTest labs através do PowerShell
Existem muitas outras tarefas comuns que podem automatizar utilizando o PowerShell. As seguintes secções da documentação descrevem os passos necessários para efetuar estas tarefas.

* [Criar uma imagem personalizada de um ficheiro VHD utilizando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Carregar ficheiro VHD para a conta de armazenamento do laboratório com o PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Adicionar um utilizador externo a um laboratório com o PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Criar uma função personalizada de laboratório com o PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Passos seguintes
* Saiba como criar um [repositório de Git privado](devtest-lab-add-artifact-repo.md) onde irá armazenar os seus modelos personalizados ou scripts.
* Explorar o [modelos Azure Resource Manager na Galeria de modelo de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
