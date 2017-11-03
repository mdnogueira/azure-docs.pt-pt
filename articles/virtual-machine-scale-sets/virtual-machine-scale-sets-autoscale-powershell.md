---
title: "Conjuntos de dimensionamento de máquina virtual de dimensionamento automático com o Azure PowerShell | Microsoft Docs"
description: "Define como criar regras de dimensionamento automático para dimensionamento da máquina virtual com o Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 1fbfbbc79a415af5e874c304412854849e134eb7
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Dimensionar automaticamente um conjunto com o Azure PowerShell de dimensionamento de máquina virtual
Quando cria um conjunto de dimensionamento, é possível definir o número de instâncias VM que pretende executar. Como muda o seu pedido de aplicação, pode automaticamente aumentar ou reduzir o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a pedido do cliente ou responder a alterações de desempenho da aplicação durante todo o ciclo de vida da sua aplicação.

Este artigo mostra como criar regras de dimensionamento automático com o Azure PowerShell que monitorizar o desempenho das instâncias da VM no seu conjunto de dimensionamento. Estas regras de dimensionamento automático aumentam ou reduzir o número de instâncias de VM em resposta a estas métricas de desempenho. Também pode executar estes passos com o [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) ou no [portal do Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de dimensionamento automático, terá de uma máquina virtual existente conjunto de dimensionamento. Pode criar uma escala definida com o [portal do Azure](virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](virtual-machine-scale-sets-create.md#create-from-powershell), ou [Azure CLI 2.0](virtual-machine-scale-sets-create.md#create-from-azure-cli).

Para tornar mais fácil de criar as regras de dimensionamento automático, definir algumas variáveis para o conjunto de dimensionamento. O exemplo seguinte define as variáveis para o conjunto nomeado de dimensionamento *myScaleSet* no grupo de recursos denominado *myResourceGroup* e no *EUA Leste* região. A subscrição ID é obtido com [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Se tiver várias subscrições associadas à sua conta, é devolvida apenas a primeira subscrição. Ajuste os nomes e o ID de subscrição da seguinte forma:

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Criar uma regra para ampliar automaticamente
Se aumentar o seu pedido de aplicação, a carga sobre as instâncias de VM no seu dimensionamento definir aumenta. Se este aumento de carga é consistente, em vez de apenas um breve pedido, pode configurar regras de dimensionamento automático para aumentar o número de instâncias VM no conjunto de dimensionamento. Quando estas instâncias VM são criadas e as suas aplicações são implementadas, inicia o conjunto de dimensionamento distribuir o tráfego aos mesmos através do Balanceador de carga. Controlar as métricas para monitorizar, tais como CPU ou disco, quanto a carga da aplicação têm de cumprir um determinado limiar, e quantas instâncias VM para adicionar à escala definido.

Vamos criar uma regra com [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que aumenta o número de instâncias VM na escala definida quando a carga média da CPU é superior a 70% durante um período de 5 minutos. Quando a regra é acionado, o número de instâncias VM é aumentado por 20%. Em conjuntos de dimensionamento com um pequeno número de instâncias de VM, pode deixar a `-ScaleActionScaleType` e apenas especificar `-ScaleActionValue` a aumentar, *1* ou *2* instâncias. Conjuntos de dimensionamento com um grande número de instâncias de VM, um aumento de 20% ou de 10% instâncias de VM podem ser mais adequadas.

Os parâmetros seguintes são utilizados para esta regra:

| Parâmetro               | Explicação                                                                                                         | Valor          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | A métrica de desempenho para monitorizar e aplicar escala definir ações.                                                   | Percentagem de CPU |
| *Intervalo-de agregação*            | Frequência as métricas são recolhidas para análise.                                                                   | um minuto       |
| *-MetricStatistic*      | Define a forma como as métricas recolhidas devem ser agregadas para análise.                                                | Média        |
| *-TimeWindow*           | A quantidade de tempo monitorizado antes em comparação com os valores da métrica e de limiar.                                   | 10 minutos      |
| *-Operador*             | Operador utilizado para comparar os dados métricos contra o limiar.                                                     | Maior que   |
| *-Limiar*            | O valor que faz com que a regra de dimensionamento automático acionar uma ação.                                                      | 70%            |
| *-ScaleActionDirection* | Define se o conjunto de dimensionamento deve aumentar ou reduzir verticalmente quando a regra se aplica.                                             | Aumentar       |
| *– ScaleActionScaleType* | Indica que o número de instâncias VM deve ser alterado por um período de percentagem.                                 | Percentagem de alteração |
| *-ScaleActionValue*     | A percentagem de instâncias de VM deve ser alterada quando a regra é acionado.                                            | 20             |
| *-ScaleActionCooldown*  | A quantidade de tempo de espera antes da regra é aplicada novamente para que as ações de dimensionamento automático de ter a hora entre em vigor. | 5 minutos      |

O exemplo seguinte cria um objeto com o nome *myRuleScaleOut* que detém este escala regra. O *- MetricResourceId* utiliza as variáveis definidas anteriormente para o nome do conjunto do ID de subscrição, o nome do grupo de recursos e o dimensionamento:

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para dimensionar automaticamente de
Num evening ou fim de semana, pode diminuir o seu pedido de aplicação. Se este carregamento menor consistente durante um período de tempo, pode configurar regras de dimensionamento automático para reduzir o número de instâncias VM no conjunto de dimensionamento. Esta ação de dimensionamento reduz o custo para executar a escala definida como executar apenas o número de instâncias necessário para satisfazer a procura atual.

Criar uma regra com [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que reduz o número de instâncias VM na escala definido quando a carga de CPU média, em seguida, descerem abaixo de 30% durante um período de 10 minutos. Quando a regra é acionado, o número de instâncias VM é diminuído por 20%. O exemplo seguinte cria um objeto com o nome *myRuleScaleDown* que detém este escala regra. O *- MetricResourceId* utiliza as variáveis definidas anteriormente para o nome do conjunto do ID de subscrição, o nome do grupo de recursos e o dimensionamento:

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>Definir um perfil de dimensionamento automático
Para associar as regras de dimensionamento automático de um conjunto de dimensionamento, pode criar um perfil. O perfil de dimensionamento automático define a predefinição, a capacidade de conjunto de dimensionamento, máxima e mínima e associa as regras de dimensionamento automático. Criar um perfil de dimensionamento automático [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). O exemplo seguinte define a capacidade predefinida e mínimo, de *2* VM instâncias e um máximo de *10*. A ampliação e, em seguida, são ligados a escala nas regras que criou nos passos anteriores:

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicar regras de dimensionamento automático para um conjunto de dimensionamento
O último passo consiste em aplicar o perfil de dimensionamento automático para o conjunto de dimensionamento. A escala tem a capacidade para dimensionar automaticamente de entrada ou saída com base no pedido de aplicação. Aplicar o perfil de dimensionamento automático com [adicionar AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) da seguinte forma:

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorizar o número de instâncias de um conjunto de dimensionamento
Para ver o número e o estado de instâncias VM, ver uma lista de instâncias no seu dimensionamento definido com [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM). O estado indica se a instância VM aprovisionamento como aumenta horizontalmente de forma a escala definida automaticamente ou é desaprovisionamento como escala reduz horizontalmente automaticamente. O exemplo seguinte visualiza o estado de instância VM para o conjunto nomeado de dimensionamento *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático com base numa agenda
Os exemplos anteriores ampliada automaticamente uma escala definida no ou terminar com a métrica de anfitrião básica, tais como a utilização da CPU. Também pode criar regras de dimensionamento automático com base no agendas. Estas regras baseadas na agenda permitem-lhe automaticamente aumentar horizontalmente o número de instâncias VM à frente de um aumento previsto no pedido de aplicação, tais como as horas de núcleo de trabalho e, em seguida, dimensionar automaticamente o número de instâncias de cada vez que antecipa inferior pedido, tal como o fim de semana.

Para criar regras de dimensionamento automático com base nas métricas agenda em vez de anfitrião, utilize o portal do Azure. Atualmente não não possível criar regras baseadas na agenda com o Azure PowerShell.


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar regras de dimensionamento automático para aumentar horizontalmente e aumentar ou diminuir o *número* de instâncias VM no seu dimensionamento definido. Também pode reduzir verticalmente para aumentar ou diminuir a instância VM *tamanho*. Para obter mais informações, consulte [Vertical dimensionamento automático com conjuntos de dimensionamento da Máquina Virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerir as instâncias de VM, consulte [conjuntos de dimensionamento de máquina virtual de gerir com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando o dimensionamento automático regras acionador, consulte [utilize ações de dimensionamento automático para enviar correio eletrónico e webhook notificações de alertas no Monitor de Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Também pode [registos de auditoria de Utilize para enviar correio eletrónico e webhook notificações de alertas no Monitor de Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
