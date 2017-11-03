---
title: Como etiqueta de um recurso de VM do Windows no Azure | Microsoft Docs
description: "Saiba mais sobre a marcação de uma máquina virtual do Windows criada no Azure utilizando o modelo de implementação Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: mmccrory
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 5f00c4265cea3db02dbb09a7f81be636a3fdd3d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Como tag de máquina virtual do Windows no Azure
Este artigo descreve as diferentes formas para marcar a máquina virtual do Windows no Azure através do modelo de implementação Resource Manager. As etiquetas são definidos pelo utilizador pares chave-valor que podem ser colocadas diretamente um recurso ou um grupo de recursos. Atualmente, o Azure suporta até 15 etiquetas por recursos e o grupo de recursos. Etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Tenha em atenção que as etiquetas são suportadas para os recursos criados através de apenas o modelo de implementação do Resource Manager. Para marcar uma máquina virtual Linux, consulte [como tag de uma máquina virtual Linux no Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marcação com o PowerShell
Para criar, adicionar e eliminar etiquetas através do PowerShell, tem primeiro de configurar a sua [ambiente de PowerShell com o Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Depois de concluir a configuração, pode colocar etiquetas em recursos de computação, rede e armazenamento durante a criação ou depois do recurso é criado através do PowerShell. Este artigo irá concentrar-se em tags visualizar/Editar colocadas em máquinas virtuais.

Em primeiro lugar, navegue para uma Máquina Virtual através de `Get-AzureRmVM` cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se a Máquina Virtual já contém etiquetas, em seguida, irá ver todas as etiquetas em seu recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se quiser adicionar etiquetas através do PowerShell, pode utilizar o `Set-AzureRmResource` comando. Tenha em atenção quando atualizar sinalizadores através do PowerShell, as etiquetas são atualizadas como um todo. Por isso, se estiver a adicionar uma tag a um recurso que já tenha etiquetas, terá de incluir todas as etiquetas que pretende que seja colocada no recurso. Abaixo está um exemplo de como adicionar etiquetas adicionais a um recurso através de Cmdlets do PowerShell.

Este cmdlet primeiro define todas as etiquetas colocadas *MyTestVM* para o *$tags* variável, utilizando o `Get-AzureRmResource` e `Tags` propriedade.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

O segundo comando apresenta as etiquetas para a variável indicada.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

O terceiro comando adiciona uma tag adicional para o *$tags* variável. Tenha em atenção a utilização do  **+=**  acrescentar o novo par chave/valor para o *$tags* lista.

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

O comando quarto define todas as etiquetas definidas no *$tags* variável para o recurso especificado. Neste caso, é MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

O comando quinto mostra todas as etiquetas no recurso. Como pode ver, *localização* está agora definida como uma etiqueta com *MyLocation* como o valor.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

Para saber mais sobre a etiquetagem através do PowerShell, veja o [Cmdlets de recursos do Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a etiquetagem os recursos do Azure, consulte o artigo [descrição geral do Azure Resource Manager] [ Azure Resource Manager Overview] e [etiquetas a utilizar para organizar os recursos do Azure] [ Using Tags to organize your Azure Resources].
* Para ver como as etiquetas podem ajudá-lo a gerir a utilização de recursos do Azure, consulte [compreender a fatura do Azure] [ Understanding your Azure Bill] e [obter informações acerca do consumo de recursos do Microsoft Azure] [Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
