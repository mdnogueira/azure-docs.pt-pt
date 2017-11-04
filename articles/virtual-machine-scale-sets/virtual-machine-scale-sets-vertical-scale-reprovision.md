---
title: "Aumentar verticalmente conjuntos de dimensionamento de máquina virtual do Azure | Microsoft Docs"
description: "Como aumentar verticalmente uma Máquina Virtual em resposta a alertas com a automatização do Azure de monitorização"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: madhana
editor: 
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: guybo
ms.openlocfilehash: 9159a5a9041864fe06785829121233379c46bb03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Dimensionamento automático vertical com conjuntos de dimensionamento da Máquina Virtual
Este artigo descreve como dimensionar verticalmente Azure [conjuntos de dimensionamento de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/) com ou sem reprovisioning. Dimensionamento de VMs que não estão a ser conjuntos de dimensionamento na vertical, consulte [aumentar verticalmente a máquina virtual do Azure com a automatização do Azure](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dimensionamento vertical, também conhecido como *aumentar verticalmente* e *reduzir verticalmente*, significa que aumente ou diminua tamanhos de máquina virtual (VM) em resposta a uma carga de trabalho. Comparar isto com [dimensionamento horizontal](virtual-machine-scale-sets-autoscale-overview.md), também referido como *aumentar horizontalmente* e *dimensionar de*, onde o número de VMs é alterado, consoante a carga de trabalho.

Reprovisioning significa remoção de uma VM existente e a respetiva substituição por um novo. Ao aumentar ou diminuir o tamanho das VMs no conjunto de dimensionamento da VM, em alguns casos que pretende redimensionar VMs existentes e manter os dados do utilizador, enquanto que noutros casos, tem de implementar novas VMs do tamanho do novo. Este documento abrange a ambos os casos.

Dimensionamento vertical pode ser útil quando:

* Um serviço incorporado em máquinas virtuais é em utilizados (por exemplo, a fim de semana). Reduzir o tamanho da VM pode reduzir os custos mensais.
* Aumentar o tamanho da VM para lidar com a maior pedido sem criar VMs adicionais.

Pode configurar o dimensionamento vertical ser accionadas com base numa métricas com base em alertas do seu conjunto de dimensionamento de VM. Quando o alerta é ativado desencadeado um webhook que um runbook que pode dimensionar o seu dimensionamento definido ou reduzir verticalmente de acionadores. Dimensionamento verticais pode ser configurado, seguindo estes passos:

1. Crie uma conta de automatização do Azure com capacidade de executar como.
2. Importe runbooks de escala Vertical de automatização do Azure para conjuntos de dimensionamento de VM para sua subscrição.
3. Adicione um webhook ao runbook.
4. Adicione um alerta para o conjunto de dimensionamento de VM através de uma notificação de webhook.

> [!NOTE]
> Vertical dimensionamento automático pode apenas ocorrer dentro de determinados intervalos de tamanhos de VM. Comparar as especificações de tamanho de cada antes de decidir ao dimensionar a partir de um para outro (número mais alto não sempre indicar maior tamanho da VM). Pode optar por aumentar entre os pares de tamanhos seguintes:
> 
> | Dimensionamento par de tamanhos de VM |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Criar uma conta de automatização do Azure com capacidade de executar como
A primeira coisa que precisa de fazer é criar uma conta de automatização do Azure que irá alojar os runbooks utilizados para dimensionar as instâncias do conjunto de dimensionamento de VM. Recentemente [da automatização do Azure](https://azure.microsoft.com/services/automation/) introduziu a funcionalidade de "Conta Run As" que faz com que a definição de segurança de Principal de serviço para executar automaticamente os runbooks em nome de um utilizador muito fácil. Pode ler mais sobre este artigo abaixo:

* [Autenticar Runbooks com a conta Run As do Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar runbooks de escala Vertical de automatização do Azure para a sua subscrição
Os runbooks necessários para aumentar verticalmente a conjuntos de dimensionamento da VM já são publicados na Galeria de Runbook da automatização do Azure. Para importá-los para a sua subscrição siga os passos neste artigo:

* [Galleries módulos e Runbooks de automatização do Azure](../automation/automation-runbook-gallery.md)

Escolha a opção de procurar galeria no menu de Runbooks:

![Runbooks importados][runbooks]

Os runbooks que precisam de ser importados são apresentados. Selecione o runbook com base em se pretende que o dimensionamento, com ou sem reprovisioning vertical:

![Galeria de Runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao runbook
Assim que tiver de importar os runbooks que terá de adicionar um webhook ao runbook para que pode ser acionada por um alerta de um conjunto de dimensionamento de VM. Os detalhes de criar um webhook de Runbook são descritos neste artigo:

* [Webhooks de automatização do Azure](../automation/automation-webhooks.md)

> [!NOTE]
> Certifique-se de que copia o webhook URI antes de fechar a caixa de diálogo de webhook como irá precisar na secção seguinte.
> 
> 

## <a name="add-an-alert-to-your-vm-scale-set"></a>Adicionar um alerta para o conjunto de dimensionamento de VM
Segue-se um script do PowerShell que mostra como adicionar um alerta para um conjunto de dimensionamento de VM. Consulte o artigo seguinte para obter o nome da métrica a acionar o alerta em: [métricas comuns de dimensionamento automático de Monitor de Azure](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Recomenda-se para configurar uma janela de tempo razoável para o alerta para evitar a acionar dimensionamento vertical e qualquer interrupção do serviço associado, demasiadas vezes. Considere uma janela de, pelo menos, 20 a 30 minutos ou mais. Considere se precisar de evitar qualquer interrupção de dimensionamento horizontal.
> 
> 

Para obter mais informações sobre como criar alertas, consulte os artigos seguintes:

* [Exemplos de início rápido do Azure PowerShell de Monitor](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Exemplos de início rápido do Azure CLI de várias plataformas de Monitor](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Resumo
Este artigo mostrou exemplos simples de dimensionamento verticais. Com estas blocos modulares - conta de automatização, runbooks, webhooks, alertas - pode ligar uma variedade de avançados de eventos com um conjunto de ações personalizada.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
