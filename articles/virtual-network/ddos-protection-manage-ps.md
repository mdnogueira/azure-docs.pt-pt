---
title: "Gerir com o Azure PowerShell do Azure DDoS proteção padrão | Microsoft Docs"
description: "Saiba como gerir com o Azure PowerShell do Azure DDoS proteção padrão."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: baac97db61b84000557e7150a64ffb64d81ce00c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Gerir com o Azure PowerShell do Azure DDoS proteção padrão

Saiba como ativar e desativar distribuída recusa de protection service (DDoS distribuídos) e utilizar a telemetria de correção para atenuar um ataque DDoS com o Azure DDoS proteção Standard. Padrão de proteção DDoS protege os recursos do Azure como máquinas virtuais, balanceadores de carga e gateways de aplicação com um Azure [endereço IP público](virtual-network-public-ip-address.md) atribuída. Para saber mais sobre DDoS proteção padrão e das respetivas funções, consulte o artigo [descrição geral do padrão de proteção DDoS](ddos-protection-overview.md). 

>[!IMPORTANT]
>Azure DDoS proteção Standard (proteção DDoS) está atualmente em pré-visualização. Proteção DDoS de suportar um número limitado de recursos do Azure e está disponível apenas num número selecionado de regiões. Para obter uma lista de regiões disponíveis, consulte [descrição geral do padrão de proteção DDoS](ddos-protection-overview.md). Terá de [registar-se para o serviço](http://aka.ms/ddosprotection) durante a pré-visualização limitada para obter proteção DDoS ativada para a sua subscrição. Depois de registar, são contactados pela equipa do Azure DDoS que irá guiá-lo durante o processo de ativação.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Se precisar de instalar ou atualizar o Azure PowerShell, consulte [módulo Azure PowerShell instalar](/powershell/azure/install-azurerm-ps).

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Ativar o padrão de proteção DDoS - nova rede virtual

Para criar uma rede virtual com proteção DDoS ativada, execute o seguinte exemplo:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

Este exemplo cria uma rede virtual com duas sub-redes e dois servidores DNS. O efeito de especificar os servidores DNS na rede virtual é que as NICs/VMs que são implementadas para esta rede virtual herda estes servidores DNS como predefinições. Proteção DDoS está ativada para todos os recursos protegidos na rede virtual.

> [!WARNING]
> Quando seleciona uma região, escolha uma região suportada na lista na [descrição geral do Azure DDoS proteção padrão](ddos-protection-overview.md).

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Ativar proteção DDoS numa rede virtual existente

Para ativar proteção DDoS uma rede virtual existente, execute o seguinte exemplo:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> A rede virtual tem de existir numa região suportada. Para obter uma lista de regiões suportadas, consulte [descrição geral do Azure DDoS proteção padrão](ddos-protection-overview.md).

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Desativar proteção DDoS numa rede virtual

Para desativar proteção DDoS numa rede virtual, execute o seguinte exemplo:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>Rever o estado de proteção DDoS de uma rede virtual 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Utilize a telemetria de proteção DDoS

Um ataque a telemetria é fornecida por meio do Monitor do Azure em tempo real. A telemetria está disponível apenas para a duração que é um endereço IP público em mitigação. Não verá telemetria antes ou depois de um ataque é atenuado.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Configure alertas nas métricas de proteção DDoS

Tirar partido da configuração de alerta do Monitor do Azure, pode selecionar qualquer uma das métricas de proteção DDoS disponíveis para o alertar quando há uma mitigação ativa durante um ataque.

#### <a name="configure-email-alert-rules-via-azure"></a>Configurar regras de alertas de e-mail através do Azure

1. Obter uma lista das subscrições que tem disponíveis. Certifique-se de que estão a funcionar com a subscrição correta. Caso contrário, defina-o à direita com o resultado de Get-AzureRmSubscription. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. Para listar as regras existentes num grupo de recursos, utilize o seguinte comando: 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. Para criar uma regra, tem de ter primeiro as seguintes informações: 

    - O ID de recurso para o recurso que pretende definir um alerta para.
    - As definições de métricas disponíveis para esse recurso. Uma forma de obter o ID de recurso é utilizar o portal do Azure. Pressupondo que o recurso já está a ser criado, selecione-o no portal do Azure. Em seguida, na página seguinte, selecione *propriedades* sob o *definições* secção. O **ID de recurso** é um campo na página seguinte. Outra forma consiste em utilizar o [Explorador de recursos do Azure](https://resources.azure.com/). Um exemplo de ID de recurso para um IP público é:`/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    O exemplo seguinte cria um alerta para um endereço IP público associado a um recurso numa rede virtual. A métrica para criar um alerta é **ataques de DDoS em ou não**. Este é um valor booleano 1 ou 0. A **1** significa que está sob ataque. A **0** significa que não está sob ataque. O alerta é criado quando um ataque iniciada últimos 5 minutos.

    Para criar um webhook ou enviar o e-mail quando é criado um alerta, crie primeiro o e-mail e/ou o webhook. Depois de criar o webhook ou e-mail, de imediato a criar a regra com o `-Actions` tag, conforme mostrado no exemplo seguinte. Não é possível associar o webhook ou e-mails com as regras existentes com o PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. Certifique-se de que o alerta criado corretamente ao observar a regra:

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

Também pode saber mais sobre [configurar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [as logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) para a criação de alertas.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurar o registo nas métricas de proteção DDoS

Consulte o [PowerShell rápido iniciar amostras](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ajudar a aceder e configurar o diagnóstico de registo através do PowerShell do Azure.

## <a name="next-steps"></a>Passos seguintes

- [Leia mais sobre os registos de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analisar os registos do armazenamento do Azure com a análise de registos](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)