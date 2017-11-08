---
title: "Azure Service Fabric - com a extensão do Windows Azure Diagnostics de monitorização de desempenho | Microsoft Docs"
description: Utilize o Windows Azure Diagnostics para recolher os contadores de desempenho para os clusters de Service Fabric do Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Monitorização de desempenho com a extensão de diagnóstico do Windows Azure

Este documento aborda os passos necessários para configurar a recolha de contadores de desempenho através da extensão do Windows Azure Diagnostics (WAD) para os clusters do Windows. Para os clusters do Linux, configurar o [agente do OMS](service-fabric-diagnostics-oms-agent.md) para recolher contadores de desempenho para os nós. 

 > [!NOTE]
> A extensão WAD deve ser implementada no seu cluster para estes passos para resolver o problema. Se este não está configurado, aceda a [agregação de eventos e coleção utilizando o Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) e siga os passos no *implementar a extensão de diagnóstico*.

## <a name="collect-performance-counters-via-the-wadcfg"></a>Recolher contadores de desempenho através de WadCfg

Para recolher os contadores de desempenho através de WAD, terá de modificar a configuração correta no modelo de Gestor de recursos do cluster. Siga estes passos para adicionar um contador de desempenho que pretende recolher ao seu modelo e executar uma atualização de recursos do Resource Manager.

1. Localizar a configuração de WAD no modelo do seu cluster - localizar `WadCfg`. Irá adicionar os contadores de desempenho para recolher sob o `DiagnosticMonitorConfiguration`.

2. Definir a configuração para recolher contadores de desempenho adicionando a secção seguinte para sua `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    O `scheduledTransferPeriod` define como frquently os valores dos contadores que são recolhidos são transferidos para a tabela de armazenamento do Azure e qualquer configuradas sink. 

3. Adicionar os contadores de desempenho que pretende recolher o `PerformanceCounterConfiguration` que foi declarada no passo anterior. Cada contador que pretende recolher é definida com um `counterSpecifier`, `sampleRate`, `unit`, `annotation`e quaisquer relevantes `sinks`. Eis um exemplo de um contador de desempenho para o *Total de tempo do processador* (o período de tempo de CPU estava a ser utilizado para operações de processamento):

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
            {
                "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                "sampleRate": "PT1M",
                "unit": "Percent",
                "annotation": [
                ],
                "sinks": ""
            }
        ]
    }
    ```

    Pode ser modificada a frequência de amostragem para o contador de acordo com as suas necessidades. O formato para o mesmo é `PT<time><unit>`, por isso, se pretender que o contador recolhido a cada segundo, em seguida, deverá definir o `"sampleRate": "PT15S"`.

    Da mesma forma, pode recolher vários outros contadores de desempenho, adicionando-os à lista no `PerformanceCounterConfiguration`. Embora pode utilizar `*` para especificar os grupos de contadores de desempenho com o nome da mesma forma, enviar contadores através de um receptor (para o Application Insights) necessita que são declarados individualmente. 

4. Assim que tinha adicionada os contadores de desempenho adequadas que têm de ser recolhidos, terá de atualizar o recurso de cluster para que estas alterações são refletidas no seu cluster em execução. Guardar o modificado `template.json` e abra do PowerShell. Pode atualizar o seu cluster utilizando `New-AzureRmResourceGroupDeployment`. A chamada requer que o nome do grupo de recursos, o ficheiro de modelo atualizado e o ficheiro de parâmetros e pede ao Gestor de recursos para fazer alterações adequadas para os recursos que tenha atualizado. Assim que iniciou a sessão na sua conta e está a subscrição correta, utilize o seguinte comando para executar a atualização:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Após a conclusão da atualização disponibilizando (demora entre 15 45 minutos), WAD deve ser recolher os contadores de desempenho e enviando-as a uma tabela na conta de armazenamento declarada no `WadCfg`.

## <a name="next-steps"></a>Passos seguintes
* Consulte os contadores de desempenho no Application Insights ao adicionar o Application Insights sink para sua `WadCfg`. Leia o *adicionar o Sink de AI para o modelo do Resource Manager* secção [análise de eventos e visualização com o Application Insights ](service-fabric-diagnostics-event-analysis-appinsights.md) para configurar o sink do Application Insights.
* Recolha mais contadores de desempenho para o cluster. Consulte [métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md) para obter uma lista dos contadores deve a recolher.
* [Monitorização de utilização e diagnóstico com modelos de VM do Windows e o Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) mais efetuar modificações ao seu `WadCfg`, incluindo a configurar contas de armazenamento adicional para enviar dados de diagnóstico para.