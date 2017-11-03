---
title: "Azure métricas comuns para o Monitor de dimensionamento automático | Microsoft Docs"
description: "Saiba quais métricas são frequentemente utilizadas para efetuar o dimensionamento automático a serviços em nuvem, as máquinas virtuais e aplicações Web."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 189b2a13-01c8-4aca-afd5-90711903ca59
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: ancav
ms.openlocfilehash: 240a230d09680672ccd5316470a87d047fab9fd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure métricas comuns de dimensionamento automático Monitor
Dimensionamento automático de Monitor do Azure permite-lhe aumentar o número de instâncias em execução ou descendente, com base nos dados de telemetria (métricas). Este documento descreve as métricas comuns que poderá querer utilizar. No portal do Azure para serviços em nuvem e de Farms de servidores, pode escolher a métrica do recurso para dimensionar por. No entanto, também pode escolher qualquer métrica de um recurso Dimensionar por diferentes.

Dimensionamento automático de Monitor do Azure aplicam-se apenas ao [conjuntos de dimensionamento de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços em nuvem](https://azure.microsoft.com/services/cloud-services/), e [Web Apps do App Service -](https://azure.microsoft.com/services/app-service/web/). Outros serviços do Azure utilizam diferentes métodos de dimensionamento.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Métricas de computação para as VMs baseadas no Resource Manager
Por predefinição, as máquinas virtuais baseadas no Resource Manager e os conjuntos de dimensionamento de Máquina Virtual emitir básicas métricas de (ao nível do anfitrião). Além disso, quando configurar a recolha de dados de diagnóstico para uma VM do Azure e VMSS, a extensão de diagnóstico do Azure emite também os contadores de desempenho de SO convidado (geralmente conhecidos como "Métricas de SO convidado").  Todas estas métricas, utilizar nas regras de dimensionamento automático.

Pode utilizar o `Get MetricDefinitions` PoSH/API/CLI para ver as métricas disponíveis para o seu recurso VMSS.

Se estiver a utilizar conjuntos de dimensionamento VM e não vir uma métrica específica listada, em seguida, é provável *desativada* na sua extensão de diagnóstico.

Se um determinado métrica não está a ser amostragem ou ser transferidos com a frequência quiser, pode atualizar a configuração de diagnóstico.

Se ambos os casos anterior for VERDADEIRO, em seguida, revê [utilize o PowerShell para ativar o diagnóstico do Azure numa máquina virtual com o Windows](../virtual-machines/windows/ps-extensions-diagnostics.md) sobre o PowerShell para configurar e atualizar a sua extensão de diagnósticos de VM do Azure para ativar a métrica. Este artigo também inclui um ficheiro de configuração de diagnósticos de exemplo.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Métricas de anfitrião para o Windows baseado no Resource Manager e VMs com Linux
As métricas de nível do anfitrião seguintes são emitidas por predefinição para a VM do Azure e VMSS em instâncias de Windows e Linux. Estas métricas descrevem a VM do Azure, mas são recolhidas a partir do anfitrião de VM do Azure em vez de através do agente instaladas na VM do convidado. Pode utilizar estas métricas nas regras de dimensionamento automático.

- [Métricas de anfitrião para o Windows baseado no Resource Manager e VMs com Linux](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Métricas de anfitrião para o Windows baseado no Resource Manager e conjuntos de dimensionamento de VM do Linux](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Métricas de SO convidado VMs do Windows baseados no Resource Manager
Quando cria uma VM no Azure, diagnóstico é ativado ao utilizar a extensão de diagnóstico. A extensão de diagnóstico emite um conjunto de métricas retirados do dentro da VM. Isto significa que pode o dimensionamento automático retire métricas que não são emitidos por predefinição.

Pode gerar uma lista das métricas utilizando o seguinte comando do PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode criar um alerta para as métricas seguintes:

| Nome da métrica | Unidade |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percentagem |
| \Processor(_Total)\% de tempo privilegiado |Percentagem |
| \Processor(_Total)\% de tempo do utilizador |Percentagem |
| Frequência de \Processor \Processor informações ( total) |Contagem |
| \System\Processes |Contagem |
| Contagem de \Thread \Process ( total) |Contagem |
| Contagem de \Handle \Process ( total) |Contagem |
| \Memory\% dos Bytes consolidados em utilização |Percentagem |
| \Memory\Available Bytes |Bytes |
| Bytes de \Memory\Committed |Bytes |
| Limite de \Memory\Commit |Bytes |
| Bytes de \Memory\Pool bloco paginado |Bytes |
| \Memory\Pool Bytes na memória não paginável |Bytes |
| \PhysicalDisk(_Total)\% tempo do disco |Percentagem |
| \PhysicalDisk(_Total)\% tempo de leitura de discos |Percentagem |
| \PhysicalDisk(_Total)\% tempo escrita em disco |Percentagem |
| \PhysicalDisk ( total) \Disk transferências/seg |CountPerSecond |
| \PhysicalDisk ( total) \Disk leituras/seg |CountPerSecond |
| \PhysicalDisk ( total) \Disk escritas/seg |CountPerSecond |
| \PhysicalDisk ( total) \Disk bytes/seg |BytesPerSecond |
| \Disk \PhysicalDisk ( total) Bytes lidos/seg |BytesPerSecond |
| Escrita de \Disk \PhysicalDisk ( total) Bytes/seg |BytesPerSecond |
| \Avg \PhysicalDisk ( total). Comprimento da fila de disco |Contagem |
| \Avg \PhysicalDisk ( total). Comprimento de fila de leitura do disco |Contagem |
| \Avg \PhysicalDisk ( total). Comprimento de fila de escrita de disco |Contagem |
| \LogicalDisk(_Total)\% espaço livre |Percentagem |
| \LogicalDisk ( total) \Free megabytes |Contagem |

### <a name="guest-os-metrics-linux-vms"></a>Métricas de SO convidado VMs com Linux
Quando cria uma VM no Azure, diagnostics está ativada por predefinição, utilizando a extensão de diagnóstico.

Pode gerar uma lista das métricas utilizando o seguinte comando do PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Pode criar um alerta para as métricas seguintes:

| Nome da métrica | Unidade |
| --- | --- |
| \Memory\AvailableMemory |Bytes |
| \Memory\PercentAvailableMemory |Percentagem |
| \Memory\UsedMemory |Bytes |
| \Memory\PercentUsedMemory |Percentagem |
| \Memory\PercentUsedByCache |Percentagem |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bytes |
| \Memory\PercentAvailableSwap |Percentagem |
| \Memory\UsedSwap |Bytes |
| \Memory\PercentUsedSwap |Percentagem |
| \Processor\PercentIdleTime |Percentagem |
| \Processor\PercentUserTime |Percentagem |
| \Processor\PercentNiceTime |Percentagem |
| \Processor\PercentPrivilegedTime |Percentagem |
| \Processor\PercentInterruptTime |Percentagem |
| \Processor\PercentDPCTime |Percentagem |
| \Processor\PercentProcessorTime |Percentagem |
| \Processor\PercentIOWaitTime |Percentagem |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Segundos |
| \PhysicalDisk\AverageWriteTime |Segundos |
| \PhysicalDisk\AverageTransferTime |Segundos |
| \PhysicalDisk\AverageDiskQueueLength |Contagem |
| \NetworkInterface\BytesTransmitted |Bytes |
| \NetworkInterface\BytesReceived |Bytes |
| \NetworkInterface\PacketsTransmitted |Contagem |
| \NetworkInterface\PacketsReceived |Contagem |
| \NetworkInterface\BytesTotal |Bytes |
| \NetworkInterface\TotalRxErrors |Contagem |
| \NetworkInterface\TotalTxErrors |Contagem |
| \NetworkInterface\TotalCollisions |Contagem |

## <a name="commonly-used-web-server-farm-metrics"></a>Métricas frequentemente utilizadas de Web (Farm de servidores)
Também pode efetuar o dimensionamento automático com base nas métricas do servidor web comuns, tais como o comprimento da fila Http. Nome métrico está **HttpQueueLength**.  A secção seguinte apresenta uma lista de métricas de farm (aplicações Web) do servidor disponível.

### <a name="web-apps-metrics"></a>Métricas de aplicações Web
Pode gerar uma lista das métricas de aplicações Web utilizando o seguinte comando do PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode alerta sobre ou dimensionar por estas métricas.

| Nome da métrica | Unidade |
| --- | --- |
| CpuPercentage |Percentagem |
| MemoryPercentage |Percentagem |
| DiskQueueLength |Contagem |
| HttpQueueLength |Contagem |
| BytesReceived |Bytes |
| BytesSent |Bytes |

## <a name="commonly-used-storage-metrics"></a>Métricas frequentemente utilizadas de armazenamento
Pode dimensionar ao comprimento da fila de armazenamento, que é o número de mensagens na fila de armazenamento. Comprimento da fila de armazenamento é uma métrica especial e o limiar é o número de mensagens em fila por instância. Por exemplo, se existirem duas instâncias, e se o limiar é definido para 100, dimensionamento ocorre quando o número total de mensagens na fila 200. Que pode ser 100 mensagens por instância, 120 e 80 ou qualquer combinação que adiciona até 200 ou mais.

Configurar esta definição no portal do Azure no **definições** painel. Para conjuntos de dimensionamento VM, pode atualizar a definição de dimensionamento automático no modelo do Resource Manager para utilizar *metricName* como *ApproximateMessageCount* e transmita o ID da fila de armazenamento como  *metricResourceUri*.

Por exemplo, com uma conta de armazenamento clássico metricTrigger de definição de dimensionamento automático incluem:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Para uma conta de armazenamento (não clássica), o metricTrigger incluem:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Métricas frequentemente utilizadas do Service Bus
Pode dimensionar ao comprimento da fila de barramento de serviço, que é o número de mensagens na fila de barramento de serviço. Comprimento da fila de barramento de serviço é uma métrica especial e o limiar é o número de mensagens em fila por instância. Por exemplo, se existirem duas instâncias, e se o limiar é definido para 100, dimensionamento ocorre quando o número total de mensagens na fila 200. Que pode ser 100 mensagens por instância, 120 e 80 ou qualquer combinação que adiciona até 200 ou mais.

Para conjuntos de dimensionamento VM, pode atualizar a definição de dimensionamento automático no modelo do Resource Manager para utilizar *metricName* como *ApproximateMessageCount* e transmita o ID da fila de armazenamento como  *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Para o Service Bus, o conceito de grupo de recursos existe mas o Gestor de recursos do Azure cria um grupo de recursos predefinidos por região. O grupo de recursos é normalmente no formato 'Default - ServiceBus-[Região]'. Por exemplo, 'Predefinição-ServiceBus-EastUS', 'Predefinição-ServiceBus-WestUS', 'Predefinição-ServiceBus-AustraliaEast' etc.
>
>
