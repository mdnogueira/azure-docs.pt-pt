---
title: "Adicionar etiquetas para um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar uma etiqueta para um laboratório no Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: tarcher
ms.openlocfilehash: c268c1a10e4a313d0520f96e452ef978b6201115
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Adicionar etiquetas para um laboratório no Azure DevTest Labs

Pode criar etiquetas personalizadas e aplicá-las para os recursos de DevTest Labs logicamente categorizar os seus recursos. Mais tarde, pode rapidamente e facilidade de ver todos os recursos na sua subscrição com essa tag. As etiquetas são úteis quando precisar de organizar recursos de gestão ou de faturação.

Os recursos que são suportados por etiquetas incluem

* VMs de computação
* NICs
* Endereços IP
* Balanceadores de carga
* Contas de armazenamento
* Managed disks

Pode aplicar etiquetas quando tem [criar um laboratório](devtest-lab-create-lab.md) e geri-los mais tarde através do painel de etiquetas na configuração e as definições.

Cada etiqueta é constituída por um **nome**/**valor** par. Por exemplo, poderá criar uma etiqueta com o nome *costcenter* que tem um valor de *34543*. Uma etiqueta como isto pode ajudá-lo mais tarde identificar recursos de laboratório estão sujeito a faturação para esta área específica da sua organização. Obter para escolher os nomes e valores que façam sentido para a forma como pretende organizar a sua subscrição.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Passos para gerir etiquetas de um laboratório existente

1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista. O laboratório já pode ser apresentado no Dashboard em **todos os recursos**.
1. Na lista de laboratórios, selecione o laboratório no qual pretende adicionar ou gerir etiquetas.  
1. No laboratório de **descrição geral** área selecione **políticas de configuração e**.  

    ![Botão de configuração e políticas](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. No lado esquerdo em **GERIR**, selecione **etiquetas**.
1. Para criar uma nova etiqueta para este laboratório, introduza um **nome**/**valor** emparelhe e selecione **guardar**. Também pode selecionar uma etiqueta existente na lista para ver ou gerir os recursos associados com essa tag.

    ![Gerir etiquetas](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Limitações de compreender a etiquetas

As seguintes limitações aplicam-se às etiquetas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares de nomes/valores de etiquetas. Esta limitação aplica-se apenas a etiquetas diretamente aplicadas ao recurso ou grupo de recursos. Um grupo de recursos pode conter muitos recursos que tenham, cada um, 15 pares de nomes/valores de etiqueta. 
* O nome de etiqueta está limitado a 512 caracteres e o valor a 256. Nas contas de armazenamento, o nome da etiqueta está limitado a 128 caracteres e o valor a 256.
* As etiquetas aplicadas ao grupo de recursos não são herdadas pelos recursos nesse grupo de recursos.

[Utilize etiquetas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) fornece maiores detalhes sobre como utilizar etiquetas no Azure, incluindo como gerir etiquetas utilizando o PowerShell ou a CLI do Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* Pode aplicar restrições e convenções na sua subscrição através da utilização de políticas personalizadas. Uma política que definir poderão exigir que todos os recursos têm um valor para uma tag específica. Para obter mais informações, consulte [definir políticas e as agendas](devtest-lab-set-lab-policy.md).
* Explorar o [Galeria de modelo de início rápido do DevTest Labs do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
