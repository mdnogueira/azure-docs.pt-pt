---
title: "Migrar a conta de automatização e os recursos | Microsoft Docs"
description: "Este artigo descreve como mover uma conta de automatização na automatização do Azure e aos recursos associados a partir de uma subscrição para outro."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 9c2db4a2-f324-48dc-8ce7-3343bf7230d5
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 6a6765753e0dadf31692819a9d8f2ca7e77c7f10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-automation-account-and-resources"></a>Migrar a conta de automatização e de recursos
Para as contas de automatização e os respetivos recursos associados (ou seja, recursos, runbooks, módulos, etc.) que tenha criado no portal do Azure e pretender migrar de um grupo de recursos para outro ou a partir de uma subscrição para outro, pode conseguir isto facilmente com o [mover recursos](../azure-resource-manager/resource-group-move-resources.md) funcionalidade disponível no portal do Azure. No entanto, antes de prosseguir com esta ação, deve rever primeiro o seguinte [lista de verificação antes de mover os recursos](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources) e além disso, a lista seguinte específica de automatização.   

1. O grupo de recursos/subscrição de destino tem de ser na mesma região que a origem.  Ou seja, não não possível mover as contas de automatização em regiões.
2. Quando mover os recursos (por exemplo, runbooks, tarefas, etc.), o grupo de origem e o grupo de destino estão bloqueados durante a operação. Escrever e operações de eliminação são bloqueados nos grupos até concluir a movimentação.  
3. Todos os runbooks ou variáveis, o que fazer referência a um ID de recurso ou na subscrição da subscrição existente, devem ser atualizadas depois de concluída a migração.   

> [!NOTE]
> Esta funcionalidade não suporta mover recursos de automatização clássico.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>Para mover a conta de automatização com o portal
1. Da sua conta de automatização, clique em **mover** na parte superior da página.<br> ![Opção de mover](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. No **mover recursos** painel, tenha em atenção que apresenta recursos relacionados com a sua conta de automatização e os grupos de recursos.  Selecione o **subscrição** e **grupo de recursos** na lista pendente, ou selecione a opção **criar um novo grupo de recursos** e introduza um nome do novo grupo de recursos no campo fornecido.  
3. Rever e selecionar a caixa de verificação para reconhecer *compreender as ferramentas e scripts terão de ser atualizadas para utilizar o novo recurso IDs depois de recursos foram movidos* e, em seguida, clique em **OK**.<br> ![Mover o painel de recursos](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Esta ação pode demorar vários minutos a concluir.  No **notificações**, são apresentados com um Estado de cada ação que ocorre - validação, a migração e, em seguida, por fim, quando for concluído.     

## <a name="to-move-the-automation-account-using-powershell"></a>Para mover a conta de automatização com o PowerShell
Para mover os recursos de automatização existentes para outro grupo de recursos ou subscrição, utilize o **Get-AzureRmResource** cmdlet para obter a conta de automatização específica e, em seguida, **mover AzureRmResource** cmdlet para efetuar a mudança.

O primeiro exemplo mostra como mover uma conta de automatização para um novo grupo de recursos.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

Depois de executar o exemplo de código acima, lhe for pedido para confirmar que pretende efetuar esta ação.  Assim que clicar em **Sim** e permitir que o script continuar, não irá receber as notificações enquanto está a efetuar a migração.  

Para mover para uma nova subscrição, incluir um valor para o *DestinationSubscriptionId* parâmetro.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Tal como acontece com o exemplo anterior, lhe for pedido para confirmar a mudança.  

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como mover recursos para o novo grupo de recursos ou subscrição, consulte [mover recursos para o novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md)
* Para obter mais informações sobre o Controlo de Acesso baseado em Funções na Automatização do Azure, consulte o [Controlo de acesso baseado em funções da Automatização do Azure](automation-role-based-access-control.md).
* Para mais informações sobre cmdlets do PowerShell para gerir a sua subscrição, consulte o artigo [utilizar o Azure PowerShell com o Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* Para saber mais sobre as funcionalidades de portais para gerir a sua subscrição, consulte [no portal do Azure para gerir os recursos](../azure-resource-manager/resource-group-portal.md).
